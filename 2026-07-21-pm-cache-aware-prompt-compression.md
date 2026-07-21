# Cache-Aware Prompt Compression: A Two-Tier Cost Model for LLM API Caching
- 原始連結：https://arxiv.org/abs/2607.15516
- 來源：arXiv cs.AI 2026-07-20 announce
- 閱讀時間：2026-07-21

## 摘要

**問題：兩種降本手段（prompt caching 與 prompt compression）一直被當作獨立優化對象，實際上會互相打架**。Production LLM deployment 普遍同時用 prompt caching（重用 prefix 給折扣價）與 prompt compression（少送 token 省錢），但主流 compression literature 預設 rho=1.0（cache 完美命中），於是 query-aware compression 為每個 query 產出不同 compressed prefix，機械性地讓 prefix-strict cache 每次都失效。本文直接以 Anthropic Sonnet 4.6 為量測對象。

**實測發現 Sonnet 4.6 的 cache 並非 rho=1.0，而是 two-tier 且有 3,500 tokens 的 sharp threshold**：低於這個門檻命中率在 30-call session 內 plateau 在 rho ≈ 0.83；超過則進入新 tier 重新計算 hit。作者把這個實證曲線代入一個明確的 cost model，預測並用實驗驗證：在 realistic rho 下，query-aware compression 只在高壓縮比 r ≥ 6 時才贏 naive caching。

**解法 CAPC（Cache-Aware Prompt Compression）= query-agnostic compression + 顯式 cache_control breakpoint + tier-preserving ratio bound**。後者確保「不要把 cached prefix 過度壓縮到掉出 hot tier」這個對 production 是反直覺的約束——多壓一點不等於更省，可能直接踩到 cache miss 的 cost cliff。

**最硬的驗證不是 LongBench-v2 的 16/16 cheapest configuration（mean 49% / 64% / 90% savings over cache-only / query-aware / vanilla），而是三個真實工作負載**：一個 94k-token schema prefix 的 enterprise tool-using assistant（r=3 下省 51.7%）、一個跑 FastAPI 與 httpx 兩個 codebase 的 graph-style RAG pipeline（vs cache-all 在 FastAPI 拿到 9.3×、httpx 2.4×），以及 tau-bench retail 50 tasks（CAPC 4 個策略最便宜、reward 與 vanilla 同分 36/50、p=1.00；query-aware compression 在這個 public benchmark 上最貴，比 vanilla 多 40.1%——首次在 public benchmark 上印證 crossover model 的 negative-ROI 預測）。

## 3W1H 分析
- **What（做了什麼/主題）**:
  把 prompt caching 與 prompt compression 從「各自最佳化」重組成「joint cost optimization」。作者先在 Sonnet 4.6 上實測 cache hit curve（two-tier、~3.5k threshold、rho≈0.83），建立一個把壓縮比 r、rho 與單次成本綁在一起的 cost model，再用這個 model 推導出 crossover point（r≥6 query-aware 才划算）。CAPC 是這個 model 的實作：query-agnostic compressor 確保 prefix 在多次呼叫間不變，搭配 Anthropic 的 cache_control breakpoint 顯式標記可重用區段，並用「tier-preserving ratio bound」預防過度壓縮把 prefix 推下 hot tier。
- **Why（為什麼重要）**:
  這個問題在 Hermes 這種靠長 system prompt、tool schema、project memory 撐起 context 的 agent runtime 上特別痛：主人現在的 prompt 已經常常超過 30k tokens（光是 `~/.hermes/skills/` 加 `~/.hermes/memories/` 載入就 5k+），但同時也會做 prompt compression 節流，兩個手段疊在一起的實際 ROI 在這篇之前從來沒有 paper-grade 量化。CAPC 的 9.3× gain 在 FastAPI codebase 上、51.7% 在 94k schema 上是 production-grade 數字，不是 long-tail toy benchmark。tau-bench retail 的 negative-ROI 驗證更狠——它告訴主人一個 counter-intuitive 結論：在目前 cache architecture 下，「再壓一點」常常反而花更多。
- **How（如何運作/實作）**:
  - 量測：對 Sonnet 4.6 不同 prefix length 跑 30-call session，把 cache hit rate 對 prefix length 畫圖，找出 3,500 tokens 的 threshold 與 plateau 的 rho
  - Model：把 r、rho、cache hit cost、miss cost 寫成 closed-form cost function，求 query-aware vs cache-only 的等價線，得到 r=6 的 crossover
  - Compressor：query-agnostic（保留 prefix 的 token-identity），不依賴 query rerank，所以 prefix-stable
  - Cache control：搭配 Anthropic `cache_control: {type: "ephemeral"}` breakpoint，把可重用段落與 query-specific 段落切開
  - Tier guard：ratio bound 形式是 `compressed_prefix_len > 0.4 * threshold_tier`，避免掉進下一 tier 重算 hit
  - 驗證：LongBench-v2 → 16/16 cheapest；三個 production workload → 9.3×、2.4×、51.7% 與 tau-bench 36/50 reward parity
- **Insight（個人心得）**:
  咱讀完直接聯想到主人現在 Hermes 的 prompt structure：`hermes_agent_profile` + 主人偏好 + skills 載入 + cron prompts + delegation history 五個段落都是 prefix-stable，但主人偶爾會貼 `重要：今天不要...` 這種 query-specific 注入，會讓 prefix-cache 失效。這篇 paper 對主人的直接 actionable 結論是：(1) 把 `~/.hermes/skills/` 與 `~/.hermes/memories/` 的 inject 流程 formal 成 prefix segment + `cache_control` breakpoint，把 query-specific 注入隔離到尾部；(2) 不要對 system prefix 做 query-aware 壓縮，那是 negative-ROI 行為；(3) 如果未來主人要自建 `agent-share` 跨機器 prompt cache（之前討論過的可能性），這個 two-tier model 比 Mistral / Anthropic 自己 hint 的版本更接近真相，可以省下主人一輪 benchmark。對 Hermes 長期 roadmap 的隱含訊號是：隨著 Anthropic / OpenAI 把 prompt cache 變成 first-class billing primitive，self-hosted LLM inference（主人現在用 LMStudio council + KTransformers 的 heterogeneous 路線）會逐漸被 cache-aware compression 的 cloud cost curve 逼到 niche——除非端到端把 KV cache state 也 expose 出來，這條技術債下一步可能會落在 Hermes 自己的 `hermes-runtime-operations` skill 維護裡。
