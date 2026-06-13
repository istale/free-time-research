# How we built Cloudflare's data platform and an AI agent on top of it
- 原始連結：https://blog.cloudflare.com/our-unified-data-platform/
- 閱讀時間：2026-06-13

## 摘要
這篇 Cloudflare 工程文介紹他們如何把分散在 Postgres、ClickHouse、Kafka、BigQuery、R2 等不同系統的資料整合成單一分析入口。核心做法是建立名為 Town Lake 的 lakehouse 平台：以 Trino 當查詢引擎、R2 Data Catalog / Apache Iceberg 當儲存與資料目錄、DataHub 當 metadata catalog，並額外做出 Lifeguard 權限控管、Skimmer PII 掃描、Transformer SQL DAG 編排與 ingestion pipeline。重點不是「把資料搬到同一處」而已，而是讓使用者可以在一個 SQL 介面裡跨來源 join，還能保留 schema evolution、time travel、分層壓縮與低成本儲存。

文章更有意思的部分是 Skipper。它不是單純把自然語言轉 SQL，而是建立在 Town Lake 之上的資料 agent：先用 DataHub 找 dataset 和 lineage，再結合 schema、欄位註解、產生該表的 SQL、少量 curated data model 文件，最後才動態查 Trino 做驗證。Cloudflare 很明確地說，光靠 schema 很容易讓 LLM 胡亂 join；真正提高正確率的是「多層 grounding」，尤其是 code-derived knowledge，也就是把產生資料表的 SQL 邏輯一起餵給 agent。這點很值得記，因為它說明了資料 agent 的關鍵不是 prompt 花樣，而是可檢索的結構化上下文。

另一個很強的設計是 default-closed governance。新表預設不能查，先經 Skimmer 掃描、標示 PII 風險、進 allowlist 待審，核可後才開放；PII 還是 session-level opt-in，所有揭露與查詢都被記錄。這代表他們不是先把 agent 放上去再補安全，而是把權限模型直接內嵌到資料模型與查詢流程裡。對任何想做內部 AI data assistant 的團隊來說，這篇最有價值的訊息是：真正困難的不是 LLM 產 SQL，而是 access control、audit、sensitive data review、可追溯性，以及把這些機制做成 self-serve 而不是阻力。原文連結：<https://blog.cloudflare.com/our-unified-data-platform/>

## 3W1H 分析
- What（做了什麼/主題）: Cloudflare 介紹其內部資料平台 Town Lake 與資料代理 Skipper，說明如何把分散資料源整合為單一分析介面，並讓使用者以自然語言查詢、驗證與視覺化企業內部資料。
- Why（為什麼重要）: 這篇把「企業內部資料平台」和「agentic interface」接得很完整，不只談資料湖或 SQL engine，而是把治理、PII、RBAC、metadata、lineage、memory、MCP 一起納入。它對想做企業級 AI data assistant 的人很有參考價值。
- How（如何運作/實作）: 底層用 Trino + R2 Data Catalog/Iceberg + DataHub + D1/Workers/Workflows/Durable Objects；上層的 Skipper 以多層 context retrieval、runtime introspection 和 Code Mode MCP 執行複合工作流，並完全沿用呼叫者權限做查詢與分享控制。
- Insight（個人心得）: 真正能落地的資料 agent，不是「把 SQL 生成做得更聰明」而已，而是把資料治理、語意模型、程式碼知識與稽核機制一起產品化。少一層都可能讓 agent 看起來能用，實際上不可靠。
