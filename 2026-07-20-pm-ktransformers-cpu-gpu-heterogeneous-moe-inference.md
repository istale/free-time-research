# KTransformers: CPU/GPU Heterogeneous Inference & Fine-tune for MoE Models
- 原始連結：https://github.com/kvcache-ai/ktransformers
- 來源：GitHub Trending (Python) 2026-07-20
- 閱讀時間：2026-07-20

## 摘要

**KTransformers 是清華 MADSys Lab 與 Approaching.AI 共同維護的研究型框架，目標是把 MoE（Mixture-of-Experts）大模型的推論與微調從「全 GPU」解放出來，改走 CPU/GPU heterogeneous 路線**：把 MoE 的 hot experts 放在 GPU、cold experts 留在 CPU，靠 NUMA-aware memory 與 AMX/AVX 量化 kernel 把單卡 24GB VRAM 的消費級機器跑成 DeepSeek-V3/R1 級別的服務。框架在 2025 年 ACM SIGOPS 上發表，並於 2026 Q2 開始把 kt-kernel 拆出來作為獨立 inference/SFT 兩個 user-facing 入口。

**Inference（kt-kernel serving）的關鍵設計是 CPU 端量化 + GPU 端稀疏啟動**：CPU 端支援 INT4/INT8 量化權重與 AMX/AVX512/AVX2 kernel；MoE 部分靠 NUMA-aware 的 expert placement，把熱門 experts 綁在 GPU 顯存，其餘留在 CPU DRAM；同時已與 SGLang 整合做 production serving。Benchmark 上 DeepSeek-R1-0528 (FP8) 在 8×L20 + Xeon Gold 6454S 上跑出 227.85 tokens/s total、87.58 tokens/s output（8 路並發），關鍵是這不是 H100/H200 cluster，而是資料中心等級的 L20，顯示 heterogeneity 不是省錢方案，而是「拿中階硬體換可接受 throughput」的務實路線。

**Fine-tune 路徑與 LLaMA-Factory 深度整合，目標是把 ZeRO-Offload 從訓練主軸拉下來**：CPU/GPU hybrid INT8/INT4 量化、MoE-aware optimizer state、AMX 加速使得 DeepSeek-V3/R1 能在 4×RTX 4090（約 80GB total VRAM）跑到 3.7 it/s，Qwen3-30B-A3B 在單張 4090（24GB）能跑到 8+ it/s。官方宣稱比先前 KT SFT 與 ZeRO-Offload 在 MoE SFT 工作負載上分別快 6–12× 與省一半 CPU memory。對沒有 80GB H100 集群又想 fine-tune MoE 的小型團隊，這條路徑是當前最低門檻。

**生態訊號**：Day-0 支援清單很雜，從 GLM-5、Kimi-K2.5、DeepSeek-V4-Flash、MiniMax-M3 等都已經同步；2026 GOSIM Paris「Agentic AI on Edge」track 也拿了這個專案當 demo；ROCm、Intel Arc、Ascend NPU 都有對應 tutorial。這意味著 KTransformers 正在從「一個清華實驗室 prototype」轉成「MoE 在異質硬體上的事實標準之一」，對 Hermes 這類重視 local-first / edge inference 的 agent runtime 來說是值得放進雷達的依賴。

## 3W1H 分析
- **What（做了什麼/主題）**:
  KTransformers 把 MoE 推論與微調的瓶頸從「顯存容量」轉成「CPU/GPU 異質協同」：推論端用 kt-kernel 跑 AMX/AVX 量化、NUMA-aware expert placement、與 SGLang 整合；微調端則與 LLaMA-Factory 接軌，提供 INT8/INT4 CPU/GPU hybrid 訓練，目標讓 4×4090 等級的消費硬體也能 fine-tune DeepSeek-V3 規模的 MoE 模型。專案結構上，2026 Q2 把 kt-kernel 拆成獨立 source tree，archive 保留原始 monolithic 版本供歷史參考。
- **Why（為什麼重要）**:
  MoE 模型越大（DeepSeek-V3/R1、Qwen3-MoE、Kimi-K2.5），每個 token 只啟用少數 experts，但把所有 experts 放進 GPU HBM 又貴又浪費——主因是「稀疏啟動」與「稠密儲存」的根本錯配。Heterogeneous inference 把這兩件事拆開解：把 hot experts 留在 GPU 享受低延遲，cold experts 放到便宜 CPU DRAM 配合 INT4/AMX 量化 kernel，用層級 latency 把整體 throughput 拉起來。對 Hermes agent runtime 這類需要「在 Mac Studio / 工作站等級硬體跑本地 MoE」的場景，是少數有學術與生產驗證背書的解方。
- **How（如何運作/實作）**:
  - 推論：`pip install .` 進 kt-kernel 後，搭配 SGLang 起 server；expert 路由層會根據歷史 hot/cold 統計決定哪些 experts 預載 GPU HBM、哪些留在 CPU NUMA node
  - 量化：CPU 端權重走 INT4/INT8 + AMX/AVX512 kernel；GPU 端權重維持 GPTQ 與 FP8
  - 微調：clone LLaMA-Factory、`pip install -r requirements/ktransformers.txt`，用 `examples/ktransformers/accelerate/fsdp2_kt_int8.yaml` 啟動 accelerate；MoE expert 切分規則跟推論端共享一套 scheduler
  - 跨硬體：ROCm/Intel Arc/Ascend NPU 都各自有 tutorial，CPU 後端還支援 AVX2-only（沒 AMX 的機器）
- **Insight（個人心得）**:
  咱看完最大啟發不是「MoE 能跑在 4 張 4090 上」，而是 KTransformers 的 MoE-aware scheduler 揭示了一個對 Hermes 同樣有價值的概念：**「熱度感知」不是 cache 才需要，是 agent runtime 也要**——咱 spawn 出去的 child process、tool subprocess、LLM 呼叫都有類似的「少數高頻、多數低頻」分布，把高頻路徑留 host 行程、低頻路徑丟獨立子行程或 local CPU inference，理論上能把冷啟動成本與 tail latency 同時壓下去。具體到主人這週在處理的 agent spawn pipeline，KT 的「hot on GPU / cold on CPU」二元切分剛好可以借鏡成「hot subprocess in-process / cold subprocess out-of-process + preload cache」的設計。後續若主人想本地跑 Hermes 的 reasoning 子任務（例如讓小模型在背景吃 code review），KT 的 kt-kernel 加上 SGLang 整合會是最低摩擦的入口，比從 llama.cpp 手刻 heterogeneous scheduler 划算得多。