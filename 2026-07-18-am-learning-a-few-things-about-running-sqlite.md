# Learning a few things about running SQLite

- 原始連結：https://jvns.ca/blog/2026/07/17/learning-about-running-sqlite/
- 閱讀時間：2026-07-18

## 摘要

**主題定調 — SQLite 是真的 database,不是檔案系統。** Julia Evans 在 2026-07-17 寫這篇時正在做她的第四個 SQLite 網站,自承「blog post 都說 SQLite 在小網站 production 沒問題,但 SQLite 畢竟是 database,資料庫很複雜,我也真的不太會運維資料庫」。全文是六段小觀察的堆疊,口吻誠懇,適合當「我踩過的小坑」筆記讀。

**`ANALYZE` 是這篇最有戲劇效果的一段。** 她在 4,000 筆的 FTS5 表跑查詢,5 秒才回。「電腦不是很快嗎?」一查才發現要 `ANALYZE` —— query plan 根據統計資料選 index,沒跑過 `ANALYZE` 的 SQLite 等於盲目猜,她懷疑踩到某種不小心變 O(N²) 的 plan。跑完 `ANALYZE` 之後同一個查詢直接降到 0.05 秒。Julia 自己也說她「用了 SQLite 四年到今天才知道有 `ANALYZE` 這東西」——這句坦白才是這段真正的訊號:文件沒讀過就上 production 不是開玩笑。

**寫入 timeout 會把整個 worker 拖死。** 第二段的坑更貼近主人日常:大量行要清掉(已完成的 django-tasks-db tasks)時她跑 DELETE,五秒未完成 → 其他 worker 要寫入的時候 timeout → worker crash → VM shutdown。她的解法是把大批 DELETE 切小批執行,讓任何單一查詢都不超過她設的 5 秒 timeout。結論是她「更能體會為什麼有人會想用 Postgres 這種能多寫者並行的『真』資料庫」。SQLite 的單寫者架構在這種 batch 維運時是硬限制,跳不過去。

**ORM 不最佳化目前還行,但有邊界。** 第三段說她目前用 Django ORM 隨便寫、不看 query plan,資料庫 10,000 筆以內「大致還行」,除了 `ANALYZE`。這個觀察對主人這種「先把東西做出來再說」的工作風格有安撫作用,但她也明說:資料量一上去就別再裝沒事了。

**備份的兩條路 — restic 整檔 vs Litestream 增量。** 她做過兩種備份:(a) `VACUUM INTO '/tmp/calendar.sqlite'` + gzip + restic 推到 S3,但 restic 有時被 OOM kill,並且她得跑 `restic unlock` 清掉被鎖住的備份;(b) Litestream `replicate -config litestream.yml`,設定 `retention: 400h` 想留一點歷史,但「我也不知道到底有沒有效」。Litestream 是 SQLite 生態裡增量 WAL shipping 的標準答案,適合 24/7 線上服務的災備設定。

**把資料拆成多個 SQLite 檔。** 第五段把「Mess with DNS」這個她跑了四年的工具拆成三個獨立 `.db` 檔(因為那些表本來就不需要共享 transaction)。這是 SQLite 特有的典範:跨檔不需要 foreign key,每個檔可以獨立 WAL 與備份,在 self-host 小工具裡比共享單檔乾淨得多。

**WAL 是預設動作,不是優化。** 最後一段最短,「我照所有 blog post 說的開了 WAL,然後祈禱」——一句話點出 SQLite 社群把 `journal_mode=WAL` 講得太理所當然,新人不會問為什麼要開,只會照做。

## 3W1H 分析

- **What（做了什麼）**: Julia Evans 把她在第四個 SQLite 網站上踩到 / 學到的六個小觀察寫成一篇部落格 — `ANALYZE` 對 query plan 的威力、單寫者架構下的 timeout 炸 worker 鏈、ORM 在萬筆規模內不需先最佳化、兩種備份策略(restic 全檔 + Litestream 增量)、跨檔拆分資料庫、WAL 一開了之。每一段都是「我以前不知道,現在知道了」的口吻。
- **Why（為什麼重要）**: SQLite 在 self-host / 個人 side project 裡越來越熱門(主人之前 pgrust 那篇已經驗證 SQLite 在 Postgres 替代品的討論裡也是核心),但大多數人(包括文章作者)把 SQLite 當檔案看待,結果在 production 等級的維運上踩坑。文章的價值不是教新東西,是把 SQLite 本身「也是個 database,所以也有 DBA 級問題」這個前提寫出來 — 對主人的 FreeTime / PocketRisu / LM Studio metadata 這類 self-host stack 都是立刻用得上的提醒。
- **How（如何運作/實作）**: 文章沒有可跑的 code repo,只有命令片段。`ANALYZE` 是 SQLite 內建指令,刷統計資料給 query planner;`VACUUM INTO '<path>'` 是 SQLite 3.27+ 提供的線上備份寫到另一檔(比 `.backup` 安全,不會卡到 live WAL);Litestream 是模擬 MySQL binlog 的 WAL streaming,把 `*-wal` 增量送到 S3 / NFS。這三個是文章推薦的最小工具鏈,撐起一個 SQLite 服務自此具備「查詢計畫正確」「熱備份」「災難還原」三件基本 DB 職責。
- **Insight（個人心得）**: 主人目前的 self-host stack 是 Hermes + multiple profiles + cron jobs + LM Studio 跑 inference,每一塊本質上都是「小到用 SQLite 就夠、但要偶爾做維運」的 workload。這篇文章的 `ANALYZE` + Litestream 兩個組合剛好對應主人即將面對的兩個問題:(1) Hermes session_search 用 SQLite FTS,主人記憶的 MEMORY.md 越多就越需要 `ANALYZE`,否則 session 開頭那幾個 query 會慢; (2) `~/.hermes/profiles/` 下的每個 profile metadata 都是 SQLite 檔,垮了等於整個 persona 沒了 — 主人現在沒有備份 profile DB 的動作,加一個 Litestream replicate 到本機 backup 目錄大約五分鐘,以後跨機器搬 profile 直接 `litestream restore` 即可。最後一點更切身:**昨天 PM(2026-07-17) 的 SearchOS-V1 跟 2026-07-17 AM 的 LM Studio Bionic 都在談 agent infrastructure** —— 把 SQLite 從「儲存體」升級成「配備 `ANALYZE` + 備援的維運物件」,正是這條主線的 baseline 動作。Lobste.rs 同一天也在 HN 上宣布改用 SQLite(同花順第 10 名),所以 2026-07-18 這個主題不只是 Julia 個人的偶發心得,而是社群整體的回頭潮。
