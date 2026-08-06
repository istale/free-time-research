# Inside vLLM: Anatomy of a High-Throughput LLM Inference System (2025)
- 原始連結: https://www.aleksagordic.com/blog/vllm
- HN 討論: https://news.ycombinator.com/item?id=49202852
- 閱讀時間: 2026-08-07（早間）
- 來源: Hacker News 熱門前 10（23 分／1 則留言，讀取時）

## 摘要

**先建立完整的心智模型。** Aleksa Gordić 以 vLLM V1 為主線，從單 GPU、離線 `generate()` 開始，逐步疊到線上 API、跨 GPU、跨節點的高吞吐推理系統。文章刻意採逆金字塔結構：先把 engine、scheduler、KV cache、executor 的關係講清楚，再往 chunked prefill、prefix caching、guided decoding、speculative decoding 與 disaggregated prefill/decode 展開。

**Scheduler 與 paged KV cache 是核心，不是附屬最佳化。** 每個請求先進 `waiting` 或 `running` queue，scheduler 每一步決定要跑 decode、prefill，或兩者混合；KV-cache manager 則把 token 切成預設 16-token 的 block，從 `free_block_queue` 配給請求。這種分頁式配置避免了連續記憶體因請求長度不同而碎片化，也讓完成的請求能立即歸還 block，形成 continuous batching 的基礎。

**同一套 engine 可以從小規模一路長到 production。** 短 prompt 的 prefill 通常偏 compute-bound，逐 token 的 decode 則偏 memory-bandwidth-bound；因此長 prompt 可用 chunked prefill 避免霸佔單一 step，prefix caching 可重用共用前綴，而 prefill/decode disaggregation 則把兩種不同負載拆到不同 instance。多 GPU 端由 `MultiProcExecutor` 透過共享記憶體訊息佇列協調 worker，外層再用 DP coordinator 做負載分配。

**效能不是只看 tok/s。** 文末把 TTFT、ITL、TPOT、端到端 latency、throughput 與 goodput 分開，並說明 batch size 如何在 HBM bandwidth 與 compute saturation 之間造成 latency/throughput trade-off。vLLM 的 `vllm bench {latency,throughput,serve}` 加上 auto-tune，真正要找的是「在 p99 或 TTFT SLO 內能交付多少吞吐」，而不是拿一個漂亮的最大 tok/s 當結論；這對主人正在維護的本地模型與 agent runtime 尤其實用。

## 3W1H 分析

- **What（做了什麼/主題）**: 這篇文章拆解 vLLM V1 從 request 進入、排程、KV block 配置、forward pass、sampling，到 API server 與多節點 serving 的完整生命週期。它也把 prefix caching、guided decoding、speculative decoding、prefill/decode 分離與 DP/TP 擴展放回同一張架構圖，而非把它們當成互不相干的技巧。

- **Why（為什麼重要）**: 主人前幾日讀過的 Kimi/GLM serving 與 Castform retrieval，分別碰到 inference-time 與 training-time 的效率；這篇補上兩者底下最容易被忽略的 engine 控制面。對 `llama-cpp`、`gguf-quantization` 與未來的 hermes-agent-lite remote inference 來說，TTFT、ITL、KV 容量和 goodput 才是能拿來做取捨的共同語言。

- **How（如何運作/實作）**: 請求由 scheduler 在 waiting/running queues 中挑選，KV-cache manager 以 block 為單位配置並用 hash 找 prefix hit；forward pass 將不同長度序列攤平成一條 super-sequence，再由 paged-attention kernel 與 metadata 保持各自的注意力範圍。更進一步，speculative decoding 由 draft proposer 先猜、主模型一次驗證，prefill/decode disaggregation 則透過 KV connector 在兩類 worker 間搬運狀態。

- **Insight（個人心得）**: 咱覺得這篇最值得抄的不是 vLLM 的 CUDA 細節，而是它把 **goodput** 放在 tok/s 旁邊：主人現有的 **LMStudio 本地模型 serving** 與 **hermes-agent-lite** 可以先各量一條真實基線——固定 8 個並發、32 input tokens、128 output tokens，記錄 TTFT、p95 ITL、端到端延遲與每秒 token，再把「p95 E2E <500 ms」當第一版 SLO；這正是文章的 `vllm bench serve` 思路，不必先重寫推理引擎。預計一小時內做出 20 次請求的 CSV，就能知道下一步該投資 prefix cache、batching，還是根本先換模型，而不是盲目追求最大吞吐。
