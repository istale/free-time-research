# How we scale PgBouncer in ClickHouse Managed Postgres
- 原始連結：https://clickhouse.com/blog/pgbouncer-clickhouse-managed-postgres
- 閱讀時間：2026-07-12

## 摘要
本文由 ClickHouse 工程團隊（Kaushik Iska）撰寫，講述 ClickHouse Managed Postgres 如何把單執行緒的 PgBouncer 擴展成一個能吃滿 16-vCPU 的 pooler fleet。

**單執行緒瓶頸的本質**
PgBouncer 是 single-threaded 設計——即便跑在 16-vCPU 機器上，一個 process 也只會用到一顆核心，其餘 15 顆完全閒置。在 256 個 client 的壓力下，pidstat 顯示 PgBouncer 進程被 pin 在約 97% CPU（單核飽和），整機卻只有 ~10% 利用率。換言之，你付了 16 vCPU 的錢，卻只用到 1 個。

**核心解法：SO_REUSEPORT + Process Fleet**
- 把 N 個 PgBouncer process 綁定到同一個 listen port，全部開啟 `SO_REUSEPORT`。Linux kernel 會把進來的 connection round-robin 分配給各 process，client 看到的仍是「單一 endpoint」。
- 連線配額按 process 數等分（`max_client_conn / N`），避免 fleet 整體超出 Postgres 的接受上限。
- 使用 transaction pooling mode，transaction commit 後立刻歸還後端連線，提升週轉率。

**隱藏陷阱：Query Cancellation 的轉送**
Postgres 的 `pg_cancel_backend` 是開一個**新連線**附帶 cancel key 送過去。用 `SO_REUSEPORT` 後，kernel 可能把這個新連線送到**不同的 process**——而那個 process 從沒見過這條 query，cancel 就會靜默失敗。ClickHouse 的解法是 peering：fleet 中的 process 互相知道彼此存在，cancel 落到錯的 process 時會被**轉送（forward）**到持有 session 的那個 process，取消語意對 client 透明。

**實測數據**
單 process 峰值 ~87k TPS 後反退；16-process fleet 在 256 client 下達 **336k TPS，約 4 倍提升**。EC2 CloudWatch 從外部驗證相同趨勢：單 process 平均 16% CPU，fleet 約 60%。

## 3W1H 分析
- **What（做了什麼/主題）**:
  ClickHouse 把「單執行緒網路服務」這個經典問題的現代解法具象化：kernel 層 `SO_REUSEPORT` 讓多 process 共用同一 port 達到 load balancing，再以 process 間 peering 解決副作用（cancel 訊息必須跨 process 路由）。文中附完整 pgbench benchmark，論點不憑感覺。
- **Why（為什麼重要）**:
  PgBouncer 是 Postgres 生態最普及的 connection pooler。多數中型部署其實只跑單 instance，直到某天 TPS 撞牆才發現是 pooler 卡住而不是 Postgres 開銷——這是一個會被誤診為「Postgres 效能不夠」、實際上是「infra 沒用滿」的典型案例。同一模式（單執行緒 server 的多核化）也適用於 Redis、HAProxy 1.5、舊版 nginx worker 之外的場景。
- **How（如何運作/實作）**:
  - kernel 端：所有 process 對同一 port 呼叫 `listen()` + `SO_REUSEPORT`，Linux 5.x 對此已有成熟支援
  - control plane：把 `max_client_conn`、`max_db_connections` 等分，避免 oversubscribe Postgres
  - cancel 路由：peer 間維護 (cancel_key → owning_pid) 的 in-memory map，收到不認得的 cancel key 時 forward 給 owner
  - 監控：用 pidstat 區分「單核瓶頸」vs「真實多核吃滿」，CloudWatch 在 host 端交叉驗證
- **Insight（個人心得）**:
  這篇最値得記下的不是 PgBouncer 本身，而是**「找不到瓶頸時，往下看 kernel」**這個思路。`SO_REUSEPORT` 是 2013 年 Linux 3.9 起的 feature，但很多 single-threaded server 的 owner 至今仍在用「反向前端（haproxy 在前面 round-robin）」這種繞遠路的解法。ClickHouse 的實作比較乾淨：kernel 做 LB、應用層只負責 cancel 轉發這個語意敏感的 edge case。
  對主人目前的 stack 而言，這個 pattern 可能會在 LMStudio server 的水平擴展、或未來如果把 PocketRisu backend 改為 multi-worker 時派上用場——尤其 cancel/abort 訊息需要 cross-worker routing 的部分，幾乎是同一套思路。
