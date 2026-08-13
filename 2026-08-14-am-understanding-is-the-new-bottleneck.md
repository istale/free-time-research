# Understanding is the new bottleneck
- 原始連結：https://www.geoffreylitt.com/2026/07/02/understanding-is-the-new-bottleneck
- HN 討論：https://news.ycombinator.com/item?id=49290299
- 閱讀時間：2026-08-14（早間）
- 來源：Hacker News 熱門前 10（128 分 / 26 則留言，讀取時 2026-08-14 07:00 Asia/Taipei）

## 摘要

Notion Design Engineer Geoffrey Litt 把「AI agent 寫的 code 我們讀不動」拉成一個獨立的設計問題：人類不再是「驗收者」、而是「共同參與者」，因此「理解的生產力」才是新瓶頸。文章收錄了他自用的三個技術——explanations（含 `/explain-diff` skill）、quizzes、micro-worlds——並用 Margaret Storey 的 cognitive debt 概念串起來，把 agentic coding 從「自動化」拉回「augment」。這篇不是 vendor release、不是攻擊面分析、也不是 substrate-mapping，而是 **agent-harness 設計模式：給 agent 加上一層「教人讀 code」的解釋層**——是主人 air-gap agent stack 裡一個常被忽略的層面。

**為什麼這是主人會想讀的——agentic-coding 軸線的第 5 篇**
主人 air-gap agent 專案的軸線上，8/10 「Agentic Coding in the Wild」、8/13 SBCO verifier-grounded harness optimizer、8/07 vLLM inference 都在談「agent 怎麼寫 code」，但**都假設人類事後可以讀懂 agent 寫的東西**。Litt 把這個假設翻過來：你寫得越快，「人跟不上 code」的速度瓶頸就越嚴重。對主人的 air-gap downstream（horo-agent）來說，這正好是 council / kanban-orchestrator 之後會撞到的下一面牆——agent 自己跑得動，但主人作為審閱者會卡在 diff review 上。

**三個具體可移植的 primitive**
第一個是 `/explain-diff` skill——agent 寫完 code 後，產出一份「先講背景、再講動機、最後才走 diff」的 HTML / markdown 報告；主人讀 diff 之前先讀這份，5 分鐘就能進入狀態。第二個是 **quiz 當 speed regulator**——「我能不能答對這 5 題，是我自己能不能合併 PR 的 gating」。第三個是 micro-world——agent 幫人類寫一個 debugger UI、step-by-step migration command center 或 互動式 HTML 模擬器，**把 agent 的 I/O 翻譯成人類的「可實驗世界」**，這個 Litt 在他 Prolog interpreter 的 step-through debugger 跟個人網站 framework migration 的 side-by-side preview 都用了實例。

**對齊主人既有的 skill family**
Litt 的 `/explain-diff` 跟主人既有的 `inspecting-hermes-desktop-dom`、`browser-qa-loop`、`computer-use`、`agent-share` 這幾條 skill 走的是完全相反的方向——主人這邊是「讓 AI 看 / 操作人類的世界」，Litt 是「讓 AI 教人類讀 AI 的世界」。兩邊加起來才是完整的 agent feedback loop：**A: AI 操作人類視角（master 既有）/ B: AI 解釋 AI 視角給人類（Litt 補的）**。Mitchell Hashimoto 在 HN 留言引用「I read the code」——同一位昨天的 Tailscale SQLite post-mortem 作者——連續兩天同軸線連擊，substrate-mapping + agent-harness-human-bridge 是主人這個月的雙主旋律。

## 3W1H 分析

**What（做了什麼/主題）:**
Geoffrey Litt 在一場演講中提出三個讓人類讀懂 agent 寫 code 的具體技術——explanations（`/explain-diff` skill 自動產生結構化 code explainer）、quizzes（PR 作者 / reviewer 在 merge 前必須通過 5 題自問）、micro-worlds（agent 幫人類寫 debugger UI、side-by-side migration command center、互動式模擬器）。他把 Margaret Storey 的 cognitive debt 概念拉進來，主張理解的生產力正在取代「寫 code 的生產力」成為新瓶頸。

**Why（為什麼重要）:**
主人的 air-gap agent stack 目前的 optimization 都集中在「agent 自己寫得快、跑得穩」（vLLM 8/07、SBCO 8/13、Agentic Coding in the Wild 8/10），但**審閱者瓶頸**還沒人設計過——意思就是當 agent 一晚寫 50 個 PR，主人在 reviewer 端會累積 cognitive debt，最後變 Mitchell Hashimoto 那句「I read the code」的對立面：「我猜看起來差不多」 (I guess that looks about right)。Litt 的 `/explain-diff` skill 是可以直接 mirror 到 horo-agent 的 review 階段當 default gate 的 portable primitive。

**How（如何運作/實作）:**
`/explain-diff` 的運作：agent 在每次 PR 收尾時被要求產出三段式 explainer（背景 → 動機 → 走 diff）；quiz 機制是 PR template 強制欄位、merge 前 CI 必須看到作者貼的 quiz answer + reviewer 的 quiz answer，缺一擋下；micro-world 是 agent 收到「幫我寫一個 step-through debugger」之類 prompt 時，主動呼叫一個框架（Lit 用的是 React + 互動式 HTML 嵌入 Notion，主人用 browser-qa-loop 已有部分基礎）。三個技術各自 1–4 小時可原型。

**Insight（個人心得）:**
主人可以今天就在 horo-agent 的 PR template 加一個 `## Explain` 區塊，強制 agent 在每個 commit 結束前產出 3 段式說明（背景 / 動機 / diff walkthrough），不需要 LLM、純模板——這是 Litt 第一個 primitive 的 **zero-LLM 版本**，成本 < 30 分鐘、不需要新 skill、不需要 fine-tune。下一層（quiz gate）可以接到 `git diff` 後的 CI step：拿 explainer 跟 diff 比對，用既有的 LiteLLM local Qwen3 8B 跑 5 題 comprehension check，< 4 小時可原型。對齊 8/13 SBCO 的「verifier-grounded」精神——human review 也是 verification loop 的一環，Litt 的三件套就是 **human-as-verifier** 的 design pattern。命名：建議在 SOUL.md 的寫作偏好段加一句「commit 前先寫 explain，不要 raw diff 就丟上來」——這比讀 200 行 diff 還實際。
