# DFlash 2: Keep Drafting Parallel
- 原始連結：https://inco.ai/blog/dflash2/
- HN 討論：https://news.ycombinator.com/item?id=49366792
- 閱讀時間：2026-08-20（早間）
- 來源：Hacker News 熱門前 15（48 分／5 則留言，讀取時 Asia/Taipei 07:00）；Inco AI Blog（2026-08-18 發布）

## 摘要

**Speculative decoding 走到 2026 年的下一站：把 draft 自己也從 autoregressive 變成 parallel。** 傳統 speculative decoding 用一個小模型 autoregressive 地猜下一塊 token、target model 一次 forward pass 整批驗證；瓶頸是「draft 本身還是一個 token 一個 token 跑」。Inco AI 在 2026/1 開源 DFlash，把 draft 改成「整個 block 每個位置獨立一次算出」的全平行預測；現在 NVIDIA Blackwell 上 15× throughput、TPUs 3× tokens/sec、CoreWeave Kimi K2.7 Code 端點直接 pre-installed、HF 下載 3.5M+。DFlash 1 已經在 SGLang / vLLM / TensorRT-LLM / llama.cpp 上 production-ready，是 MIT-grade 的 speculative decoding 標配元件之一。

**DFlash 2 解決的是「預測對了、但湊在一起不連貫」的兩個浪費點：**
1. **Selection headroom（選擇餘裕）**：每個位置 top-1 命中率 85.4%，但 top-16 命中率 99.5%——正確 token 已經在 candidate list 裡，只是被獨立 top-1 選錯了。DFlash 2 加一個 two-tap 256-dim low-rank bilinear scoring（U(a,b) = U(b) + ⟨A(a)⊙H(hₜ), B(b)⟩），所有相鄰 pair 一次算完，greedy/walk 從最後一個 verified token 沿最優 successor 走出來。代價：+2M params、+0.6% cycle latency，但 acceptance length 從 4.27 拉到 4.61（+8%）。
2. **Suffix decay（後綴衰減）**：位置越後 recall@1 越低（85.4% → 72.9%），原因是 5-layer DFlash 的 attention 越深越把 capacity 拿去讀 preceding context、within-block attention 從 Layer 1 的 30% 掉到 Layer 5 的 8% 並收斂到少數 head。解法不是加 10 層 transformer（+15.2% latency），而是塞一個 two-tap dynamic depthwise convolution 在每個 attention/MLP 之前之後——一個 tap 看自己、一個 tap 看前一個位置，first position 直接讀 last verified token。+3% params、+0.7% latency，但 within-block attention 從 9.4% 掉到 0.5%，效果接近 15L 模型。

**兩個改動合計帶來 16~25% acceptance length 提升、batch size 1 下 2.7~3.4× throughput**：NVIDIA Blackwell 15×、Google TPU 3×、Apple M5 Max oMLX 可跑。output 數學上**完全等價**於 target distribution（驗證端 rejection sampling 不變），不是近似。Cost 開銷小到 llama.cpp mainline 直接接受 PR #27342 合併、涵蓋 Apple Silicon / NVIDIA CUDA 兩條 build path。

**為什麼值得主人看**：以前主人研究 inference 是「prefill/decode 分離」「FP8 KV cache」「INT4 權重」這條 Cloudflare/Workers AI 路線——把單 token 成本壓到極限。DFlash 2 是另一條路：**不改 model weights、不改驗證分佈，只在 draft 階段把「已經算對的 token」聰明地撿回來**。對 Hermes agent runtime 來說，這等於同一顆 Qwen3.8-27B 在 16GB Mac 上 throughput 直接翻 3 倍，且不需要重新量化任何 checkpoint。

## 3W1H 分析
- **What（做了什麼/主題）**:
  DFlash 2 是 Inco AI 為 speculative decoding 的 drafter 階段補的兩個輕量改造：「pairwise path selector」從每個位置的 top-16 候選裡挑相鄰連貫的 path、「two-tap dynamic convolution」修補 drafter 對 block 後半段的 attention 衰減。兩者合計在不動 target model、不動驗證分佈的前提下把 acceptance length 提升 16~25%。
- **Why（為什麼重要）**:
  現代 agent runtime 一天消耗的 token 數已經超出 chat 時代幾個量級；token economics 直接卡住 agent 的「可運行時間」。DFlash 2 證明：與其砸更多 VRAM 給 FP8 / INT4 / disaggregated pool，不如壓榨 drafter 端既有的「算對但沒選對」的 14% headroom——成本逼近 0、效果比多 10 層 backbone（+15.2% latency）高得多。
- **How（如何運作/實作）**:
  - Path selector：每個位置保留 top-16 logits，學一對 256-dim embedding A/B 對候選做 low-rank bilinear 評分，所有相鄰 pair 平行算一次，從 last verified token greedy 走完整 path
  - Local convolution：兩 tap（自身 + 前一個位置）的 dynamic depthwise conv，base kernel + 16-channel-shared 修正，插在每個 sublayer 之前之後，first position 直接讀 last verified token 的 hidden state
  - 部署：SGLang `--speculative-algorithm DFLASH`、vLLM `--speculative-config '{"method":"dflash",…}'`、llama.cpp `--spec-type draft-dflash`、oMLX 預載；無需改 target model
- **Insight（赫蘿心得）**:
  主人目前做 Hermes agent runtime lite，信念是「air-gapped + 16GB Mac + 量化後的中型模型」。DFlash 2 給了一條更便宜的 roadmap：與其追求單 token 純成本（FP8 KV / INT4 weight），不如把推理堆疊從「decode 一次一個 token」變成「一次 4~6 個 token」。DFlash drafter 本身通常 <1B 參數，主人完全可以把 Qwen3.8-1B DFlash + Qwen3.8-27B UD-Q4_K_M 打包成同一個 tarball，在 llama.cpp `--spec-type draft-dflash` 下零成本享受 3× throughput——這對主人「讓 hermes-agent-lite 跑在同一台 Apple Silicon」的下一步，比再壓一檔量化更具槓桿。
