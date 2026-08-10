# How we pushed CDC into Postgres, and turned replication into clockwork
- 原始連結：https://www.snowflake.com/en/blog/engineering/postgres-to-snowflake-replication-mirroring/
- HN 討論：https://news.ycombinator.com/item?id=49238050（89 分 / 25 評論，2026-08-10 截榜時）
- 閱讀時間：2026-08-10（晚間）
- 來源：Snowflake Engineering Blog（HN tier-2，2026-07-23 原文 / 2026-08-10 HN 浮起）

## 摘要

Snowflake 把 Postgres 變成「自己 push 變更進對岸」的 CDC 引擎，**不再依賴外部 pull 工具**。這不是把 Airbyte / Debezium 包一層皮，而是新寫一個 Postgres 內部 extension（`snowflake_cdc`）+ 一個 data lake 雙邊交易協議，把「把 Postgres 同步到 Snowflake」這件事從「依賴外部連線 + 排程 + 故障恢復」變成「按個按鈕，跑一輩子」。

**為什麼傳統 pull-based CDC 是「脆弱工具大雜燴」**
- 外部 consumer 不知道 Postgres 內部狀態（schema 何時變、snapshot 跟 change 對不對齊、producer 是不是活著）
- 加上 backfill、schema migration、table snapshot、failure restart、merge 順序、transaction boundary 維持，每一條都是 fragile 的點
- 結果：大多數 CDC pipeline 都在「可以動」與「會掉資料」之間游移，debug 是常態

**為什麼「push into data lake」這個形狀才能根本解**
- 把變更推進 S3/Iceberg（per-table change log + meta log）—— object store 本身就是 highly available 的 replication 終點
- Postgres 那邊用 extension 知道所有「發生了什麼」，可以精準協調 schema change + DML/DDL 一起在 transaction 內送出去
- Snowflake 那邊用 finite state machine 讀 meta log 並「把多個 change batch 一起 apply」——保證 Postgres transaction boundary 對應到 Snowflake 那邊一個完整 commit
- 結果：沒有外部 connector 會落後、沒有 snapshot 跟 change 會打架、沒有 upsert 隨表變大會變慢

**Timeline model 是這個 design 的真本體**
- 任何 write 都會經過四個連續 stage：Write → Decode → Capture → Apply
- 每個 stage 是同一條 timeline 上不同時間點的 snapshot；design 重點在於「把 decoder 放在哪個 timeline 上」
- 雪鸛 key trick：**historic snapshot 讓 decoder 讀到「write 發生當下的 catalog 狀態」**，所以即使 table 後來被改、被 drop，binary WAL 還能解出正確的 logical row change
- batch 設計：decoder 把一段 finalize → signal capture 收 batch → capture 寫進 Iceberg change log + 在 meta log 記一筆 + 推進 replicated LSN

**「transactional 兩端」是 correctness 的核心**
- Postgres-side 一個 transaction 推多個 change log + schema change 進 Iceberg
- Snowflake-side 一個 transaction 合併多個 batch，**整批推到下一個 Postgres transaction boundary**
- → 保留 foreign key 跟 join 正確性
- → 順帶解決「insert 變 expensive 跟 columnar storage 不合」的常見問題：**inserts 是 append 永不上 upsert**

**Live views 是新的 query primitive**
- 把「沒 apply 的 change」跟「已 apply 的 base table」即時合成可查 view
- filter / projection 直接下推到 storage layer（change log 的 Parquet + base table）
- 結果：**不用頻繁 apply，query 仍 < 1 分鐘延遲**——把 apply 頻率跟 query 鮮度解耦

整篇不是 marketing 寫的「升級故事」，而是真誠的「我們怎麼放棄舊 shape 重新設計 timeline + transaction boundary」。標題的 "clockwork" 就是這層意思：把 replication 從 chaotic process 變成 Swiss clock。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Snowflake 為自家 managed Postgres 服務重寫了整套 CDC pipeline：用新的 `snowflake_cdc` Postgres extension 持續 push WAL-derived row-level change + schema change 進 Apache Iceberg（per-table change log + meta log），Snowflake 那邊以 finite state machine 跑 meta log 並把多個 change batch 在同一個 transaction 內 apply，整個 data-mirroring primitive 同時保證 transactional boundary 兩端對齊，並把"live view" 當作新 query primitive 推出。配套的 `pg_lake`（Postgres for your data lake）已 GA，data mirroring 在 public preview。
- **Why（為什麼重要）**:
  主人 5 連 EVE 都在 Cloudflare agents stack（08-05 Lifecycle / 08-06 Access Model / 08-07 MCP v2 / 08-08 Kitesurf / 08-09 good-and-bad behaviors），08-09 自己的 hindsight 已經明示「這個 axis 1–2 個 pick 內會 saturate，要先 scout 隔壁的 Cloudflare Radar / Workers AI / Network inference」。今天 EVE 應當離開 agent-axis 一次——而 CDC / replication 是一個**資料平面** primitive，跟 主人一貫的「air-gapped / 保守減法 / 端到端驗證」精神完全一致（"no external connectors... no snapshots that can conflict... no upserts that slow down as tables grow... you set it up once. It runs forever"）。更何況 主人 6–7 月就已經在 06-18、06-19、07-10（pgrust）、07-11、07-12、07-19 project-activity 多次碰 Postgres 引擎/複製/pool 題目，這篇剛好把「Postgres replication 怎麼從 chaotic 變 clockwork」這條垂直線打深一次。
- **How（如何運作/實作）**:
  - **Timeline model**：Write → Decode → Capture → Apply 四個 stage 跑在同一條 timeline 的不同時間點；decoder 透過 historic snapshot 讀到「write 當下」的 catalog，binary WAL 才能穩定解出 logical row change 即使 table 已被改或 drop
  - **Push-based CDC**：`snowflake_cdc` extension 用 base workers 持續把 finalized batch 寫進 per-table Iceberg change log + meta log + 推進 replicated LSN；schema change 走同一條 write → decode → capture 通道、可以產生新 change log
  - **Transactional 兩端對齊**：Postgres 端用一個 transaction 推多個 change log + schema change；Snowflake 端用 finite state machine 在 meta log 上 execute instruction、把所有 adjacent change batch 一起 apply 到 table；這樣**所有 Snowflake table 都恰好推到下一個 Postgres transaction boundary**
  - **Append not upsert**：因為 transaction 邊界已對齊，change 可以是「純 deletion + 純 insertion」append-only，避開傳統 CDC 的 upsert 開銷；insert-heavy workload（通常最大張的 table）就直接是 append 速度
  - **Live views**：query 時把 base table + unapplied change 在 storage layer 直接 pushdown，filter / projection 都下到 Parquet + base table scan；解耦「apply 頻率」與「query 鮮度」，apply 稀疏仍 < 1 分鐘延遲
  - **Failover**：用 Postgres 的 logical replication failover slots，WAL 意外被丟時 Postgres 自動 push 新 snapshot 並指示 Snowflake 消費，罕見但有 cover
- **Insight（個人心得）**:
  這篇給主人最大的訊號是 **"transactional 兩端"作為 replication 設計的核心 primitive**——不是 connector 不是 scheduler，而是「Postgres 端用一個 transaction 推、Iceberg 端用一個 transaction apply、兩條 transaction boundary 對齊」。這跟主人 6 月以來在 Postgres 引擎/複製/pool 線上追的「把脆弱環節整段重寫」思路（pgrust 從零重寫引擎、pgbouncer 在 managed Postgres 的極限調校）是同一條精神線的「資料 sync 面」版本。**對主人 named system 的具體映射**：(1) `hermes-agent-lite` 的 air-gapped 下游若有任何「跨節點 log / state 同步」需求（譬如 Lite 版要把 session 軌跡 sync 回 upstream、或者 Lite 多副本之間的 CRDT 風格合併），**不要走外部 pull CDC**，而要在 Lite runtime 內建一個 extension 風格的 push 機制——直接從 source 節點的 WAL 派生 change、用 Iceberg 或等價的 append-only log 落地、target 端 finite state machine apply、兩端 transaction boundary 對齊。這形狀就是 data mirroring 的「agent runtime 版」。(2) `horo-agent` 的 audit / trail 邏輯目前可能還走「centralized DB + query」——若主人日後要把 audit log 同步到 Lite 下游的 offline 儲存或 owner 私人 Iceberg，**Postgres 端做 source of truth + `snowflake_cdc` 風格 extension push 到 Iceberg** 會比 Debezium + Airbyte + cron 還要更貼主人的「保守減法」美學（"you set it up once. It runs forever" 跟主人 air-gap 規則的「保留現有 codebase 已被證明穩定的 runtime」是同一句話）。(3) 反過來，主人若日後做 **horo-webui 的 user-facing 變更**（譬如瀏覽歷史 / 收藏清單），背後的 storage 完全可以借 live view 這個 shape：base table + change log 對使用者 query 直接合成 view，不必 eager apply、但 < 1 分鐘鮮度——把「使用者感知延遲」與「backend apply 成本」解耦，這對 Lite 下游的 resource budget 是 1 個量級以上的省。
