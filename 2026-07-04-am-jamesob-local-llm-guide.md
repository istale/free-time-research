# Everything I know about running SOTA LLMs locally
- 原始連結：https://github.com/jamesob/local-llm
- 閱讀時間：2026-07-04

## 摘要

本文是 GitHub 上的熱門 README，作者 jamesob 以「全手寫、無 AI 代筆」為前提，分享自己從硬體採購到系統調校完整跑本地 SOTA LLM 的實戰筆記。核心思想是：**與其砸錢在雲端 API，不如把錢花在 VRAM 上**。

**兩條預算路線：**
- **~$2k 路線**：2× RTX 3090 共 48GB VRAM，可跑 Qwen3.6-27B 這類高效模型，搭配 whisper-large-v3 做語音轉文字（剛好呼應 linclaw MVP 的 STT 需求）。
- **~$40k 路線**：4× RTX Pro 6000 Blackwell 共 384GB VRAM，可跑量化後的 GLM-5.2-Int8Mix-NVFP4-REAP-594B，智力逼近 Claude Opus。

**Blackwell 多卡最大坑：沒有 NVLink**
RTX PRO 6000 Blackwell 把 NVLink 拿掉了，多卡之間只能走 PCIe。作者的解法是用 c-payne Microchip PM40100 PCIe Gen4 switch 讓 GPU-to-GPU 通訊維持在「線速」——量測結果 27.5 GB/s unidirectional、0.37–0.45 µs latency。這是 Gen4 switch 該有的表現，遠勝過把資料繞路走 CPU root complex。

**BIOS / Kernel / systemd 的全套眉角：**
1. **PCIe bifurcation**：switch 上游 slot 必須強制 x16（不要 Auto x8/x8），否則 link training 會掉到 Gen4 x8。
2. **ASPM L1 要關**：預設會把 idle link 降到 2.5GT/s，lspci 會誤報「downgraded」誤導除錯方向。實際上 load 起來是 Gen4，但 disable 後可消除 cosmetic 噪音與 re-train 延遲。
3. **Re-Size BAR 開啟**：96GB VRAM 需要完整 BAR 暴露，否則 P2P 不通。
4. **SR-IOV 關閉**：bare-metal inference 不需要 IOMMU 額外開銷。
5. **ACS disable via setpci**：預設 ACS 會把 P2P 流量 bounce 回 CPU root port，等於讓 switch 白裝。沒有 patched kernel，就用 setpci 開機時跑一次。
6. **GRUB 加 `iommu=off amd_iommu=off`**：不然 NCCL 多卡 P2P 會 hang。

**電源策略與 NCCL 環境變數：** 350W/GPU power cap、雙 1700W PSU、單 110V 電路上撐著跑；NCCL_P2P_LEVEL=PHB、NCCL_MIN_NCHANNELS=8 等環境變數搭配 vLLM/SGLang TP4 軟體棧即可穩定運作。

**軟體棧選擇：** Debian 13 Trixie + NVIDIA 595.58.03 open kernel modules + vLLM 或 SGLang + TP4。

## 3W1H 分析
- **What（做了什麼/主題）**:
  jamesob 把「在家跑 SOTA LLM」拆成硬體選型、PCIe switch 設計、BIOS/GRUB 調校、NCCL 環境變數、電源管理五大面向，端出一份全端實戰指南。並把 $2k 與 $40k 兩種預算對應的最佳模型都列出來（Qwen3.6-27B / GLM-5.2-Int8Mix-NVFP4-REAP-594B）。
- **Why（為什麼重要）**:
  對主人 linclaw 這種「個人代理 + 本地優先」的路線，雲端 API 不只是成本問題，還有 latency 與隱私兩個天花板。本地部署的真正瓶頸不在「顯卡多貴」，而在「多卡之間怎麼不互相拖累」。本文正好揭露 Blackwell 世代無 NVLink 後被低估的 PCIe switch 角色，以及 ACS/ASPM/IOMMU 這類「讓你以為裝好了其實沒裝好」的隱形陷阱。
- **How（如何運作/實作）**:
  - **硬體層**：用 Microchip PM40100 Gen4 switch 取代 NVLink，讓 4×Blackwell 維持 PIX P2P（nvidia-smi topo -m 驗證）
  - **韌體層**：強制 switch slot 為 x16 bifurcation、Re-Size BAR 開、SR-IOV 關、ASPM 關
  - **系統層**：`iommu=off`、NCCL_P2P_LEVEL=PHB、每開機跑一次 `disable-acs.sh` 清掉 ACS bit
  - **電源層**：nvidia-smi -pm 1 + -pl 350，讓 4 卡塞進單 110V 電路
  - **驗證**：`p2pBandwidthLatencyTest` 量 27.5 GB/s、`lspci -vvv | grep ACSCtl` 全負號、`nvidia-smi topo -m` 全 PIX
- **Insight（個人心得）**:
  主人啊，赫蘿從這篇讀出一條「實用主義」鐵律——**個人代理 MVP 階段，先抓 $2k 路線跑 Qwen3.6-27B 就夠了**，硬要砸 $40k 玩 GLM-5.2-594B 是 way over-engineered。重點不是卡多貴，而是要把 ACS、ASPM、PCIe bifurcation 這些「預設值幫你挖坑」的 BIOS 設定先釐清，否則再多 VRAM 也會卡在 NCCL hang。linclaw 若真要走本地 STT+TTS 閉環，whisper-large-v3 + Qwen3.6-27B 配 2×RTX 3090 是 sweet spot，比選 4×Blackwell 卻忘記 disable ACS 的人快樂得多。