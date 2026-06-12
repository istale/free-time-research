# How we built Cloudflare's data platform and an AI agent on top of it
- 原始連結：https://blog.cloudflare.com/our-unified-data-platform/
- 閱讀時間：2026-06-12

## 摘要
這篇 Cloudflare 工程文（[原文](https://blog.cloudflare.com/our-unified-data-platform/)）講的是他們如何把分散在 Postgres、ClickHouse、Kafka、BigQuery、R2 等多個系統中的資料，收斂成統一的內部資料平台 `Town Lake`，再在其上疊一個自然語言資料代理 `Skipper`。核心不是「再做一個聊天機器人」，而是先解掉資料分散、取樣資料不適合帳務與安全場景、權限與欄位治理混亂、以及知識只存在少數人腦中的問題。

`Town Lake` 本質上是 lakehouse 架構：以物件儲存搭配查詢引擎與 metadata layer，讓內部人員能用單一 SQL 介面查公司資料。同時它把治理放在預設路徑上，而不是事後補洞。新表預設不可查，必須先經過 `Skimmer` 掃描、欄位分類、審核與 allowlist 註冊後才能開放；未審核欄位即使存在，也不會從 `DESCRIBE` 或 `SELECT *` 裡洩漏出來。PII 也不是預設裸奔，而是 session 級 opt-in，開啟與查詢都會留下審計紀錄。

`Skipper` 則是建立在 Town Lake 上的 AI data agent。它會先找資料集與 schema、理解 lineage，再產生 SQL、送進 Trino、輪詢結果並回傳表格或圖表。如果查出來結果不對，還會根據上下文迭代修正查詢。文章裡最值得抄的地方不是「模型多強」，而是作者明說：少一點 micromanagement prompt、少一點重複工具，效果反而更好。高層 guidance 加上清楚的工具邊界，比一大串規定式 prompt 更能讓 agent 穩定產出可稽核答案。

文中也給了實際成效：帳務相關查詢已占 Town Lake 大宗流量，原本 200 到 300 行的 legacy SQL 收斂成約 5 行；一些以前要開 Jira 票排隊處理的資料問題，現在幾秒內就能由 Skipper 回答。這代表真正的價值不是「把 SQL 換成聊天」，而是把資料存取、治理、上下文與 agent workflow 一起產品化，讓資料探索從專家特權變成可授權的組織能力。

## 3W1H 分析
- What（做了什麼/主題）: Cloudflare 打造了統一資料平台 `Town Lake` 與其上的 AI 查詢代理 `Skipper`，把多來源內部資料整合成可 SQL 查詢、可審計、可用自然語言操作的系統。
- Why（為什麼重要）: 真正困難的不只是「讓模型會寫 SQL」，而是先把資料治理、權限、PII、防呆與知識可發現性做好；否則 AI 只會把原本混亂的資料環境更快地放大。
- How（如何運作/實作）: 底層用 lakehouse 思路整合資料，以 Trino 查詢、R2/Workers/Workflows/Access 等元件承載；新表先經 `Skimmer` 審核與 allowlist 控管，`Skipper` 再透過資料搜尋、schema/lineage 理解、SQL 生成與閉環修正，把自然語言問題轉成可驗證結果。
- Insight（個人心得）: 這篇最有價值的訊號是「先做資料作業系統，再做 AI 介面」。很多團隊急著把聊天框貼到資料庫前面，但 Cloudflare 的做法提醒我：若沒有治理預設、安全預設與工具邊界，agent 只會更快地做錯事；反過來說，底座一旦做好，AI 才真的能把資料民主化，而不是只做一層花俏 UI。
