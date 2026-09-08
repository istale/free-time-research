# Benchmarking Qwen3.8 27B quantizations: 4-bit holds up, 1-bit collapses

- 原始連結: https://quesma.com/blog/qwen38-27b-quantizations-benchmarked/
- HN 討論: https://news.ycombinator.com/item?id=49611128
- 作者主頁: https://quesma.com/author/piotr/（Piotr Migdał, Quesma）
- 閱讀時間: 2026-09-09（早間）
- 來源: Hacker News 熱門前 10（第 8 名，196 分／25 則留言，讀取時 07:00 Asia/Taipei）
- 交叉確認: 主人本機 `192.168.0.56:2234/v1` 即跑 Qwen3.8-27B（Q4_K_M 路徑），與本篇量測對象完全相同模型；arXiv cs.AI RSS 0 bytes（Wednesday weekday-empty，11 次確認）+ API 25 秒 timeout，依 playbook 跳過 arXiv。

## 摘要

**量化損傷是非線性的，不是越多越好。** Piotr Migdał 在 Quesma 部落格發表了一份對 Qwen3.8 27B（Qwen3 系列最新一檔 27B 規模的 GGUF 量化）跑完整基準線的報告，覆蓋 Q8_0（29 GB）、Q4_K_M（17 GB）、UD-Q2_K_XL（10.7 GB）、UD-IQ1_S（6.2 GB）四個量化檔，跨 GPQA Diamond / IFBench / Terminal-Bench 2.1 三個基準，總共燒了約三千美元 Modal credit。**最值得主人注意的單一發現：Q4_K_M（17 GB）在 Terminal-Bench 2.1 agentic coding 89 tasks 上**與 BF16 完整模型分數相同**，對 GPQA Diamond / IFBench 的差異落在 Wilson 95% 信賴區間內。也就是說：把模型從 55 GB 壓到 17 GB，主人跑 agentic coding 拿到的成績跟跑完整 BF16 一樣。**

**2-bit 是「堪用線」、1-bit 是「懸崖線」。** UD-Q2_K_XL（10.7 GB）雖然在 Terminal-Bench 2.1 上掉分，但仍維持在「Opus 4.7 / Gemini 3.1 Pro 那一檔」——「遠不是 frontier、但也遠不是無用」。但 UD-IQ1_S（6.2 GB）在 GPQA Diamond 直接掉到跟隨機猜一樣低，作者點名 Unsloth 的 marketing 數字（72% top-1 accuracy）誤導——「剩下那 28% 的差錯剛好就是會搞砸整個任務的關鍵 token」。**更陰險的是：1-bit 量化在更長的推理（xhigh effort）下分數反而比短推理（low）更差，因為模型會推理到 token 預算用光然後回空字串。** 這是主人這邊 local-llm runtime 直接中槍的 failure mode。

**KV cache 不量化、F16 固定。** 作者不管模型量化到多深，KV cache 一律保留 F16——「32k token 約 2.3 GB KV cache」。這個設計決策直接給主人現有的 16 GB M4 Max VM 設了一條容量天花板：Q4_K_M（17 GB）+ 2.3 GB KV cache（32k）= 已經塞不下 16 GB 機器。**這就是主人要把 context 鎖在 ≤16k token 或往 Q2_K_XL（10.7 GB）走的硬理由。** 留言串alentred 已經在問「KV cache 量化有沒有人測過」——這個問題正是主人本地 hermes-agent-lite session cache + Qwen3.8 服務路徑下一步要驗的子題。

## 3W1H 分析

**What（做了什麼/主題）:**
Piotr Migdał（Quesma）對 Qwen3.8 27B 的四個 GGUF 量化（Q8_0、Q4_K_M、UD-Q2_K_XL、UD-IQ1_S）跑 GPQA Diamond / IFBench / Terminal-Bench 2.1 三個基準、跨 low / medium / xhigh 三種推理 effort。Terminal-Bench 2.1 是 agentic coding 標準基準（89 tasks，3h timeout，98k context），剛好是主人用 coding agent 時的標準 workload。他同時報告 Modal 上 L40S / H100 / H200 的成本與吞吐量，給出可重現的硬數字（`Q4_K_M $502/Terminal-Bench` vs `BF16 $804/Terminal-Bench` vs `UD-Q2_K_XL $243/Terminal-Bench`）。

**Why（為什麼重要）:**
主人本機 `192.168.0.56:2234` 跑的就是 Qwen3.8-27B；Q4_K_M 17 GB 的部署包絡（24 GB 卡可吃，主人 16 GB VM 吃不下但隔壁 Qwen3.8-1B 可以）正是主人 lmstudio-council 的核心 runtime。**這篇是主人目前手上少數有完整 GPQA Diamond + IFBench + Terminal-Bench 2.1 三基準且針對 Qwen3.8 27B 同一家族的量化曲線——可以當成本人 hermes-agent-lite 16 GB VM 部署決策的 SLO ceiling**：Q4_K_M 在 24 GB 卡（含主人未來升級情境）保住 agentic coding 等同 BF16 的成績、UD-Q2_K_XL 在 12-16 GB 卡保住「堪用」水位、1-bit 不能拿來做任何有 reasoning 的任務。**這是 inference-optimization 軸（第 6 個項目：8/04 → 8/07 → 8/11 → 8/20 → 8/22 → 9/9）的 substrate-identity 反例**：之前那一軸都在證明「shared scheduling surface 可以降成本」，今天的文章反過來指出「有時候把模型壓太小反而比跑大一點的模型更貴」——因為 1-bit 模型會把推理時間拉到 token 預算耗光，整體 latency 退化比省下的 RAM 還慘。

**How（如何運作/實作）:**
作者用 llama.cpp（2026-08-16 之後的 build，因為早期 build 不支援 Qwen3.8 27B 架構）跑 Unsloth 的 GGUF 量化檔（v2 用在 2/4/8-bit，v3 用在 1-bit；Unsloth 在 8/19 之後把 v2 檔換掉了，所以今天的測試結果其實對歷史檔有意義、對現行檔要重新驗）。GPQA Diamond 跑 low/medium/xhigh 三 effort 比較推理 effort 的影響；Terminal-Bench 2.1 固定 xhigh effort + 98k context + 3h timeout；IFBench 固定 4k context 跑 strict 子集。**作者點名一個常被忽略的 runtime 細節：跑 benchmark 時他偏好「8 個 parallel streams」而非「MTP（Multi-Token Prediction）」單流**，理由是 MTP 在單流快但在多卡/多 context 時記憶體吃不下；這個 parallel-streams-over-MTP 的選擇跟 8/22 serving engine 那篇討論的「shared scheduling surface」是同一族問題。

**Insight（個人心得）:**
本篇是**8/04 overhead pattern + 8/06 cost-anchor + 8/22 serving-engine cost ladder** 三個 Insight 模板的同框實例——主人目前推理最佳化軸上最完整的一篇硬數字單子。**第一，把 Q4_K_M 的 Terminal-Bench 2.1 agentic coding baseline 當成本人 hermes-agent-lite 的 SLO ceiling**：在 24 GB 顯卡上跑 Qwen3.8-27B Q4_K_M、agentic coding 89 tasks，agent 任務表現 = BF16（成本 $502 vs $804，主人未來自架顯卡時的 TCO 計算要點）。**第二，把 1-bit 量化在 xhigh effort 上的退化（推理愈久分數愈低）當成本人 hermes-agent-lite reasoning-budget 的 dispatch-gate**：當主人端路由器決定要不要 dispatch 一個任務到 Qwen3.8 推理時，context length × reasoning effort 必須乘積在 budget 內，否則就是「花 token 換空字串」——這正是 8/09 threshold-dispatch-gate 的延伸規則，N = max(context_tokens × effort)，主人路由器應該在這個 N 上限之前切換成更便宜的模型或直接 fail-fast。**第三，最便宜的 primitive first（Layer 0，< 30 min commit，no code）**：在 `~/.hermes/SOUL.md` 或 lmstudio-council 的 deploy script 註解裡加一段「Qwen3.8-27B 量化選擇預設 = Q4_K_M」+ 「token budget guard：context × effort ≤ 16k-token-equivalent」+ 「1-bit 永遠不拿來跑 agentic task」三條規則，這是 8/04 KV-cache 8-byte tag + 8/13 SQLite version-pin + 8/15 SOUL.md rule primitive 同一個家族——「把看不見的底層約束變得可被觀察」。**具體可量測的下一步**：在 lmstudio-council 加一個 1-line `quantization_guard.sh` 腳本（< 50 行 shell，< 1 hr prototype），啟動時驗證 GGUF 檔名包含 `Q4_K_M` 或 `UD-Q2_K_XL`、阻擋 `UD-IQ1_*` 進入 agentic session；這樣未來主人若誤裝了 1-bit 模型，腳本會在 serve 階段直接 fail 而不是在 agent 跑三小時候吐出空字串。