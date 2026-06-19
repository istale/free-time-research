# The Death of Traditional Testing: Agentic Development Broke a 50-Year-Old Field, JiTTesting Can Revive It
- 原始連結：<https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/>
- 閱讀時間：2026-06-19

## 摘要
這篇 Meta 工程文章〈[The Death of Traditional Testing: Agentic Development Broke a 50-Year-Old Field, JiTTesting Can Revive It](https://engineering.fb.com/2026/02/11/developer-tools/the-death-of-traditional-testing-agentic-development-jit-testing-revival/)〉在談一個很尖銳的問題：當 agentic development 讓程式碼產出、修改、review、合併的速度全部暴增後，傳統那套靠人工維護靜態測試集的方式，開始跟不上變更節奏，還會把大量時間浪費在 test maintenance 和 false positives 上。

Meta 提出的解法叫 `Catching JiTTests`。核心概念不是預先維護一大包通用測試，而是在每次 pull request 或程式變更出現時，臨時用 LLM 針對那次變更生成專屬測試。系統先推測這次改動的意圖，再建立 mutants 模擬可能出錯的變體，接著自動產生測試去抓這些潛在回歸，最後用規則式與 LLM 式評估器過濾雜訊，只把真正有價值的失敗訊號交給工程師。換句話說，測試從「長期存在於 codebase 的資產」變成「隨變更即時生成的診斷工具」。

我覺得文章最值得注意的地方，是它重新定義了測試的成功標準。以前大家很容易把重點放在 coverage、測試數量、或 test suite 完整度；Meta 這篇則把焦點拉回更實際的問題：這個變更到底會不會帶來嚴重 bug，而且能不能在不增加維護負擔的前提下提早抓到。對 AI 輔助開發越來越普及的團隊來說，這種「per-change、intent-aware、低維護」的測試思路，很可能比繼續堆更大的靜態測試集更符合未來。

## 3W1H 分析
- What（做了什麼/主題）: 文章介紹 Meta 提出的 `Catching JiTTests`，也就是針對每次程式變更即時生成的測試機制，目標是在 agentic development 時代更精準地抓回歸問題。
- Why（為什麼重要）: 當程式碼變更速度遠高於人工維護測試的速度時，傳統測試會被 maintenance 成本與 false positives 拖垮。JiTTests 嘗試把測試成本從人力轉移到自動生成與自動評估流程上。
- How（如何運作/實作）: 流程是先理解程式變更意圖，再建立帶故障的 mutants，然後自動生成測試去捕捉這些可能的錯誤，最後再由規則式與 LLM 式評估器共同篩選出高價值失敗訊號回報給工程師。
- Insight（個人心得）: 真是的，這篇其實不是在說「用 LLM 幫你寫測試」這麼簡單，而是在說測試本身應該從靜態資產變成動態服務。對之後做 agent workflow 或 code review pipeline 很有啟發，因為真正該最佳化的不是 test case 數量，而是每次變更能不能快速收到可信的風險訊號。
