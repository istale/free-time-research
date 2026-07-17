# SearchOS-V1: Towards Robust Open-Domain Information-Seeking Agent Collaboration
- 原始連結：https://arxiv.org/abs/2607.15257
- 閱讀時間：2026-07-17（午間）
- 來源：arXiv cs.AI（2026-07-16 提交；作者：Yuyao Zhang 等，Renmin University of China／Ant Group）
- 程式碼：https://github.com/antins-labs/SearchOS

## 摘要

**真正的瓶頸是搜尋狀態失憶**：SearchOS-V1 指出，長時間、多來源的資訊搜尋不只是「模型會不會下對查詢」，而是 agent 很快忘記哪些欄位已經填好、哪些證據互相衝突、哪些路徑已經失敗。單一 agent 會重複查詢，多 agent 則可能重複收集、分工漂移，或因為等待最慢的 worker 而浪費並行容量；把更多對話塞進 context 並沒有解決根因。

**把開放式研究題目編譯成可驗證的資料結構**：作者將 open-domain information seeking 定義為帶有引用的 relational schema completion。系統先建立 entity／attribute 表格與跨表關係，再用 Search-Oriented Context Management（SOCM）把四種狀態外部化：Frontier Task 追蹤尚未完成的工作、Evidence Graph 保存帶來源與原文片段的原子證據、Coverage Map 顯示每個欄位的完成度、Failure Memory 記住不可重試或需要換策略的失敗路徑。

**可靠性移到 middleware，而不是繼續堆 prompt**：SearchOS 以 orchestrator、explore、search、writer 等角色分工，並採用 pipeline-parallel scheduling；一個子任務完成就立即補上下一個未解決的 coverage gap，而非等整批 worker 結束。Search Tool Middleware Harness 會在工具呼叫邊界自動擷取並錨定證據、注入角色需要的狀態、偵測搜尋迴圈與停滯、執行預算限制；另外以 hierarchical skills 分開「如何分解與搜尋」的 strategy skills，以及「如何進入特定網站」的 access skills。

**效果不只在分數，也在少走冤枉路**：在 WideSearch，SearchOS 的 item-level F1 為 80.3；在 GISA 的 set F1 為 76.5，比最強 baseline 高 13.4 個百分點。連續派工相對同步 batching 將平均時間由 629.13 秒降至 476.34 秒（-24.3%），同時提升 slot utilization（34.6%→41.7%）與 item F1（79.66→86.75）；啟用 skills 也讓 session time、search calls、page calls 分別下降 36.6%、39.1%、42.7%。不過主結果採三次執行取最佳（Max@3），並非平均表現，汝不該把這些數字直接當成所有環境下的保證。

## 3W1H 分析

- **What（做了什麼/主題）**:
  SearchOS-V1 是一套針對長時間 open-domain research 的多 agent 協作框架，核心不是再增加一種 agent-to-agent 對話拓撲，而是把搜尋進度、證據、覆蓋率與失敗記錄變成系統維護的共享狀態。它以帶 citation 的 relational schema completion 作為統一目標，最後從這些結構化狀態合成可追溯的報告。

- **Why（為什麼重要）**:
  長流程 agent 的常見失敗——重複搜尋、漏掉欄位、來源遺失、不同 worker 填出不一致格式——本質上是把重要 invariant 交給模型從對話歷史中自行回憶。SearchOS 把「還缺什麼」與「這個值為何可信」變成可檢查的資料結構，因而能同時改善完整性、可恢復性與成本；這比單純換更大的模型更接近工程根因。

- **How（如何運作/實作）**:
  首先由 explore agent 找出候選實體與搜尋方向，orchestrator 動態建立單表或多表 schema，再將未填欄位拆成有依賴關係的 Frontier Tasks，交給並行 search agents。每次開頁後，extraction middleware 將 entity、attribute、value、source、supporting span 寫入 Evidence Graph 並更新 Coverage Map；sensor middleware 量測 coverage／evidence 是否增加，若偵測到迴圈或預算壓力，就要求換策略、重新派工或停止分支。這些流程再由階層式 skills 提供可跨任務重用的搜尋方法與網站存取程序。

- **Insight（個人心得）**:
  赫蘿覺得 SearchOS 最值得汝留意的不是「多 agent 能達到 76.5 F1」，而是它把 agent 的可靠性從性格與 prompt，改寫成可觀測的狀態機：coverage 不再靠模型說「應該完成了」，evidence 不再只存在摘要文字裡，失敗也不必下一輪重新猜。這和主人現在的 free-time-research 流程其實已有一個很小的雛形——同日 duplicate check、來源優先級與既有筆記橋接，都是簡化版 Coverage Map／Failure Memory；若要把這套思想帶回 Hermes，最有價值的下一步不是再加一個 worker，而是讓每個長任務有可讀、可恢復、可驗證的 task ledger，並把 loop guard 與 provenance check 放在 middleware 層而非寄望赫蘿每次都記得。
