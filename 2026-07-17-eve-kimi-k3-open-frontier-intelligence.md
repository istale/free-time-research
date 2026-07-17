# Kimi K3: Open Frontier Intelligence
- 原始連結：https://www.kimi.com/blog/kimi-k3
- 閱讀時間：2026-07-17（晚）
- 來源：Moonshot AI Research Blog（2026-07-17 出刊，Kimi 官方技術部落格）
- 發布時間：2026-07-17

## 摘要

Moonshot AI 正式釋出 Kimi K3 — 首個開源 3T 等級的 frontier 模型，總參數 2.8 兆，內建原生視覺、1M token context，以「Delta Attention + Attention Residuals」為新架構骨幹。本文是 Kimi 官方的 release + 技術綜述。

**模型規模與新架構**
- **2.8T 總參數 / 16 active experts of 896（Stable LatentMoE）**：採用 Mixture of Experts 高度稀疏設計，運行時僅激活 16/896 個 expert，比前代 K2 提升約 2.5 倍 scaling efficiency。
- **KDA（Kimi Delta Attention）**：改良 attention 機制以更有效處理長序列；KDA 對傳統 prefix caching 提出新挑戰，Kimi 已貢獻對應實作至 vLLM 社群。
- **AttnRes（Attention Residuals）**：跨深度選擇性檢索 representations，避免 uniform accumulation — 為 trillion-param regime 的穩定訓練提供骨幹。
- **Quantile Balancing**：直接從 router-score 推導 expert 配置，消除 heuristic updates 與敏感平衡超參數。
- **Per-Head Muon**：把 Muon optimizer 擴展為各 attention head 獨立優化，提供更適應的 scale-time learning。
- **SiTU（Sigmoid Tanh Unit）+ Gated MLA**：改善 activation control 與 attention selectivity。

**量化與部署**
- 採用 **MXFP4 weight + MXFP8 activation** 的 quantization-aware training，從 SFT 階段就啟用，兼顧硬體相容性與效率。
- 推薦部署在 **64+ accelerator supernode 設定**，因為 inference 效率隨高頻寬通訊域擴大而提升。
- 透過 Mooncake 的 disaggregated inference architecture，官方 API 在 coding 工作負載上達成 **90%+ cache hit rate**。

**長時程代理表現（long-horizon agentic coding）**
- Kernel optimization：在 24 小時 sandbox 內自主 profile、rewrite、benchmark 四個 GPU kernel 任務（AttnRes、KDA、512-head-dimension MLA 等），表現與 Fable 5（with fallback）相當，大幅超越 Opus 4.8、GPT 5.6 Sol、GPT 5.5。
- GPU Compiler：從零建構 Triton-like compiler **MiniTriton**（自製 tile-level IR over MLIR、優化 passes、PTX codegen），benchmark 與 Triton/torch.compile 持平甚至更佳，並能 end-to-end nanoGPT 訓練且 loss curve 對齊 reference。
- Chip design：在 48 小時自主運行中，使用 open-source EDA 工具在 Nangate 45nm library 設計一顆 nano model 用的晶片，4 mm² 內 closure timing at 100 MHz，模擬解碼 throughput 超過 8,700 tokens/s。
- 跨領域研究：2 小時內完成「I-Love-Q universal relations in computational astrophysics」— 過去需資深研究員 1-2 週，自動 cross-validate 20+ papers、生成 3,000+ 行 Python、產出互動式 HTML dashboard。

**基準對比（節錄核心指標）**

| Benchmark | K3 | Fable 5 | GPT 5.6 Sol | Opus 4.8 |
|---|---|---|---|---|
| Terminal Bench 2.1 | 88.3 | 84.6 | 88.8 | 84.6 |
| DeepSWE | 67.5 | 70.0 | 73.0 | 59.0 |
| Program Bench | 77.8 | 76.8 | 77.6 | 71.9 |
| SWE Marathon | 42.0 | 35.0 | 39.0 | 40.0 |
| BrowseComp | 91.2 | 88.0 | 90.4 | 84.3 |
| Toolathlon-Verified | 73.2 | 77.9 | 74.9 | 76.2 |

**定價與可取得性**
- API 價格：cache-hit input $0.30/MTok、cache-miss input $3.00/MTok、output $15.00/MTok。
- 完整 model weights 預計 **2026-07-27** 釋出。
- 同步提供 Kimi Work（桌面應用）、Kimi Code（CLI）、Kimi Enterprise 版本。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Moonshot AI 推出 2.8T 參數的開源 frontier 模型 Kimi K3，採用全新的 KDA + AttnRes + Stable LatentMoE 架構組合，搭配 MXFP4/MXFP8 quantization-aware training 與 Mooncake disaggregated inference stack，並展示從 GPU kernel optimization、compiler 建構到 chip design 的長時程 agentic coding 能力。
- **Why（為什麼重要）**:
  開源模型首次踏入 3T 等級，KDA + AttnRes 對 attention 與 residual connection 提出結構性新解（不是純 scaling，也不是 LoRA/quantization 這類已成熟的優化手段）。K3 在 Terminal Bench 2.1 與 Fable 5、GPT 5.6 Sol 並駕齊驅（差距 < 0.5pt），但 Mooncake 90%+ cache hit + $3/MTok cache-miss pricing 對企業級部署具實際吸引力。更深層意義：對 LMStudio / 本地推理部署者而言，KDA 對 vLLM prefix caching 的新要求代表「開源 frontier 模型」與「本地部署」的相容性不再是理所當然，必須仰賴上游 vLLM 同步補丁。
- **How（如何運作/實作）**:
  - **Stable LatentMoE + Quantile Balancing**：用 router-score quantiles 直接分配 expert capacity，消除啟發式 balancer — 讓 16/896 極高稀疏度可穩定訓練
  - **KDA + AttnRes 雙軸**：attention 層負責長序列資訊流（KDA），residual 層負責跨深度 selective retrieval（AttnRes），兩者互補解開 trillion-param scaling 的訓練不穩定
  - **量化策略**：SFT 階段即啟用 MXFP4 weight / MXFP8 activation，並透過 quantization-aware training 保留能力
  - **Inference**：Mooncake disaggregated inference 拆 prefill / decode 到不同硬體池，搭配 supernode（64+ accelerator）部署與 KDA 專屬 prefix cache 補丁（已貢獻 vLLM）
  - **Agentic 行為**：模型本身沒有顯式的 agent framework — K3 的長時程表現依賴 Kimi Code / Kimi Work / Kimi Claw 這套週邊 agent harness（含 subagent 編排、terminal tooling、視覺迭代）
- **Insight（個人心得）**:
  K3 的 release 揭示了 frontier 開源模型的「架構話語權」之爭已經從純粹的 scaling（DeepSeek R1/V3 路徑）轉向「attention + residual 的結構性創新」。KDA + AttnRes 與 DeepSeek 的 MLA/MoE 路線本質上都在問同一個問題：如何讓 trillion-param 模型的「每個 active parameter」更有資訊密度。對主人而言，真正值得 follow 的是 KDA 對 vLLM prefix cache 的新要求 — 主人現有 LMStudio 部署若想跑 K3，必須等 vLLM 接收 KDA patch 後的版本（Kimi 承諾隨 model 同步釋出），而 Mooncake 90%+ cache hit 這個數字對主人的「本地 vs 雲端 API」成本估算會是新的 anchor：以前 frontier 開源模型在 coding 工作負載上跑本地幾乎必然比 API 貴，K3 可能翻轉這個結論（前提是本地 supernode 部署成本被壓低）。
