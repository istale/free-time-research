# AI handles incidents, engineers lose touch with their systems

- 原始連結：https://www.sylvainkalache.com/blog/ai-handles-incidents-engineers-lose-touch-with-their-systems
- 作者：Sylvain Kalache（Rootly AI Labs lead、前 LinkedIn SRE、Holberton School 共同創辦人）
- 發布日期：2026-09-04
- HN 討論：https://news.ycombinator.com/item?id=49574167
- 閱讀時間：2026-09-05（晚間）
- 來源：Hacker News 熱門第 1 名（讀取時 63 分 / 39 則留言，2026-09-05 17:30 CST）

## 摘要

**這篇文章把「AI 自動化」從常見的生產力敘事反轉成「comprehension debt」風險。** Sylvain Kalache（Rootly AI Labs lead、前 LinkedIn SRE）開宗明義：AI SRE 工具現在能 inspect alerts、form hypotheses、query telemetry、correlate deployments、甚至自己 implement fix——「但我們正在失去與自己系統的接觸」。Routine incidents 正是人類建立「系統如何運作與失敗」的直覺的安全練習場，AI 把這些練功機會全部拿走之後，**當一個 ambiguous、從未見過的高嚴重性 incident 發生、自動化無法處理時，當班工程師會出大問題**。

**Bainbridge 1983 的「Ironies of Automation」是這篇文章的理論骨幹。** 人因工程研究者 Lisanne Bainbridge 在 1983 年的論文中已經預言：自動化減少 operator 的 routine 練習機會，卻同時把責任推給他們處理更異常的情況——因此「自動化時代的 operator 需要比自動化前**更高**的技術與訓練」。Kalache 把這個 43 年前的人因學命題映射到今天的 AI SRE，並提出一個具體預測：**未來幾年，平均 MTTR 會因 AI 下降，但 complex incident 的 resolution time 會因為當班工程師失去系統手感而暴漲**——這是平均數被掩蓋的雙峰分佈。

**解決方案不是「少用 AI」，而是建立「incident simulator」作為 on-call readiness 的常態訓練。** Kalache 引用航空業的對照組：現代渦輪發動機 in-flight shutdown 機率低於 1/100,000 飛行小時，多數機長職業生涯從未在真實飛機上遇過——但他們仍必須能在 30 秒內正確處置（TransAsia Flight 235 就是反例）。Rootly 與 Uptime Labs 合作實作「incident simulator」：工程師坐上 simulated e-commerce outage 的 incident commander 座位、用真實 observability 工具、在 Slack 上跟 LLM-powered stakeholders 協調。Tabletop exercises 跟 chaos engineering 不是新東西，但在 LLM 時代它們從「nice to have」變成「on-call readiness 的必要條件」。

**「comprehension debt」是這個故事對工程組織最值得記住的新詞。** Kalache 把「程式碼債務」、「技術債務」的比喻延伸到「理解債務」——LLM 做越多我們的工作，**組織對自家系統的整體理解就跟實際系統行為拉開越來越大的距離**。這不是個人問題、是組織問題，也是為什麼 incident simulation 要變成定期輪值的、而非可選的。

## 3W1H 分析

**What（做了什麼/主題）**：Rootly AI Labs lead Sylvain Kalache 引用 1983 年人因學經典「Ironies of Automation」，論證當前 AI SRE 工具的快速普及會在中期造成工程組織的「comprehension debt」——routine incidents 被自動化解決、但這些正是人類建立系統直覺的安全練習場，**練習機會被拿走、責任沒有減少**，最終導致 complex incident 的 resolution time 暴漲。解方是借鏡航空業的 incident simulator 文化：把 LLM-powered stakeholders + 真實 observability 工具組成的 simulated outage 變成 on-call readiness 的例行訓練。

**Why（為什麼重要）**：這個議題跟主人正在做的 hermes-agent / kanban orchestrator 直接對接——主人建構的正是「AI 處理 routine kanban dispatch、human 在 protocol violation / genuine blocker 時介入」的混合編制。Kalache 的警告等於在告訴主人：**如果 kanban 自動化的 dispatcher 把所有「routine 心智模型建立」都拿走了，那下次出現一個 dispatch schema 之外的 ambiguous blocker 時，主人的直覺反應時間會變長**。今天早上 EEBench（09-05-am）寫的是「verifier-grounded eval-as-RL-environment」——把決策變可驗證；今天這篇剛好相反方向——「自動化把實踐練習機會拿走了」。同一條 substrate 軸：可見性 vs 練習機會，**這兩個張力必須同時管理**，只押一邊的話組織會在另一邊爆掉。

**How（如何運作/實作）**：
- **診斷指標**：追蹤「complex incident（MTTR > 30 min）vs routine incident（MTTR < 5 min）」的雙峰分佈，而不是只看平均 MTTR——AI 自動化會拉低平均但推高尾端
- **訓練設計**：每季一次「incident commander simulator」輪值，使用 LLM-driven stakeholders 在 Slack 模擬 engineering leadership / customer support / on-call 等多角色場景，讓值班者練「在不完整資訊下做決定 + 跨角色溝通 + 即時下指令」
- **文化設計**：把 simulator 寫進 on-call readiness 的 SOP，而非鼓勵式「optional tabletop exercise」——主人在 hermes-agent-lite 可以把這個模式直接對應到「每週一次的 kanban-blocked scenario drill」：故意 inject 一個 protocol violation 進 board，讓 agent 練習走 `kanban_block(kind='needs_input')` 的路徑，而不是默默走 fallback
- **指標替代**：除了 kanban 完成率（主人已經在追蹤），新增「dispatch-gate false negative rate」——當 agent 把 routine 處理掉時，有多少是「真正的 routine」、多少其實是「agent 看不懂的 complex」被誤判為 routine

**Insight（個人心得）**：主人不需要擔心這篇文章的「失去系統手感」——你正好是反例：你堅持每一輪 kanban task 都要「真實跑 lint/build/test 取 exit code」才視為完成、堅持 live smoke > Kanban PASS、堅持 protocol violation 跟 network blocker 要分開回報，**這些都是 Bainbridge「operator 需要更高技能」在 hermes-side 的具體落實**。但有一個翻轉的提醒值得記下：當主人未來決定把 kanban dispatcher 再自動化一層（譬如「自動 heartbeat 30 秒內若 agent 沒進度就 escalate」），那層自動化本身也會變成主人失去練習「手動看 board、嗅到某個 task 已經悶燒 25 分鐘」的機會。**所以最便宜的 Layer 0 投資不是再加 dispatcher 規則，而是「每月手動 review 一個 failure postmortem + 親自跑一次 dispatch retry」**——這就是主人 kanban protocol 的 incident simulator。今天這篇剛好跟你早上的 EEBench 形成對偶：早上是「把決策變可驗證」、今晚是「把練習機會留給自己」——**同一個主人、同一個 substrate，兩條腿才走得穩**。
