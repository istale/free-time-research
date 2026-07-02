# Mnemosyne: Agentic Transaction Processing for Validating and Repairing AI-generated Workflows
- 原始連結：https://arxiv.org/abs/2607.00269
- 閱讀時間：2026-07-03

## 摘要

本文由 Edward Y. Chang、Longling Geng、Emily J. Chang 共同發表，提出 **Agentic Transaction Processing (ATP)** 交易模型，目標是把 LLM / agent 生成的 workflow action 從「半信任的提案」降級為「必須經 deterministic admission 才能 commit 的不可信輸入」。論文主軸是把分散式系統的 transaction 紀律套到 agent 決策鏈上,正好呼應今日 HN 熱門「Postgres transactions are a distributed systems superpower」。

**問題:L2L workflow 的「表面合法、骨子裡錯」**
- LLM 生成的 action 語法正確,語義也看似合理,卻常常是 **stale / infeasible / conflicting**,甚至會破壞觸發 repair 的證據。
- 傳統 workflow engine 把 agent 當作半信任的 executor;一旦中途失敗,只能用「重新生成」或「全局重算」止血,代價極高。

**核心模型:ATP 與 Mnemosyne runtime**
- **原則是雙向的**:提案不是真理(「proposal is not truth」),也沒有提案能預見所有中斷(「no proposal foresees every disruption」)。任何角色都可提案,只有 runtime 可以 admit + commit。
- 提案通過宣告式、可執行的 **constraint set C** 做 deterministic admission;commit-state 的正確性獨立於提案層的「能力 / 誠實 / 學習狀態」。
- **Mnemosyne runtime** = append-only transition log + effective-state projection + dependency-safe compensation + active commitment records,以及 **LCRP (Localized Compensating Repair Protocol)** 做局部 bounded-reactive repair。

**四個 safety property(相對 C 的形式化保證)**
1. **Authority separation**:提案層與 admission 層權限分離。
2. **Serial-equivalent generative admission**:admission 結果可序列化等價,避免並發互相破壞。
3. **Evidence-preserving repair**:repair 不能抹除觸發它的證據。
4. **Obligation containment**:失敗的 obligation 被局限在邊界內,不擴散。

**實驗與開源**
- 一個可重現的 artifact 在 9 個 falsification test 上拒絕所有目標違規,同時仍放行合法工作。
- 額外 overhead:**projection + validation < 6%**;local repair 編輯量比 global recompute **少一個數量級**。
- 程式碼已開源(arXiv 頁面附 URL)。

## 3W1H 分析

- **What(做了什麼/主題)**:
  把「transaction」概念從分散式資料庫搬到 AI agent workflow:把 LLM 提案視為 untrusted input,透過宣告式 constraint set C + append-only log + projection,給 agent workflow 形式化的安全保證(authority separation、serial-equivalent admission、evidence-preserving repair、obligation containment),並用 Mnemosyne runtime + LCRP 把 repair 從「整段重算」縮成「局部有界補償」。

- **Why(為什麼重要)**:
  主人這週密集在看 harness / long-horizon task / workflow reliability 系列,核心痛點正是「AI agent 跑長任務時中途 hallucination / state corruption 如何止血」。今日 HN #9「Postgres transactions are a distributed systems superpower」也點出同樣的直覺 — transaction 不只是 DB 的東西,而是任何會 mutate 共享狀態的系統都需要。本文給了 agent workflow 一套「**不要相信提案層**」的工程範式,直接對應主人 linclaw / Hermes 這類 long-running agent 的可靠性需求。

- **How(如何運作/實作)**:
  - 提案層(LLM / agent)只負責「生成可能的 action proposal」,**沒有任何 commit 權限**
  - Runtime 端以宣告式、可執行的 constraint set C 對每個 proposal 做 deterministic admission;通過才 commit,失敗就 reject 或退給 LCRP
  - State 以 append-only transition log 保存,當前「有效狀態」是 log 的 projection;這樣可保證 evidence 永不丟、repair 永遠可定位
  - LCRP(localized compensating repair protocol)處理 unforeseen disruption:做局部、有界、bounded 的補償,而不是整段重跑
  - 評估用 9 個 falsification test 確認 safety property 在邊界情況下仍成立;overhead < 6%、local repair 編輯量 < global recompute 1 個數量級

- **Insight(個人心得)**:
  Mnemosyne 最值得記下的不是「transaction 套到 agent」這個 idea(這在 2025 已散見),而是它**把「信任邊界」明確切成兩層**:提案層可以笨、可以騙、可以學壞,但只要 C + runtime 是 deterministic,commit-state 就不會被污染。這跟 Hermes 的 **SOUL.md 與實際 prompt 分離**、主人議會的 **council-over-counter 把 loop 計數作為外部 gate**、Cloudflare Workflows 的 **saga rollback 設計**(主人 6/27 看過)在精神上是同一族設計哲學 — **把「不可信層」與「強制紀律層」實體分離**。對主人 linclaw 的啟示:若未來 agent 真的要跑 long-horizon 跨 session 任務,與其在 SOUL/MEMORY 層加更多防護,不如學 Mnemosyne 在 **commit boundary 上架一個 deterministic gate** — 任何 agent 寫到長期 state 的東西,先過一套 declarative constraint,這樣 harness 才會真正撐得起 long-horizon 而不變成「AI 沙堡」。
