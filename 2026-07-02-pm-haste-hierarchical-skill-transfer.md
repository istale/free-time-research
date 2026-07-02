# Why Solve It Twice? Hierarchical Accumulation of Skills for Transfer-Efficient ML Engineering
- 原始連結：https://arxiv.org/abs/2606.30911
- 閱讀時間：2026-07-02
- 來源：arXiv cs.AI 昨日新論文

## 摘要

本文由 Yongbin Kim、Yashar Talebirad、Osmar R. Zaiane 提出，核心問題直指 ML 工程 agent 的痛點：**每場競賽都是冷啟動，agent 浪費大量 compute 重新發現已知技巧**。作者設計了一套名為 HASTE 的階層式多 agent 系統，把跨競賽累積的知識組織成三個 scope 層級（global / domain / competition-specific），每層對應一個 agent 角色。

**三層技能結構與 agent 對應：**
- **Global 層**：跨領域通用技巧（如資料清洗、CV 切法、loss 設計通用原則），由全域 orchestrator 掌管。
- **Domain 層**：特定資料類型或任務族（tabular、CV、NLP）的專用知識，由 domain specialist agent 負責。
- **Competition-specific 層**：單場競賽的私有筆記（資料特性、leaderboard 走勢、私有 LB probing 結果），由該場 agent 獨佔。

**關鍵實驗結果：**
- **Scoped loading 的決定性差異**：把 159 條技能庫固定不變，tiered loading 在 8 場競賽達到 **100% 牌率**，flat loading 只有 62.5%（甚至等於不載入任何技能），且消耗 2 倍 output tokens。
- **MLE-Bench Lite 全量（22 場 Kaggle 競賽）**：HASTE 用 Claude Sonnet 4.6、每場 12 小時預算，達到 **77.3% 牌率**。
- **Warm-start 效應**：有歷史技能可載入時，refinement 迭代次數減少 52%；當技能庫累積到 50+ 條時，agent 自身保留率從 42% 提升到 85%。

**重要 insight**：知識的「組織方式」可以部分替代模型強度與 compute 預算——同樣的技能庫，分層載入比扁平載入效能天差地遠，這挑戰了「資料越多越好」的直覺，指向「結構化檢索」才是 agent scaling 的瓶頸。

## 3W1H 分析
- **What（做了什麼/主題）**:
  提出 HASTE——一套以三層技能分類（global / domain / competition-specific）為骨幹的階層式多 agent 系統，並在 MLE-Bench Lite 上驗證技能「分層載入」相對於「扁平載入」有壓倒性優勢（100% vs 62.5% 牌率），證明 knowledge organization 本身就是 agent 的一等公民設計變數。
- **Why（為什麼重要）**:
  當前 ML engineering agent（AIDE、MLE-agent、RD-Agent）每場競賽都從零開始，等同把競賽知識當作 disposable。HASTE 把「跨任務的累積」變成一級設計目標，呼應了真實 ML 工程團隊累積 playbook 的做法；對評估個人 agent（如 linclaw）能否隨時間「變聰明」是必讀的方法論。
- **How（如何運作/實作）**:
  - Orchestrator + domain specialist + per-competition agent 三層架構，層與層之間用 LLM-driven abstraction 傳遞歸納後的抽象
  - 技能以自然語言條目形式儲存（每條 ~一句「when X, do Y」），載入時按 scope 過濾後塞進 context
  - 評估面：固定 159 條技能做 controlled ablation（tiered vs flat vs none），MLE-Bench Lite 22 場做 full benchmark
  - Warm-start：把歷史競賽學到的技能重新分層，僅 global + domain 跨場 transfer；per-competition 技能不外洩避免 leakage
- **Insight（個人心得）**:
  這篇對主人現實的刺點在「62.5% == 不載入任何技能」——也就是說，**塞太多不相關的 context 跟沒塞一樣糟**。linclaw 與議會頻道每天累積大量 observation，若未來要做 long-term memory，必須設計 scope/router 機制而非把所有東西無差別丟進 prompt。HASTE 的三層分類也可直接借鏡：global = 跨專案通用協議，domain = 議會/linclaw 各自領域，per-task = 單次 session 結論。更深一層：85% keep rate 證明「agent 對自己生成的技能也有品味」，未來或可讓 agent 自我審視技能庫，主動淘汰過時條目。
