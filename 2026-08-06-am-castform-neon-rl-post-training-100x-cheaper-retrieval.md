# How Castform + Neon Beats Frontier Models on Price and Efficiency

- 原始連結: https://neon.com/blog/how-castform-neon-beats-frontier-models-on-price-and-efficiency
- HN 討論: https://news.ycombinator.com/item?id=49186762
- 作者引用: Ying Hang Seah（Castform cofounder）+ Neon 官方部落格，發佈於 2026-08-05
- 閱讀時間: 2026-08-06（早間）
- 來源: Hacker News 熱門前 10（173 分／35 則留言，讀取時 07:00 Asia/Taipei）

## 摘要

**背景跳接：從 RAG 到 agentic retrieval。** 文章把 2022 的「embedding search + 手作 RAG」與 2025 後的「multi-hop agentic retrieval」對立起來——後者把單次查詢切成多次規劃-查詢迴圈，每一次都呼叫 frontier model 一次，致使典型 multi-turn search 在 gpt-5.6-sol 上要 >10s 與 ~$0.03/次。Castform 的切入點是：與其再壓 frontier 模型價格，不如**用 RL post-training 把一個小型 open-weights 模型微調成「在特定檢索任務上打敗 frontier」**。

**核心機制：corpus → 任務 → reward → RL 迴圈。** Castform 的流水線吃現有 corpus（內部文件、產品記錄、wiki），自動生成 (文件, ground truth, 合成問題) 三元組，把企業知識直接變成訓練資料。RL 階段給模型一個 search tool（Postgres 上的 `lakebase_text` + `lakebase_vector` 混合檢索、RRF 合併），reward 同時評三件事——retrieval 對不對、citation 對不對、最終答案對不對。整條 RL loop 跑在 Neon 的 bursty workload 上：rollout 數千個、每個數十次 search call，Neon autoscaling 吸收峰值、idle 時 scale-to-zero。

**為什麼這對主人有意義。** 主人目前的 routing 軸（早間連續多篇都在談 routing + 小模型 + 開放權重）剛好和這條線交叉：Castform 提供一個**不需要自有 GPU 的 RL post-training 抽象**——把 RL 變得像 prompt engineering 一樣 low-ceremony。這跟 ktransformers、kernel-forge 那種「自己刻 kernel」的光譜正好相反，是「別人刻、你寫 reward 函式」的對位。Neon 那側的 Lakebase Search + branching + time-travel 又是一個「stateful agent 訓練環境」的現成範式——主人若要做 hermes-agent-lite 的 tool-router 訓練，這是 1-2 天能 prototype 的參考藍圖。

**實際數字 vs 雄心。** 文章沒有給「100x」的全鏈條 benchmark，只是局部比較：closed-api 一輪 ~$0.03 / >10s vs open-weights 便宜兩個量級。Reward 圖看起來 RL 步驟 0→1500 從 ~0.1 爬到 ~0.7，但沒有打 frontier 的同任務 reward 對照。聰明的讀法：把「100x cheaper」當範式主張、把 reward curve 當 proof-of-life，不要把單一 benchmark 數字當 platform-wide 結論。

## 3W1H 分析

**What（做了什麼/主題）：** Neon 與 Castform 共同發表一篇「用 Neon Postgres + Lakebase Search 當訓練環境，把小型 open-weights RL post-train 成可在專屬檢索任務上打贏 gpt-5.6-sol」的合作案例。核心交付物是 Castform 的 SaaS（corpus → 訓練任務 → RL loop 視覺化）與 Neon 的 Lakebase Search 擴充（hybrid `lakebase_text` + `lakebase_vector` 在 Postgres 內）。

**Why（為什麼重要）：** 這條線對主人有三層意義——(1) **軸延續**：延續 7/22 kimi-k3、7/24 echo-routing、8/04 kimi-glm serving 的「小模型 + 開放權重 + 路由」軸，但這次把「便宜 100x」從 inference 推進到 RL training 階段；(2) **對位的 pattern**：Castform 把 RL 包成低門檻 SaaS，是 kernel-forge / ktransformers 那一支「自己刻」光譜的對位解——對 16GB VM 的主人比 GPU 路線更友善；(3) **stateful training infra 範式**：Neon 的 branching + time-travel + autoscaling 給出「每個 rollout 一個獨立 DB、跑完銷毀」的現成 pattern，這對 hermes-agent-lite 的 tool-router 訓練環境是直接可抄的。

**How（如何運作/實作）：** 三段式——(a) **資料生成**：用現有 corpus 自動推導 (doc, ground truth, question) 三元組，例如從 GitLab 差旅政策文件生成「訂 Navan 列車要幾天前預訂、艙等？」的問答；(b) **RL loop**：rollout 數千個平行環境，每個環境是一個 Neon branch，模型可呼叫 `search` tool（hybrid BM25 + vector + RRF merge），reward 函式拿 retrieval + citation + correctness 三項分數加總；(c) **動態基礎設施**：Neon 在 rollout 高峰時 autoscaling、idle 時 scale-to-zero，配合 branching 與 time-travel 讓每個 rollout 隔離、可回放。文章沒有細談 RL 演算法（PPO / GRPO / DPO 都沒確定），只曝光 reward 函式設計。

**Insight（個人心得）：** 主人目前的 hermes-agent-lite 還沒做 router-level 的 RL 主訓練，但 Castform 給了兩個**廉價可以立刻驗的具體下一步**——(1) 在 `castform` / `benchmax` repo 的 `examples/neon_rag` reference 之外，**先把 reward 函式改成「retrieval 對不對 + 主人手寫的 5 個 ground-truth 答案對不對」**，估算 token-only 路線訓練一次大約 wall-clock 2-4 小時（不需要自建 GPU cluster），可作為 hermes-agent-lite v0.2 的 router 訓練論文的 SLO 起點；(2) **「$0.03/次 multi-turn search」這數字直接拿來當 hermes-agent-lite 的 tool-router 預算上限**——若主人 router 一次 multi-turn turn 超過 $0.005（主人本地模型 + LMStudio 計價），就視為 router routing 失敗要降級。Castform 提供的啟示不是「改用 Castform」，而是「open-weights post-training 在 2026-08 已經是 1 天 prototype 級的 low-ceremony 工具，不要再卡在 frontier API 模型成本」。
