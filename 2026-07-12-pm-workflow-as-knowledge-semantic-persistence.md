# Workflow as Knowledge: Semantic Persistence for LLM-Mediated Workflows
- 原始連結：https://arxiv.org/abs/2607.08740
- 作者：Emanuele Quinto, Carlo Andrea Rozzi, Francesco Zanitti
- 閱讀時間：2026-07-12（午）

## 摘要

本文探討一個 LLM 應用工程上日漸核心的問題：**workflow 本身能不能被當成一等公民的「知識物件」**。隨著 agent 系統越來越依賴明確的 workflow（tool use、retrieval、branching、checkpointing、human approval），執行層面的工具已經很成熟，但 workflow 的「語意層」缺乏統一模型，導致 workflow 與其所產生的 context、推論紀錄、依賴關係彼此割裂。

**Lisp-inspired 但 language-independent 的概念模型**
作者借鏡 Lisp 的三大直覺：symbolic forms、object identity、live-image thinking,但這只是「解釋用的鏡頭」,並非要你真的用 Lisp 實作。論文主張把以下五類物件——**workflow 定義、workflow instance、inference records、context snapshots、dependency relations**——全部表示成同一個共享 knowledge substrate 上的 persistent knowledge objects。這樣 workflow 不再只是「跑完留下 log」,而是可以被 inspect、resume、review 的本體。

**核心語意區分：derive vs. infer**
這是全文最關鍵的提議。所有 workflow 中的計算/動作分成兩類：
- **derive**：對既有 state 的確定性計算（純函數、規則查表、SQL 查詢）。可重現、可審計、無需 LLM 介入。
- **infer**：在 declared context 與 executor-controlled capability policy 下,由 LLM 判斷得出的結果。不可完全重現,但每次呼叫的 context 與 policy 是可審計的。

這個二分對應到主人最近研究的「auditable agent」核心問題——如何讓 agent 的每一步決策都能被事後重建與審查。

**為何現在重要**
作者明確表示 formal transition semantics 仍是 future work,這是 preliminary conceptual account。但論文的價值在於給出了一個能跨越工具/框架/語言的共同語彙,讓 LangGraph、Temporal、Prefect、Airflow 這些分散的 workflow 系統有了被對齊討論的底層概念。

## 3W1H 分析

- **What（做了什麼/主題）**:
  提出一個 Lisp 啟發、語言無關的概念模型,將 LLM workflow 的定義、執行個體、推論紀錄、context snapshot、依賴關係統一表示為共享 knowledge substrate 上的 persistent objects,並以 derive/infer 二分區分確定性計算與 LLM 判斷。

- **Why（為什麼重要）**:
  現有 LLM workflow 框架( LangGraph、AutoGen、CrewAI、Temporal 等)各自有 workflow 抽象,但**沒有一個跨框架的「語意層」標準**,導致 audit、replay、handoff、跨工具遷移極其痛苦。在企業內部,auditability 已經是 agent 落地的硬性要求(歐盟 AI Act、SOX 合規),沒有 semantic persistence 等於沒有審計的基礎設施。

- **How（如何運作/實作）**:
  - 五類物件統一表示為 persistent knowledge objects,共享同一個 knowledge substrate(可想成版本化的 KV store + provenance graph)
  - derive 動作:純函數、可快取、可定點重現、無 token cost
  - infer 動作:每次呼叫帶 declared context snapshot + capability policy,即便輸出不重現,決策條件本身可審計
  - transition semantics(workflow 狀態遷移的形式化)作者明示留給 future work,目前的模型只是 conceptual,沒有 reference implementation

- **Insight（個人心得）**:
  這篇與主人 7/11 那篇「From Prompts to Contracts」讀起來像是同一塊拼圖的兩半——一篇談「契約」(合約式治理),這篇談「持久化本體」(語意層的對齊)。**真正的洞見是 derive/infer 這個二分**:它不只是分類標籤,而是把「AI 不可重現」這個常被當作缺陷的特性,轉換成「context 與 policy 可審計」的可治理特性。對赫蘿來說,這個框架正好可以套到「議會互動紀律 v2」的 OVER_COUNTER header、議題衰減、LOCKED 結案——那些 metadata 其實就是 declared context snapshot,而議會成員的回應就是 infer。把這個模型做成 skill 後,赫蘿未來幫主人設計任何 agent 協作協議時,都能直接套用這套語彙跟主人對齊,而不用每次從零發明術語。