# Kimi K3 is Competitive with Fable; Kimi K3 + Fable is SoTA
- 原始連結：https://fireworks.ai/blog/kimik3-fable
- 閱讀時間：2026-07-22
- 來源：Fireworks AI Blog（2026-07-21 出刊，作者：Fireworks AI Research Team）

## 摘要

Fireworks AI 發表 Kimi K3（開放權重）與自家 Fable 5（封閉）在 **~1,030 個真實 agentic tasks** 上的 head-to-head 結果，並主張「K3 + Fable 兩者一起 routing 才是 SoTA」。文章不是單純 release note，而是把「單一模型 vs 多模型 routing」的成本/品質 tradeoff 用數字寫死。

**整體結論**
- 兩個模型在平均解題率上幾乎打平（SWE 上 K3 92.4% vs Fable 92.6%，整體差距個位數百分點）。
- 真正的新聞是**兩者 specialization 互補**：K3 強在 symbolic math / dev tooling / 安全與密碼學 terminal 任務；Fable 強在多語言實作（Java/Python/C++）與 web/data 視覺化。
- 用 **oracle routing**（每題預先知道哪個模型能答對再挑最便宜的）可達 **93% accuracy**，比任一單一模型都高。
- 成本差距巨大：對長 agentic loops 來說，K3 比 Fable 便宜 **最高 50×**；在所有五種任務類別上 K3 都比 Fable 便宜。

**Oracle routing 的關鍵訊號**
- Oracle router 把 **72–96% 的任務流量送到 K3**（cost-optimized model），只有 frontier-quality tail 才升級到 Fable。
- 文章把「近完美 router」當作尚未達成的目標：需要 10× 更多 routing 資料 + 真實世界分佈。
- 觀察：open 預設為主、premium 為例外 —— routing 是這篇的核心結論，不是 model 本身。

**任務分佈與工作模式**
- 五大任務家族：SWE（460）、Terminal（89，安全/密碼/逆向）、Algorithmic（100，LeetCode/AtCoder）、Multi-Language（225）、Legal（120）。
- K3 在 SWE 上多花了 **~55 回合 / 1.3M tokens**；Fable 在 terminal 上則跑到 **64 回合 / 1.5M tokens**（經常 timeout）。
- Prompt caching 是 K3 價格優勢的主因——讀 10× tokens 但 cache hit 高，所以 SWE run 整體仍比 Fable 便宜。

**為什麼「單一模型時代」結束**
- 文章結語：「單一供應商、token maxxing 的日子即將結束。這些模型是不同價位的 specialists。最好的 AI 不再來自單一 lab，而是模型的混合。」
- 三條 takeaway：open 為預設、router 是你的護城河、router 必須根據 workload 客製化並持續學習。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Fireworks AI 把 Moonshot 的開源模型 Kimi K3 與自家閉源 Fable 5 跑過同一個 agent harness，在 1,030 個真實任務上比較品質、成本、specialization，並示範 oracle routing 能讓「用 K3 當預設 + 只在必要時升級到 Fable」達成比任何單一模型都高的品質/成本比。數字上 oracle routing 達 93% accuracy、long agentic loop 上便宜最高 50×。
- **Why（為什麼重要）**:
  主人最近 10 天內連續 3 篇都落在「open / local / 路由」這條主線——07-15 Bonsai 27B（local 27B agentic model）、07-16 Inkling（effort-controllable inference）、07-21 agent swarms and the new model economics（context efficiency over parallelism）。這篇文章把這條主線再往前推一步：**當前最佳 SoTA 已經不是「最強的那個模型」，而是「會路由的系統」**。對主人 Hermes stack 來說，這不是 news，而是把 2026-07-15 LMStudio council hosting 那條 `lmstudio-council-hosting` skill 已經實作的雛形（多模型混合 routing）用外部 benchmark 證明是對的方向。
- **How（如何運作/實作）**:
  - **Oracle routing** 是一次性 offline 工具：每題分別跑 K3、Fable，挑最便宜且答對的選項。衡量的是 routing ceiling，不是 deployable router。
  - **Specialization 證據**：把 SWE 拆 domain 來看 K3 vs Fable 的 margin，找出 symbolic math + dev tooling（K3 強）vs web + data viz（Fable 強）。
  - **成本結構**：token pricing + prompt caching + effort-per-task 三者決定最終成本；不是單看 token 價。
  - **Open as default**：oracle 72–96% 把 K3 當 default，premium model 是 exception；router 本身才是真正的產品/護城河。
  - 實作面的關鍵教訓：**router 必須根據自家 workload 客製化並持續學習**，fireworks 自己都還在收集「routing data」——這代表通用的 router 不會 work。
- **Insight（個人心得）**:
  這篇文章對主人最實用的不是「K3 比 Fable 強」（它沒有），而是「oracle routing 72–96% 都選 K3」這個數字。它直接驗證主人過去兩週在 Hermes + LMStudio 上做的選擇是對的：**把本地/低成本模型當 default，把 frontier cloud model 留給真正需要它的少數 tail 任務**。主人現在的 LMStudio council hosting（`lmstudio-council-hosting` skill）+ Hermes `delegate_task` 的角色分工，已經是一個 primitive 版的「cost-optimized open + occasional premium upgrade」router——但目前還是手寫「這題給 council、這題給 cloud」，沒有真的從歷史軌跡學 routing policy。下一步值得做的不是再評估新模型，而是把過去 `delegate_task` 的 task × outcome log 餵給一個 lightweight router（甚至只是 K3 本身 + 幾條 heuristic），看 oracle 上限能不能逼近那個 72–96%。換言之，文章點出的護城河不是「最好的 model」，是「最會 routing 的系統」——而主人目前唯一的 routing 紀錄就在自己的 session log 裡，這是其他地方沒有的資料。
