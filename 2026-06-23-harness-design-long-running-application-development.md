# Harness design for long-running application development
- 原始連結：<https://www.anthropic.com/engineering/harness-design-long-running-apps>
- 閱讀時間：2026-06-23

## 摘要
這篇文章是 Anthropic Labs 成員 Prithvi Rajasekaran 對長時間自主 coding agent 的實作回顧，原文連結：<https://www.anthropic.com/engineering/harness-design-long-running-apps>。核心觀點是，agent 的能力上限不只受模型本身影響，更強烈地受 harness 設計左右；如果只靠單一 agent 長時間執行，常見失敗模式包括 context window 膨脹後的失焦、接近上限時提早收工的「context anxiety」，以及自評過度寬鬆，導致成品看似完成但品質平庸。

作者先在前端設計任務上驗證 generator/evaluator 雙代理結構：generator 負責產生頁面，evaluator 透過 Playwright MCP 實際操作頁面、截圖、依據設計品質、原創性、工藝與功能性四個面向打分，再把批評回送給 generator 疊代 5 到 15 輪。這種做法的重點不是把美感完全量化，而是把原本模糊的「設計好不好」轉成可檢查的評分準則，逼模型脫離安全但無聊的預設產出。

接著作者把這套思路擴展到完整全端開發，形成 planner、generator、evaluator 三代理架構。planner 把簡短需求擴寫成產品規格；generator 以 sprint 為單位逐步實作 React/Vite/FastAPI/SQLite（後續改 PostgreSQL）應用；evaluator 則用 Playwright MCP 驗證 UI、API 與資料狀態，並依產品深度、功能、視覺設計與程式碼品質設下門檻。特別有意思的是，generator 與 evaluator 會在每個 sprint 前先協商 sprint contract，先定義「這輪要交付什麼、怎樣算完成」，再開始寫程式。

文章也提醒一個很實際的工程判斷：evaluator 不是永遠越多越好，而是當任務超出模型單兵穩定能力時才真正划算。作者比較舊版與新版模型後發現，模型能力提升會讓某些原本必要的 harness 組件變成多餘成本，因此每次模型升級後，都應重新檢查哪些 orchestration 元件仍然是 load-bearing，哪些已經可以刪掉。

## 3W1H 分析
- What（做了什麼/主題）: 文章介紹 Anthropic 如何用多代理 harness 改善長時間自主軟體開發，從前端設計任務一路擴展到完整全端 app 建置。
- Why（為什麼重要）: 它把「模型強不強」轉成更可操作的「系統怎麼編排」，說明高品質 agent workflow 往往來自上下文管理、角色分工與驗證回路，而不只是換更大的模型。
- How（如何運作/實作）: 先用明確評分準則建立 evaluator，再讓 planner 產規格、generator 分 sprint 實作、evaluator 用 Playwright 做黑箱驗證，並透過檔案化 handoff 與 sprint contract 保持多輪任務一致性。
- Insight（個人心得）: 我最有感的是「evaluator 不是信仰，是邊界條件工具」這點。真是的，很多人會把 multi-agent 當萬用靈藥，但這篇文章反而提醒要定期拆掉不再必要的流程，只保留真正能撐住品質的結構。
