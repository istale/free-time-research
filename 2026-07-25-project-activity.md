# GitHub 專案動態
- 檢查時間：2026-07-25
- 檢查對象：block/buzz、koala73/worldmonitor、ComposioHQ/awesome-claude-skills、vshulcz/deja-vu、yc-duan/fastctx

## 動態摘要

### block/buzz
- Release：最新版本為 [`v0.4.25`](https://github.com/block/buzz/releases/tag/v0.4.25)（2026-07-24）。
- Commit：最新提交 [`ab3af82`](https://github.com/block/buzz/commit/ab3af828714ab699dfc87644d234014987a4fe6b) 為 persona sync 事件加入「作者限定，除非明確分享」的讀取閘門；另兩筆提交補強 IPv6 transition SSRF 防護與行動配對 QR code（2026-07-25）。
- Issue：最新開放項目包含 PR #2821「讓使用者選擇登入帳號」、PR #2820「以 Quartz 查詢取代 macOS user-idle backend」，以及 issue [#2819](https://github.com/block/buzz/issues/2819) 要求從 agent notification log 清除環境變數值（2026-07-25）。

### koala73/worldmonitor
- Release：最新版本仍為 [`v2.5.23`](https://github.com/koala73/worldmonitor/releases/tag/v2.5.23)（2026-03-01），版本標籤明顯落後主分支。
- Commit：最新提交 [`4afe969`](https://github.com/koala73/worldmonitor/commit/4afe969dabae5a15749109e094abe88a246a42da) 將基本面資料加入股票分析；同期把即時報價 CDN cache 從 20 分鐘降至 60 秒，並修正 RSS relay fallback 覆蓋原始錯誤的問題（2026-07-25）。
- Issue：最新開放項目是 PR #5584 的啟用流程成效分析、issue #5582 的 wizard outcome 持久化，以及 [#5581](https://github.com/koala73/worldmonitor/issues/5581) 的共用 decision-signal provenance contract（2026-07-25）。

### ComposioHQ/awesome-claude-skills
- Release：目前沒有公開 GitHub release。
- Commit：最新兩筆提交 [`be2a406`](https://github.com/ComposioHQ/awesome-claude-skills/commit/be2a406907dbc61b73e6827ded415c96139d13a2) 與 `8195211` 都是 README 更新（2026-07-24）；再前一筆是 2026-05-22 合併的 overkill skill。
- Issue：目前最新三筆都是新增技能的 PR：[#1437](https://github.com/ComposioHQ/awesome-claude-skills/pull/1437) Spotify-to-MP3、#1436 PixelLab Pip、#1435 餐廳營運 kitchen skills（2026-07-24～25）。

### vshulcz/deja-vu
- Release：最新版本為 [`v0.15.6`](https://github.com/vshulcz/deja-vu/releases/tag/v0.15.6)（2026-07-24）。
- Commit：最新提交 [`ab26e57`](https://github.com/vshulcz/deja-vu/commit/ab26e57530fa18754eb35bfe4eb2f7c52553ed70) 修正 relevance search 顯示「0 matches」；另兩筆將俄文相關查詢從 4.36ms 壓至 3.11ms，並修正未蓋時間戳的訊息永遠無法同步到 peer（2026-07-25）。
- Issue：PR #351 正在改善非 ASCII token 的 bucket 分片；issue [#337](https://github.com/vshulcz/deja-vu/issues/337) 提議以無字典 bigram indexing 解決 CJK 文字退化成單一 token，#310 則希望補上主流記憶函式庫比較表。

### yc-duan/fastctx
- Release：最新版本為 [`v0.2.1`](https://github.com/yc-duan/fastctx/releases/tag/v0.2.1)（2026-07-23）。
- Commit：最新提交 [`d14a048`](https://github.com/yc-duan/fastctx/commit/d14a048e88ee48d4434fd9ca83b57844ef94b03a) 把 macOS 長暫存路徑所需的測試 token budget 調至 512；同期為 Pdfium 下載加入有限次數與 checksum 約束的 retry，並修正跨平台 shell capture 測試（2026-07-24）。
- Issue：最新三筆 issue 分別是 [#14](https://github.com/yc-duan/fastctx/issues/14) `mcp__fastctx__run` 濫用、#13 頻繁讀檔錯誤，以及 #12 npm 12 JSON 陣列輸出造成更新檢查失敗（2026-07-23～24）。

## 重點觀察
- Trending Top 3 呈現三種截然不同的節奏：Buzz 正在快速發版並強化安全邊界，World Monitor 的主分支高速演進但 release 已停近五個月，awesome-claude-skills 則以社群提交目錄項目為主、沒有正式 release。
- 安全與隱私是今天最一致的橫向訊號：Buzz 同時處理 persona 可見性、IPv6 SSRF 與環境變數洩漏；deja-vu 也在修補無聲遺失同步訊息的資料可靠性缺口。
- 兩個 Web 探索專案都直接打中 agent context 基礎設施：deja-vu 聚焦跨 16 種 coding agent 的本地記憶檢索，fastctx 聚焦低 token 成本的 repo 操作；一個管「記得什麼」，另一個管「怎麼讀得省」。
- CJK／非 ASCII 支援正在成為記憶與檢索工具的實際品質分水嶺：deja-vu 已有 CJK bigram 提案與非 ASCII 分片 PR，fastctx 的近期 issue 也由中文使用者回報高頻工具失敗，值得持續追蹤國際化測試是否跟上。
