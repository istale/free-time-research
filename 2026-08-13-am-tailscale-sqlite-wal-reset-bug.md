# Tailscale Traces Database Corruption to 16y/o SQLite WAL-Reset Bug
- 原始連結：https://tailscale.com/blog/sqlite-wal-reset-bug
- HN 討論：https://news.ycombinator.com/item?id=49272832
- 閱讀時間：2026-08-13（早間）
- 來源：Hacker News 熱門前 10（710 分 / 114 則留言，讀取時 2026-08-13 07:00 Asia/Taipei）

## 摘要

Tailscale 工程團隊把控制平面（control plane）六個月內發生的 19 次資料庫損毀事件，追溯到 SQLite 中一個�藏了至少 16 年、極度罕見的 checkpoint / write transaction 競爭條件（race condition），並命名為「WAL-Reset bug」。這篇文章是教科書級別的「生產事故 + 取證分析」紀錄，主人近期恰好在 SQLite / session persistence 場域工作（hermes-agent-lite 用 SQLite 做 session cache），是直接命中軸線的文章。

**事故時間軸與「單寫者」架構**
Tailscale 控制平面用一組 SQLite shard 服務 tailnet：每個 tailnet 落在一個 shard，每個 shard 是一個 SQLite DB、單一 Go process 獨佔寫入——這是 SQLite 官方推薦的用法。2024 年 8 月開始出現備份損毀警告，六個月內累積 19 次，每次都必須停掉該 shard 的 control plane、從備份還原；恢復時間最初超過一小時，逐步壓到一小時內。

**取證方法論：被動遙測 + 第三方原始碼級合作**
事故沒有可重現的�發條件（不是特定 shard、用戶、時段或負載），他們沒辦法做合成復現，只能在 production 部署被動 forensic telemetry 等事故自然發生。同時付費購買 SQLite 官方 professional support contract，與 SQLite 開發者直接對話、列出候選理論（POSIX advisory lock 在 close() 被取消、SQLite 內部記憶體管理錯誤、disable thread safety 後多執行緒存取等），逐個排除。中間做了「transaction log replay pipeline」——把所有寫入語句串流到另一個 log，從已知良好的備份重放以跳過損毀。這個 pipeline 在兩次事故中**意外暴露**了真正的線索：已 commit 的資料從後續 transaction 眼中消失，write silently vanished——這在 SQLite 的 serialisable 交易模型下理論上不可能。

**WAL-Reset Bug 的本體：checkpoint / write race**
開 WAL（write-ahead log）模式後，新 page 不直接寫進 DB file，而是先寫到 WAL file；checkpoint 把 WAL 的 pages 拷回 DB file。Tailscale 因為 backup pipeline 需求**手動控制 checkpoint 流程、頻率也比預設激進**——這正是他們比一般使用者更容易踩到 bug 的原因。Bug 細節：當一次 write 在 checkpoint 的特定瞬間發生，checkpointing process 會誤以為某些 pages 已經從 WAL 拷回 DB file 但其實沒有，那些 page 從未寫入 DB file，永遠遺失；後續其他 page（如 index 引用這些 page）繼續寫入，使整個 DB file 進入 corruption 狀態。SQLite 開發者為此寫了 `tmstmpvfs` shim 包進 virtual filesystem 層，把所有 I/O 加上 trace log，部署到 production 等下一次事故發生，順利抓到 race。

**Fix 路徑：SQLite 3.52.0 與其後續副作用**
SQLite 開發者加了一個 check：偵測 WAL 是否被另一個 thread reset。這包進 3.52.0，Tailscale 部署後被動備份 monitor 立刻報 13 個「損毀」，但其實是新引入的 stale expression index 問題（精度 timestamp 用 text 存、在 VIRTUAL generated column 轉成 float，3.52.0 改了 text-to-float rounding 行為）。SQLite 撤回 3.52.0、改發 3.51.3（只含 WAL-Reset fix），3.53.0 加了 self-healing index。Tailscale 自己把 timestamp 精度降到 integer seconds（避免浮點 round）。修好後 Tailscale 在自家 driver patch 加一個「party mode」warning：當 WAL-Reset 條件出現但 SQLite 攔住、沒有真的損毀時主動告警——兩個月後這個 alert 真的 fire，證明了真實 production 中 race 確實會發生。

**對主人的直接意義：hermes-agent-lite 的 SQLite session cache 是同類 substrate**
主人目前用 SQLite 做 hermes-agent-lite 的 session cache 持久層（air-gap downstream 預設單寫者 + WAL）。這篇文章把三件事一次說清楚：(1) 在 SQLite 上「單寫者 + WAL + 手動 checkpoint」是最高風險組合，(2) 真正的 corruption root cause 16 年沒人抓到，是因為它太罕見——純靠「跑得久」才會浮現，(3) Tailscale 用了「transaction log replay pipeline」這種「線性確定性重放」的解法，恰好對應 session cache 的 audit 需求。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Tailscale 工程團隊把 2024/8–2025/3 共 19 次 SQLite 損毀事故追到 16 年之久的 WAL-Reset bug——一種 checkpoint 與 write transaction 之間的罕見 race condition。文章包含事故 timeline、SQLite 內部架構（parser / pager / VFS 三層）、`tmstmpvfs` 客製化 VFS shim、transaction log replay 取證 pipeline、3.51.3 / 3.52.0 / 3.53.0 的連環修補，以及 Tailscale 自家 driver 的「party mode」warning。
- **Why（為什麼重要）**:
  主人目前用 SQLite 做 hermes-agent-lite session cache、單寫者 + WAL，正是 Tailscale 的同款 substrate 選擇。文章同時提供 (a) 一個「真實 production 取證」的方法論樣板（被動 forensic telemetry + 第三方原始碼級合作 + transaction log replay），(b) 一個具體的 SQLite 風險點（手動 aggressive checkpoint 會放大 race），(c) 一個已經被官方修掉、可直接對照的 bug 編號（SQLite 7168988acbec2d8d commit）。這些都直接落在主人 pinned interests（SQLite / session persistence / 安全 + 取證）。
- **How（如何運作/實作）**:
  SQLite WAL 模式下，write transaction 先 append page 到 WAL file，checkpoint 負責把 WAL 的 page 拷回 main DB file。Race：當一次 write transaction 在 checkpoint 進行中發生，checkpoint 內部狀態會被 write 重置 WAL 的行為干擾，導致 checkpoint 把「從未實際寫入」的 page 標記為已拷貝，造成 page 永久遺失與後續 corruption。SQLite 官方 fix 是加一個 detection 條件檢查「WAL 是否被另一個 thread reset」。Tailscale 端的解法則是 transaction log replay pipeline：把所有 write SQL 串流到獨立 log，從已知良好備份重放以跳過損毀，這條 pipeline 同時也是「write silently vanished」的取證線索來源。
- **Insight（個人心得）**:
  把 Tailscale 的事故數字直接當 hermes-agent-lite session cache 的 SLO 錨點：**同一個 SQLite 版本（>=3.51.3）+ 預設自動 checkpoint + 嚴禁手動 aggressive checkpoint**——這是 1 小時內可落地的最小安全閾，零改寫 runtime。下一個層級是給 session cache 加「transaction log replay」的 audit 路徑（hermes-agent-lite 目前用 SQLite 做 session persist，已經有寫入路徑，加 replay 只需在 persist hook 多串流一份 `INSERT INTO session_audit_log` + 30 行 replay script）。**Tailscale 那個 `tmstmpvfs` shim + `party mode` warning 是這篇文章最強的 primitive 樣板**：可以在 hermes-agent-lite 用 `sqlite3_trace_v2` + 自家 driver patch 在 checkpoint / write race 條件觸發時 log warning，把「看不見的 race」變成「看得見的 alert」。成本：純 Python dict 比對 + 一個 sqlite hook callback，預估 < 100 行，< 4 小時 prototype。對應到 8/04 KV cache 8-byte tag + integer compare 那個 template，這是同一個「把隱形失敗變顯性」家族的第二個實例。
