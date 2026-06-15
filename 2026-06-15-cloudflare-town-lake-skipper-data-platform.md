# How we built Cloudflare's data platform and an AI agent on top of it
- 原始連結：<https://blog.cloudflare.com/our-unified-data-platform/>
- 閱讀時間：2026-06-15

## 摘要
這篇 Cloudflare 工程文說明他們如何打造統一資料平台 Town Lake，以及建在其上的 AI 資料代理 Skipper：<https://blog.cloudflare.com/our-unified-data-platform/>。核心問題不是模型不夠強，而是企業資料散落在 Postgres、ClickHouse、Kafka、R2、BigQuery 等多個系統，還伴隨權限碎片、取樣誤差、資料陳舊與大量 tribal knowledge。Cloudflare 的解法是把 Town Lake 做成一個 lakehouse 入口，用 Apache Trino 作為查詢引擎，直接跨 Postgres、ClickHouse 與 Iceberg/R2 做聯查，並用資料分層與壓縮控制成本，讓熱資料、溫資料、冷資料都能維持可查詢性。真正讓它可在公司內廣泛落地的，則是 default-closed 治理模型：敏感資料預設封閉、PII 預設遮罩、權限具時效且所有查詢可稽核，避免「先全開、出事再補洞」的老路。Skipper 則把這層能力往上包成自然語言介面，能自己找表、看 schema 與 lineage、生成 SQL、執行查詢並在結果不合理時迭代修正。對我來說，這篇最有價值的地方是它再次證明：企業級 AI 代理的真正護城河不是聊天框，而是可驗證的資料面、穩定的治理面，以及把 SQL、權限、資料血緣一起產品化的工程紀律。

## 3W1H 分析
- What（做了什麼/主題）: Cloudflare 建立了統一查詢平台 Town Lake 與 AI data agent Skipper，讓內部人員可以用 SQL 或自然語言查詢跨系統資料。
- Why（為什麼重要）: 這解決了大型公司最常見的資料孤島、權限治理、資料品質與知識集中在少數工程師手上的問題，也把 AI 代理從 demo 變成可審計、可授權、可真正上線的資料工作流。
- How（如何運作/實作）: 底層以 Trino + Iceberg/R2 lakehouse 為核心，整合多資料源；治理上採 default-closed、PII 預設遮罩、時效性授權與完整 audit；上層的 Skipper 取得 schema、lineage 與語意上下文後，自動產生與修正 SQL，並以 Code Mode 降低多工具 round-trip 成本。
- Insight（個人心得）: 真正能放大 AI 生產力的，不是單純把 LLM 接進資料庫，而是先把資料平台做成「安全、可驗證、可自助」的系統。換句話說，AI 只是介面，真正難的是資料工程與治理工程本身。
