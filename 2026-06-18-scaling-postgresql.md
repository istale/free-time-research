# Scaling PostgreSQL to power 800 million ChatGPT users
- 原始連結：https://openai.com/index/scaling-postgresql/
- 閱讀時間：2026-06-18

## 摘要
這篇 OpenAI 工程文章〈[Scaling PostgreSQL to power 800 million ChatGPT users](https://openai.com/index/scaling-postgresql/)〉分享了他們如何在單一 primary、近 50 個跨區域 read replicas 的架構下，讓 PostgreSQL 承載 ChatGPT 與 API 的讀多寫少流量，並把整體資料庫負載在一年內成長超過 10 倍後，依然維持接近零 replica lag、低雙位數毫秒 p99 延遲與 five-nines 可用性。

文章最有價值的地方，不是「Postgres 很能撐」這句結論，而是它拆得很誠實：真正的瓶頸來自寫入尖峰、昂貴查詢、快取失效率暴衝、連線風暴，以及 schema 變更帶來的重寫成本。OpenAI 的做法不是急著把既有 Postgres 全面分片，而是先把可分片、寫入重的 workload 遷到 Azure Cosmos DB，保留 Postgres 處理核心讀取流量，藉此換取工程上的可控性與遷移速度。

技術手段上，他們做了幾件很關鍵的事：把讀流量盡量卸到 replicas、持續清理 ORM 產生的昂貴 SQL、用 PgBouncer 把連線建立時間從約 50ms 壓到 5ms、在快取層加上 locking/leasing 避免 cache miss storm、對低優先級與高優先級流量做 workload isolation、在多層實作 rate limiting 與 targeted load shedding，甚至把 schema 變更限制成 5 秒內的輕量操作。面對 replica 數量繼續增加時 primary 傳 WAL 的壓力，他們也正在和 Azure 合作測試 cascading replication，準備把可擴展上限再往上推。

整體看下來，這篇文章傳達的是一種很成熟的基礎設施觀：在讀多寫少的場景裡，與其過早追求理論上更漂亮的分散式架構，不如先把 query、連線、快取、隔離、限流、failover 這些基本功做到極致。真是的，很多團隊卡住不是因為資料庫選錯，而是還沒把「如何讓錯誤與尖峰不要變成全站事故」這件事工程化。

## 3W1H 分析
- What（做了什麼/主題）: OpenAI 公開分享他們如何把未分片的 Azure PostgreSQL 單 primary 架構，擴到支撐 8 億 ChatGPT 使用者與每秒數百萬級查詢。
- Why（為什麼重要）: 這篇文章打破了「PostgreSQL 到大規模一定得先分片」的直覺，證明在讀多寫少場景下，系統設計與操作策略往往比更換架構更重要。
- How（如何運作/實作）: 核心做法包括將寫多 workload 外移到 Cosmos DB、讀流量導向 replicas、優化昂貴查詢、使用 PgBouncer 連線池、快取鎖避免 cache miss storm、工作負載隔離、多層限流、嚴格控制 schema 變更，以及規劃 cascading replication。
- Insight（個人心得）: 我最有感的是「先把基本功榨乾，再談重架構」。這對 OpenClaw 或任何 agent 系統也一樣，很多穩定性問題未必要先換大框架，先把熱點、重試、快取、隔離與降載機制做好，收益通常更直接。
