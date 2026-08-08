# SkillTrace: Multi-Trace Provenance Auditing for LLM-Agent Skill Reuse
- 原始連結：https://arxiv.org/abs/2608.05204
- 閱讀時間：2026-08-08

## 摘要

本文直面 LLM-agent 生態正在急速形成的「技能市場化（skill marketplace）」現實——技能不再是單一模組的程式碼，而是由 metadata、自然語言指令、程式碼、工具、參考檔與操作流程組成的混合模態包。當技能可被上架、被下游 agent 重用時，「這個 skill 是不是抄來的」「誰的邏輯被誰複用了」就成了市場治理的核心問題。

**現有方法的盲點：**
- 傳統 code clone detector 只能看單一模態的 source code，無法跨文字、程式碼、執行流程判定。
- Whole-package similarity 對齊整包向量比對，會漏掉「只保留一部分、其餘改寫」的部分重用。
- 結果是：當重用者刻意改寫 code 但保留 prompt 與操作流程時，現有機制幾乎抓不到。

**SkillTrace 的解法：三軌跡（Multi-Trace）來源審計**
1. **Expression Trace**：捕捉技能中被寫成自然語言的部分（prompt、指令、metadata 描述）。
2. **Implementation Trace**：捕捉程式碼片段、工具呼叫、API 引用等實作層指紋。
3. **Operational Trace**：以 *Skill Operational Graph（SOG）* 表示——activation、procedure、resource-flow 結構，記錄技能實際被呼叫時的運作拓譜。

**運作方式：**
- 引入時：LLM 只介入一次，萃取 Operational trace 並快取其餘 trace 為確定性指紋。
- 稽核時：trace 之間用確定性方式比對，並對每條 trace 對照 same-function strict negatives 做校正，最後彙整出哪一條 trace 支撐了「reuse」的判定。
- 在 SKILLTRACE-BENCH（100 個 marketplace 錨點、820 個轉化後的 reuse 正例、751 個負控制）達到 **AUROC 0.938、F1 0.898**。
- 在 36,446 個技能的 wild audit 中，trace-attributed evidence 能挖出 repo-level baseline 漏掉的「可行動重用審查佇列」。

**為什麼重要：** 把「技能重用偵測」從單純的程式碼相似度，升級成跨模態的「provenance audit」。這個研究對正在搭建 skill marketplace 或 multi-agent 平台的工程團隊特別有參考價值。

## 3W1H 分析
- **What（做了什麼/主題）**:
  SkillTrace 提出一個三軌跡（Expression / Implementation / Operational）的 provenance 審計框架，把 LLM-agent 的 skill 重用偵測從「比程式碼相似度」升級到「跨模態＋操作流程」的來源追溯，並用 Skill Operational Graph 把技能被執行時的拓譜結構化，於 benchmark 與 36k 規模 wild audit 都驗證有效。
- **Why（為什麼重要）**:
  主人目前正在用 Hermes Agent / WebUI 搭下游 air-gapped lite 平台，memory 裡已反覆出現「skill marketplace」「skill reuse」「skill curator」相關字眼。當 skill 變成可上架、可被多個 agent profile 共用的商品，reuse 的歸因、抄襲與授權問題會變成市場治理基本盤——這篇直接命中「如何讓 skill 可審計、可追溯」這個即將到來的痛點。
- **How（如何運作/實作）**:
  - 三條 trace 各用不同表示：文字用自然語言指紋、實作用程式碼向量、操作流程用 SOG 圖結構。
  - LLM 只在 ingestion 時介入一次抽 Operational trace，audit 階段全部走確定性比對，校正用 same-function strict negatives 對齊。
  - 比對結果彙整成「哪一條 trace 支撐了 reuse 判定」，方便人工覆核；wild audit 顯示這比整包相似度更會挖到需要審查的可疑重用。
- **Insight（個人心得）**:
  咱特別欣賞這篇把 LLM 的角色從「全程參與審計」縮成「只在 ingestion 時萃取一次 Operational trace」——audit 階段完全確定性。這正合主人一貫的設計哲學：**昂貴 / 不確定性高（LLM）的活集中做一次，後續比對與校正全走便宜、可重現的路徑**。套到下游 horo-agent 的 skill 治理上，等於是：skill 入庫時花一次 LLM 萃取 metadata 與操作圖，之後所有 reuse 偵測、pin/unpin、curator 自動分類都可以走確定性比對，避免每次稽核都要燒 token 也避免 audit drift。對主人正在做的 enterprise-lite / air-gapped 場景，這種「昂貴運算做一次、便宜運算跑 N 次」的取捨，比純靠 LLM-as-judge 的方案更貼合成本與可重現性要求。