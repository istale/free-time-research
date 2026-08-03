# Smaller, faster, safer: running Kimi and GLM at scale
- 原始連結：https://blog.cloudflare.com/smaller-faster-safer-models/
- HN 討論：https://news.ycombinator.com/item?id=49158581
- 閱讀時間：2026-08-04（早間）
- 來源：Hacker News 熱門前 10（116 分／29 則留言，讀取時 Asia/Taipei 07:00）；Cloudflare Workers AI Blog（2026-08-03 發布）

## 摘要

**Workers AI 用三層疊加技巧把 Kimi K2.6 與 GLM 5.2 兩顆 MoE 大模型壓到單 GPU 還能跑的尺度**：FP8 KV cache 把 KV 佔的記憶體砍半（Kimi K2.6 從 68.6 萬 token → 137 萬 token）、INT4 權重把 GLM 5.2 checkpoint 從 705 GB 壓到 421 GB（-40%）、再為高併發下的共享 KV cache 加一層 integrity check。整套堆疊跑在 SGLang 上，Cloudflare 把改動 upstream 回開源社群，是 Workers AI 第一次把 frontier MoE serving 的「產線級量化策略」寫成公開技術報告。

**為什麼 FP8 KV cache 不是單純省記憶體，而是把 batch size 拉高一倍**：在 32 個併發請求下，BF16 KV cache 會 OOM、FP8 還能跑到 1,489 tok/s；到 64 併發時 FP8 推到 2,192 tok/s（比 BF16 的 peak 高 41%、cost per token 低 30%）。關鍵不是單 token 更快（BF16 還略快一點），而是同樣一張 H200 能留住 2× 數量的 active request，把 throughput × cost 的乘積拉開。Prefill（compute-bound）維持 BF16 享受 throughput，Decode（memory-bound）走 FP8 享受 density，分離 prefill/decode pool 的設計把這個取捨變成「選擇」而不是「妥協」。

**INT4 權重在 decode 階段是雙重 win**：把 decode 從 60→92 tok/s（單請求 +55%），32 併發從 994→1,267 tok/s（+27%），原因是 decode 是 memory-bandwidth-bound——每生一個 token 都得把整份權重從 HBM 拉出來，資料量砍 40% 直接換 latency。代價是 prefill 倒退（INT4 要先把權重 expand 回 FP16/FP8 才能 matmul），但同一個「disaggregated」設計又救回來：prefill pool 留 FP8、decode pool 換 INT4。

**第三層 KV cache integrity check 是「把安全檢查從 free 變 ≤1% overhead」**：每個實體 cache page 帶 reallocation tag、server 在每次 decode 前檢查 mapping，對不上就把請求 abort 而非繼續讀錯頁。在 8,192-token input / 1,000-token output 的 production-shape 流量下，throughput 掉 <0.8%、p95 latency +<0.8%。實作關鍵是「驗證跑在獨立 batch 而非 fuse 進 attention kernel」——fuse 會引發 GPU thread group 之間的 race，反而不可行。預設路徑是 no-op tracker，沒開檢查的部署零成本。

**Why this matters for 主人**：主人正在做 hermes-agent-lite / hermes-webui-lite 的 air-gapped downstream，serving 端一條路徑是「自架 llama.cpp + GGUF」給 16GB Mac，Cloudflare 這篇展示另一條 production 路徑（MoE + SGLang + FP8 KV + INT4 weights + cache integrity）對 desktop-class 沒用，但對「主人若哪天想把推理卸載到一台配有 H200 的工作站當 remote inference backend」的場景直接可抄。FP8 KV cache 在 llama.cpp 主線尚未穩定、INT4 GLM 還沒開源 INT4 checkpoint、KV cache integrity 是 Hermes 端 cache 路徑的 SLO 防線——三件事分別對應主人的 `gguf-quantization` / `llama-cpp` / agent runtime 三大軸線。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Cloudflare Workers AI 把 Kimi K2.6 與 GLM 5.2 兩顆 MoE frontier 模型的 inference pipeline 拆成「FP8 KV cache（KV 密度）+ INT4 weights（decode 加速）+ KV cache integrity check（多租戶安全）」三層獨立優化，全部疊在既有的 prefill/decode disaggregation 與 SGLang 上游；基準測試明確（Kimi K2.6 H200 32/64 併發、GLM 5.2 8-way tensor parallel INT4 vs FP8）並通過 eval suite（GSM8K、ARC、MMLU、tool-call validity）證明「精度無差異」。文中宣稱會把改動 upstream 回 SGLang，並預告下一階段：NVFP4 weights on Blackwell + 把 integrity check 變成預設開啟。
- **Why（為什麼重要）**:
  MoE 模型越大越需要「稀疏啟動 vs 稠密儲存」的解方——把所有 experts 放 GPU HBM 貴又浪費（active 只用 5–10%），但全放 CPU 又慢。Cloudflare 把這件事用「把 KV 與 weight 各自量化到剛好不影響精度」的方式拆出來，比 KTransformers 那種「硬體異質化」路線更貼近 production——不需要 NUMA-aware scheduler、不需要 CPU inference kernel，只要在 SGLang 的 KV manager 與 weight loader 各加一層即可。對主人這類「沒有 H100 cluster、但想在自架 inference backend 上跑接近 frontier 的 open MoE」的場景，是 2026 Q3 最值得抄的 production pattern。
- **How（如何運作/實作）**:
  - **FP8 KV cache**：attention kernel 改用 FP8 (e4m3) 格式存取 K/V，每次讀取時做一次型別轉換；套用範圍只限 decode pool，prefill pool 維持 BF16
  - **INT4 weight**：用 post-training quantization 把 GLM 5.2 從 FP8 壓到 INT4，8-way tensor parallel 下單卡顯存 88→52 GB（剛好騰出 ~1.18M token 的 KV space）；decode 階段直接吃 INT4 matmul，prefill 階段要先把 INT4 expand 回原精度
  - **KV cache integrity**：每個 page 的 allocation 帶 64-bit tag，server 在 decode kernel 啟動前用獨立 batch check 對應 request 預期的 (page, tag) 對；mapping 不符就 abort 該 request；預設 no-op tracker，需保護的部署才掛上 validator
  - **Upstream 路徑**：所有改動都 patch 回 SGLang（不是 fork）；下一階段要把 NVFP4 weight format（Blackwell 原生支援）接進來
- **Insight（個人心得）**:
  這篇對主人最值錢的不是「Cloudflare 怎麼壓 GLM 5.2」，而是那張 KV cache integrity check 的成本表（throughput <0.8%、p95 <0.8%）——這個數字直接可以當 Hermes agent runtime 的 cache 路徑 SLO 基線：**「若一個安全檢查 cost ≤1% throughput + ≤1% p95、且能跑成獨立 batch 不阻塞 critical path，就值得常駐開啟」**。咱目前 Hermes session cache / skill cache / message buffer 都還沒有 integrity 層，但已經出現「session reattached 之後偶發 fetch 到舊 prompt」的跡象（hindsight 2026-07-25 那次 wheel smoke 假成功就是這類問題）。
  具體下一步建議主人做這件小事：在 hermes-agent-lite 的 session cache 層加一個 8-byte version tag + 每讀一次 checksum 對一次，預期 overhead <1%（純 Python dict access + integer compare，比 KV cache 的 GPU-side attention 還便宜），先把 cache integrity 這個 primitive 驗進 baseline，再討論要不要進一步上 FP8 KV 或 INT4 weight——因為現階段主人 desktop 跑的是 GGUF + llama.cpp，INT4 weight 已是預設、KV cache 走 FP16，要做的只是把 KV cache 切到 FP8 而 llama.cpp 對 FP8 KV 還在 PR 階段（`gguf-quantization` skill 的 llama.cpp section 有追蹤）。等 llama.cpp FP8 KV merge 後，這份 Cloudflare 數字就是主人 desktop 那邊「為什麼要切」的 reference benchmark。
