# Scaling PostgreSQL to power 800 million ChatGPT users
- 原始連結：<https://openai.com/index/scaling-postgresql/>
- 閱讀時間：2026-06-19

## 摘要
這篇文章說明 OpenAI 如何在單一 primary 的 PostgreSQL 架構下，支撐 ChatGPT 與 API 平台面向 8 億使用者、每秒數百萬查詢的讀多寫少工作負載。核心觀點不是「PostgreSQL 無限可擴」，而是透過非常嚴格的系統工程，把 primary 的寫入壓力壓到最低，盡可能把讀流量導向全球多區域的 read replicas。

文中最值得注意的是取捨邏輯。OpenAI 並沒有急著把現有 PostgreSQL 全面分片，因為那會牽動大量應用端點與資料存取路徑，成本極高。相反地，他們先把可水平切分、且寫入密集的 workload 遷到像 Azure Cosmos DB 這類分散式系統，把 PostgreSQL 留給較適合它的讀密集場景。這種「先拆可拆的，再保住核心單體」的策略，比口號式的全面重構更務實。

技術上，文章列出幾個關鍵手段：把昂貴 join 與 ORM 產生的低效 SQL 持續清掉、用 PgBouncer 做連線池化、把高優先與低優先流量分流到不同實例、在多層做 rate limiting 防止 retry storm，以及限制 schema migration 只做輕量變更。針對 read replicas 擴張帶來的 WAL 傳輸壓力，OpenAI 也和 Azure 團隊合作測試 cascading replication，目標是在不壓垮 primary 的情況下繼續擴副本數。

我覺得這篇最有價值的地方，是它提醒人別把「是否分片」看成非黑即白的架構信仰問題。真正的順序往往是先把 query、連線、快取、限流、隔離與營運流程做到位，確認單體系統真的碰到天花板，再付出分片那種高昂複雜度。很多團隊不是死在資料庫不夠強，而是死在還沒把基本功做滿，就提早進入分散式地獄。

## 3W1H 分析
- What（做了什麼/主題）: 文章拆解 OpenAI 如何以單一 primary、近 50 個 read replicas 的 PostgreSQL 叢集，支撐 ChatGPT 與 API 的超大規模讀取流量。
- Why（為什麼重要）: 它提供了一個很實際的訊號：對讀多寫少系統來說，PostgreSQL 的上限常比團隊想像得高，前提是你願意把查詢優化、流量治理、故障隔離與營運紀律做到非常徹底。
- How（如何運作/實作）: 做法包含遷出可分片的寫密集 workload、把讀流量轉往 replicas、優化 SQL 與 ORM、使用 PgBouncer、分級隔離 workload、多層限流、限制 schema 變更，以及研究 cascading replication 來降低 primary WAL 壓力。
- Insight（個人心得）: 比起一開始就喊著微服務或分片，先把資料庫周邊的工程紀律補齊，通常更便宜也更有效。這篇很適合拿來提醒自己：架構升級不只是換技術名詞，重點是先把瓶頸拆解清楚。
