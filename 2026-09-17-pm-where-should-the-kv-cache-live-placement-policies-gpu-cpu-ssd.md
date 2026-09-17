# Where Should the KV Cache Live? Placement Policies Across GPU, CPU, and SSD for Long-Lived Sessions
- 原始連結：https://arxiv.org/abs/2609.16215
- 閱讀時間：2026-09-17（午間）
- 來源：arXiv cs.AI 昨日新論文（lastBuildDate 2026-09-16 04:00 UTC；RSS 解析 297 篇 cs.AI 中揀選）

## 摘要

**主人，這一篇跟您今早 4B Qwen 擊敗 Postgres optimizer 的 query-plan 軸線形成「inference-impl ↔ inference-runtime substrate」互補對：都是「把看不見的 substrate 行為變可量化」，但打的是 KV cache tier-placement 這個更上游的 memory-substrate 問題。** 作者 Srikanta Datta Tumkur 等人在 arXiv 2609.16215 提出一個簡單但強烈的問題：當 chats / agent loops / document QA 累積 KV cache 到數 GB 等級時，這些 cache blocks 應該留在 GPU HBM 還是退到 CPU DRAM / SSD？該用什麼 placement policy、何時搬、預測重用有沒有用？

**核心發現：73× sessions / 62× cost 的提升** 來自 tier capacity（1 GPU HBM + 8 CPU DRAM + 64 SSD）的容量配比，**不是 placement policy**——把 GPU HBM 留給最近訪問，把歷史 / 偶爾重用退到 CPU+SSD，就能讓單 GPU 同時容納 73 倍 session、單 session 成本降 62 倍。這是「讓 KV cache memory substrate 變可量化」教科書級的 instance：substrate identity = cache tier，substrate behavior = 73× capacity ratio、62× cost ratio。

**Policy sweep 反直覺的負向結論** 作者在 discrete event simulator（搭配 random forest execution-time predictor）跑 4 種 placement policy × 3 workload（chat / agent / document QA）× cache-size grid，得到 4 條讓主人 horo-agent runtime 工程師必須內化的工程發現：
1. **Recency 對 chat 最優**：2.30× 比 reuse frequency 更少 PCIe 遷移流量——「最簡單 policy 對 chat 最好」。
2. **Reuse frequency 對 agent / document QA 最優**：但已知「predicted reuse」policy 跟 recency 對 chat byte-identical，等於「實作的 predicted reuse 沒有預測力」。
3. **EWMA predictor 沒有超越 reuse frequency**：作者明確歸因到 predictor 的工程失敗而非 idea 失敗。
4. **Prefetching 從不勝出**——oracle with future knowledge 也不能比 no-prefetch 更省 PCIe migration traffic。
**Decode 在 batch=1 已經 compute-bound**，placement 對 throughput 影響極小，主要改變的是 PCIe 流量與 time-to-first-token（TTFT）。這個結果直接挑戰了業界 Mooncake / LMCache / InfiniGen 把 KV cache offload 故事賣在「**等於 throughput 提升**」上的論述——實際上 offload 的價值在 capacity × cost，不在 throughput。

**為什麼主人會想讀——對應到 horo-agent / hermes-agent-lite 的 memory-substrate 設計** 三個可立即搬運的 primitive：
1. **Tier capacity 1+8+64 是 sweet spot**：主人 16 GB M4 Max VM 上跑 hermes-agent-lite 時，context window 通常 4K-128K tokens，KV cache 大小對應 MB 等級；從 tier-placement 角度，主人環境的「GPU」其實是 unified memory，相當於 1+8+0 的極小 capacity——這意味著 hermes 的 session 容量瓶頸是 GPU RAM 而非 policy，正確的工程動作是 **enforce session eviction policy + 對 long-lived session 把 KV 序列化到 SSD**，而不是砸時間設計 fancy placement algorithm。
2. **Recency > predicted reuse on chat**：horo-agent runtime 內建的 session cache eviction（如果有）採 LRU 是合理 default；不必為了「預測」引入 ML model。
3. **TTFT 是 offload 真正的成本**：KV cache offload 讓首 token 變慢但穩態快——主人若在 horo-agent lite 上加 context cache，要衡量 TTFT 退化是否被穩態加速抵銷，**這個 trade-off 在每 workload 都不同**。

**Substrate-arc pair 位置** 這篇是今早「4B Qwen 擊敗 Postgres optimizer」（inference-impl，verifiable reward shape）之後的 **inference-runtime substrate** 補腳——同樣「把看不見的 substrate 量化」家族，但 substrate identity 從「optimizer」換成「KV cache tier placement」。延伸 pair #2（runtime ↔ inference）跟 pair #7（cost-of-token ↔ cost-of-tool-call）：KV cache tier placement 正好是「cost-of-token」最上游的 cost driver——token 沒生成前就要先 allocate KV cache 容量，所以這個 substrate 是「比 token 更早的 cost」。也跟 8/04 Cloudflare KV-cache 8-byte tag 同 family。

## 3W1H 分析

- **What（做了什麼/主題）**:
  在 GPU HBM（1×）+ CPU DRAM（8×）+ SSD（64×）三層 cache hierarchy 上，用 discrete event simulator + random forest execution-time predictor 對 KV cache blocks 進行 placement policy sweep：recency / reuse-frequency / EWMA-predicted-reuse / oracle-prefetch 共 4 policy × 3 workload（chat / agent loop / document QA）× 完整 cache-size grid，量測 concurrent sessions per GPU、cost per session、PCIe migration traffic、TTFT、throughput。最大單一結論：把 tier 容量設成 1+8+64 就拿到 73.02× concurrent sessions / 62.04× cost reduction；placement policy 在這個 capacity 之上是次要因子。Decode 在 batch=1 是 compute-bound，所以 placement 不影響 throughput；主要 trade-off 在 PCIe traffic 與 TTFT。
- **Why（為什麼重要）**:
  LLM serving 業界（vLLM / Mooncake / LMCache / InfiniGen / AttentionStore）的 KV cache offload 故事幾乎都賣在「等於 throughput 提升」上，這一篇用乾淨的 simulation 反駁了這點——offload 的價值在 **capacity × cost**，不在 throughput。同時證實 **簡單 policy（recency / reuse frequency）往往比 fancy predictor 好**，因為 predictor 的工程成本（training data drift / online retraining / feature engineering）會吃掉預測紅利。這是 2026 下半年 LLM serving 工程的清醒劑：對主人 horo-agent / hermes-agent-lite 的意義是「**預設採 LRU + tier eviction，不要為了 prediction 引入 ML overhead**」。對應主人 pinned topic 的 inference / runtime / cost-of-token 三軸，是當期最強的 substrate visibility paper。
- **How（如何運作/實作）**:
  - **Discrete event simulator**：3-tier cache hierarchy（GPU HBM 1×、CPU DRAM 8×、SSD 64× 容量比），每層有不同 bandwidth / latency profile，搭配 random forest execution-time predictor（calibrated against measured A40 / vLLM stack 當 ground truth）。
  - **4 policy 對照**：(a) recency——最近訪問放 GPU；(b) reuse frequency——重用頻率最高放 GPU；(c) predicted reuse——基於過去行為預測下一個 reuse（結果跟 recency byte-identical，作者歸因到 predictor 訓練失敗）；(d) EWMA 預測器——加權移動平均預測 reuse（行為改變但仍排名 reuse frequency 後面）。
  - **3 workload**：(a) chat——短 prompt + 中等 response，reuse pattern 由 follow-up question 決定；(c) agent loop——multi-turn tool calls，cache blocks 大小不均勻；(d) document QA——長 prompt + 多 document blocks，reuse pattern 由 query refinement 決定。
  - **Pre-fetching 對照**：oracle 知道未來 access pattern，仍然比 no-prefetch 更糟——證明 PCIe 預取成本 > 收益。
  - **Decode 在 batch=1 是 compute-bound**：placement 對 throughput 影響 < 1%，但 PCIe migration 流量、TTFT 顯著改變——這是「inference-server engineer 應該看的 metric」而不是 throughput 本身的 insight。
- **Insight（個人心得）**:
  主人，這一篇對 horo-agent / hermes-agent-lite 的 actionable 三件事：
  1. **Session eviction 採 LRU 是正解**：論文的 recency 對 chat 工作負載勝過所有 fancy predictor——主人若在 horo-agent runtime 加 session cache eviction，**直接採 LRU，不要為「預測」引入 ML model**。這跟主人 pinned topic「**勿盲目加複雜度**」一致，也跟 8/04 KV-cache 8-byte tag 同 family：substrate visibility 不等於 substrate prediction，visibility 才是 engineering gain。
  2. **主人 16GB M4 Max VM 的 substrate capacity 是 1+0+0**：論文 tier 是 1+8+64，但主人 unified memory 沒有分層（無 SSD swap 也通常不啟用），所以等於 1+0+0——**session 容量瓶頸是 GPU RAM 總量，不是 policy**。正確工程動作是 **enforce hard eviction + serialize evicted KV to disk**，不是設計 fancy placement。同時，論文證實 placement 對 throughput 影響 < 1%，所以主人若犧牲 5-10% throughput 換 4× session 容量，**ROI 完全正向**。
  3. **TTFT trade-off 是 horo-agent Lite 評測的 missing metric**：論文最後一個 insight「placement 主要改變 TTFT 而非 throughput」給了主人一個現成的評測維度——在 horo-agent-lite 評測床上加一個 **TTFT measurement rig**（cold session / warm session / evicted session 三類），把 hermes session 切到 disk-cache path 時的 TTFT 退化量化出來，跟穩態 throughput 改善一起當 trade-off curve 報告。這跟今早 4B Qwen 訓練的 measurement rig as substrate 是同 family primitive，**從 training-time 搬到 inference-time substrate auditing**——是主人 9 月底可以開的 hermes-agent-lite v0.4 內部 experiment。