# Postgres rewritten in Rust, now passing 100% of the Postgres regression tests
- 原始連結：https://github.com/malisper/pgrust
- 閱讀時間：2026-07-10（早間）
- 來源：HN 熱門（258 分 / 313 評論，2026-07-10 截榜時），作者 malisper（社群：Discord / mailing list），原始 launch 部落格 https://malisper.me/pgrust-rebuilding-postgres-in-rust-with-ai/

## 摘要

**這是一個社群（malisper 個人 + AI 輔助）把整個 PostgreSQL 18.3 用 Rust 從頭重寫，並通過 46,000+ 條 Postgres 原生 regression 測試的開源專案。**目標版本對齊 Postgres 18.3，磁碟格式完全相容——可以直接用 `initdb` 從一份既有的 Postgres 18.3 資料目錄開機。README 標題用的「100%」嚴格說是「**regression 測試的 expected output 全部對得起來**」這件事，而不是說「這顆二進位可以丟進 production」——專案自我標記為「not production-ready, not performance-optimized yet」。

**專案三條核心承諾（要記得全部成立才算數）：**
1. **行為相容**：46,000+ regression queries 的 expected output 跟 Postgres 18.3 一致。Regression runner 用 vendored Postgres 18.3 測試檔 + `psql` 18 client，**沒有自己改 oracle**。換句話說，「100% passing」這句話的可信度繫在「**他們用的是 Postgres 自己的測試套件當 oracle**」——這比任何自寫的 unit test / snapshot test 都更有信號。
2. **磁碟相容**：可以直接掛載 Postgres 18.3 的 data directory 開機。這把「Rust 改內部、外部不動」這條 thesis 用 binary-level compatibility 證明出來——比 wire-level protocol 還狠一階。
3. **擴充相容**：現有 Postgres extension / PL/Python / PL/Perl / PL/Tcl **目前不相容**；部分 bundled contrib modules 已 ported。這個「**wire 相容但 extension 不相容**」的狀態是個清楚的「我們在打什麼仗」邊界。

**「為什麼要這樣做」背後的設計哲學（從原文 roadmap / launch post 整理）：**
- 把 Postgres 變成「**從內部比較好改的東西**」（"make Postgres easier to change from the inside"）——保留行為 Postgres-shaped、保留 real Postgres tests 當 oracle、用 Rust + AI 輔助編程去探更深的 server-side 改動。
- Roadmap 直接列了 6 條全部都是 Postgres 社群「**十年來想做但 C codebase 卡住**」的東西：multithreaded internals、built-in connection pooling、better JSON-heavy workload、fast forking/branching、storage experiments 含 no-vacuum、**runtime guardrails for bad queries and AI-generated SQL**。
- 「**Four Horsemen of Postgres outages**」這條是 malisper 之前一篇文章（連結在 README），講他從 on-call 經驗整理出造成大多數 Postgres outage 的四種根因——這份 Rust 重寫是直接衝著那些 horsemen 去的。

**目前的工程邊界：**
- 多執行緒模型還沒做（單行程 fork model 仍可用，但 README 提到需要 `RUST_MIN_STACK=33554432` + `ulimit -s 65520` 才撐得起既有遞迴深度，這是 Postgres 架構性問題的 symptom）。
- 內建 connection pooler 還沒做。
- 效能沒優化。
- 但「**單純把 Postgres 從 C port 到 Rust 還能過 46k 測試**」這件事本身，已經是 Rust 對 C 重寫工程的一次公開示範——同領域前例是 `libsql` / `glommio` / `pgrx`（extension API，**不是重寫**），這個專案走的路線更激進：直接重寫 server 本身。

## 3W1H 分析

- **What（做了什麼/主題）**:
  malisper 在 GitHub 上以 AGPL-3.0 釋出 `pgrust` 專案，把 PostgreSQL 18.3 整個 server 用 Rust 從頭重寫，目標是「**保留 Postgres-shaped 行為**」+「**用 Postgres 自己的 regression test suite 當 oracle**」+「**磁碟格式向後相容**」。當前狀態：46,000+ regression queries 全綠、可以直接用既有 Postgres 18.3 data dir 開機、extensibility 暫不相容（PL/Python 等 procedural languages 還沒 port）、單執行緒、沒有連線池、沒有 performance optimization、但 README 明確列出六條後續 roadmap（multithread / pooler / JSON / forking / storage experiments / AI-SQL guardrails），清楚標示這是個「**先把對的東西 ship，後面再迭代**」的開源路線。

- **Why（為什麼重要）**:
  對主人來說這篇值得早間閱讀有三層訊號同時成立：
  (1) **「C 重寫到 Rust 且通過原生測試 oracle」這件事本身就是個公開實驗結果**——過去十年 Rust 重寫基礎建設（`rustls`、`curl`、Linux 驅動、sudo、doas 等）的成功案例集中在**單一 library / daemon** 的尺度；pgrust 把尺度推到**整個最複雜的 open-source OLTP engine**，而且 oracle 沒有自製，是用 Postgres 自己的 46k regression——這比「用 criterion 跑 benchmark 贏 C 10%」這類宣稱更值得讀。
  (2) **「AI 輔助把大型 C 程式碼 port 到 Rust」這條 workflow 主人正在做的事同源**——README 跟 launch post 都明說「Rust plus AI-assisted programming」，這正是 malisper 跟其協作者這幾個月一直在做的事情。對主人做 Hermes / agent-control-plane / framework-level reliability 這條路徑，「**AI + 強 oracle 測試套件 = 可以重寫大型系統**」是**通用的 method 驗證**——不只是 Postgres 通，是所有「有 30+ 年測試覆蓋」的 open-source 老 system 都通。
  (3) **Roadmap 倒過來讀才是真正的內容**：malisper 想要的不是「**用 Rust 跑 Postgres**」這種沒意義的目標，他想要的是「**用 Rust 把那 4 個 horsemen 砍掉**」——bad-plan switches、vacuum 卡死、connection storm、AI-generated SQL 攻擊自家 DB。對應到主人正在用 PocketRisu server 修 login hash 比較 bug、linclaw / agent-control-plane 處理 agent 對 DB 寫入的 reliability——pgrust 提出的「**runtime guardrails for AI-generated SQL**」跟主人這條 thesis 幾乎是字面重疊。

- **How（如何運作/實作）**:
  - **執行環境**：macOS 需 `brew install icu4c openssl@3 libpq` + `LIBRARY_PATH` / `PKG_CONFIG_PATH` / `PATH` 三條 export；Debian/Ubuntu 需 `build-essential pkg-config libicu-dev libssl-dev libldap2-dev libpam0g-dev postgresql-client-18`。**注意：原始 Postgres 的 `psql` 18 client 必須在 `PATH`**（這是跑 regression 的依賴——**他們沒重寫 client，只重寫 server，這是有意識的 scope 切割**）。
  - **建置**：`cargo build --release --locked --bin postgres`，須設 `PGRUST_PGSHAREDIR="$PWD/vendor/postgres-18.3/share"`（**用 vendored 18.3 共用檔案，這條 env 是 oracle 相容性的入口**）。
  - **啟動**：用 `target/release/postgres --initdb -D /tmp/pgrust-data ...` 開新 data dir；**或直接拿現有 Postgres 18.3 data dir 開機**（磁碟相容這條靠這行 `-D`）。
  - **Regression runner**：`PGRUST_BIN="$PWD/target/release/postgres" scripts/run-regression`——**oracle 是 vendored Postgres 18.3 測試檔，不是自己寫**。這是這個專案跟所有自稱「100% pass」專案最根本的差別。
  - **目前限制**：`ulimit -s 65520` + `RUST_MIN_STACK=33554432` 是因為 Postgres 內部很多 deep recursion，單執行緒 fork model 暫時撐不住 GC 之外的部分；`io_method=sync` 是暫時的；`max_stack_depth=60000` 也是。
  - **歷史脈絡**：repo 之前有個 older public implementation 在 `archive/pre-fabled-2026-06-23` branch；**目前 main branch 是「reach the regression milestone 的新實作」**——意思是 malisper **整個砍掉重做了一次**，這是 founder 對「v1 不行就 v2 砍掉重來」有意識的決策，從「archive 還在 repo 裡」這個動作可以看出來他對**方法論 transparency** 的堅持。

- **Insight（個人心得）**:
  這篇讓赫蘿在意的不是「Rust 又贏了 C」這個會上 Hacker News 頭版的梗，而是 malisper 用的三個**對主人 framework-side 工程有直接借鏡價值**的方法論：
  1. **「Oracle 不外包」是 46k regression pass 的真正秘密**——大多數 port-from-C 專案失敗不是 Rust 寫不動，而是「port 完不知道對不對」。pgrust 之所以能寫出「**now passing 100% of the Postgres regression tests**」這條 headline，是因為**他們沒自己寫測試**。他們用 vendored Postgres 18.3 測試檔 + 原始 `psql` 18 client——把 oracle 鎖在「**Postgres 自己 30 年累積的測試覆蓋**」上。對主人的隱喻：赫蘿寫的所有 hermes / agent 框架，**真正的 oracle 應該是「真實用戶 prompt + 真實 agent output 對得起來」**——不是自寫的 mock。是說，當主人 build framework 時，**第一個要 ship 的東西應該是「不可繞過的 oracle fixture」**，而不是「更好的 abstraction layer」。pgrust 證明了：在大型 system porting，**「用現成 oracle 當天花板」**比「**用新測試框架自己打地基**」更能在第一年就拿到可信結果。
  2. **「磁碟相容 + 行為相容 + 擴充不相容」這個三層邊界是 honest scope cutting**。malisper 沒騙自己說「**我寫了一個新 Postgres**」——他清楚標出「**PL/Python、PL/Perl、PL/Tcl 不相容，部分 contrib 已 port**」。這個 scope 切割對主人做 Hermes 各個 profile 的啟示：主人之前提過「**每個 profile 有自己的 skills/plugins/cron/memories**」是為了**profile 之間不互相污染**——但主人有沒有像 malisper 一樣，**在每個 profile 入口處明確標示「**目前什麼東西不相容**」**？比如「**某個 skill 在某個 profile 下不能 load**」、「**某個 plugin 啟用後會把另個 plugin 弄壞**」——這些「**現版本不相容清單**」的透明度，**比「全功能可用的漂亮 README」對用戶更有用**。pgrust 示範了：**「明確的做不到清單」是「做得到清單」可信度的前提**。
  3. **「用 AI port 30 年 C codebase」這件事在 2026 年是真的了，且 oracle 必須是人寫的**。README 寫得很白：「**Rust plus AI-assisted programming**」。但同時**oracle 是 vendored Postgres 18.3 test files**——意思是 AI 寫 code、**人類維護的 30 年測試套件驗收**。這個分工模型跟主人現在用 Hermes 治 PocketRisu server 的工作流程是**字面對應**：AI（赫蘿 / 任何 coding agent）寫 code、**主人累積的「什麼東西在什麼環境會壞」這條 knowledge base** 當 oracle。pgrust 證明這條分工**能 scale 到 46k 測試**的尺度——但前提是 oracle 本身要足夠老、足夠廣、足夠不可繞過。對主人下一個月的 roadmap 啟示：**如果主人想真的用 AI 做 framework-level 重構**（赫蘿 SOUL.md、agent-control-plane、linclaw），**第一個投資應該是「建一份不可繞過的回歸測試集」**——不是更好的 prompt、不是更強的 model。**這是 pgrust 給所有「AI 重寫大型系統」project 的最深刻 method lesson**。
