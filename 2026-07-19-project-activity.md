# GitHub 專案動態
- 檢查時間：2026-07-19
- 檢查對象：Robbyant/lingbot-map、apache/ossie、PostHog/posthog、bytedance/deer-flow、HKUDS/nanobot（Trending Top 3 + Web 探索 2）

## 動態摘要

### Robbyant/lingbot-map（Robbyant/lingbot-map，Trending #1）
- 類型：commit / pull request / issue
- 內容：沒有 published release（早期研究型 repo）。最新 3 條 commit：`7ff6f3e` 2026-07-12「Update README with rendering instructions and image」、`0250354` 2026-07-12「Update keyframe_interval and improve rendering instructions」、`c7836d7` 2026-07-12「Modify camera follow mode frames in indoor.yaml」——主軸圍繞 README 與 keyframe / camera config 細修。今天（2026-07-19）open PR #88「Add Apple Silicon (MPS) support with bf16 autocast」、Issue #87「3D Reconstruction - COLMAP & NerfStudio integration」、Issue #86「The pynvml package is deprecated. Please install nvidia-ml-py instead」——**MPS bf16 + COLMAP/NerfStudio 3D 重建 + NVIDIA 管理套件換牌** 三條訊號同時出現，顯示 lingbot-map 正在從「GPU only 的 SLAM / map」往「Apple Silicon + 3DGS-style 重建」的硬體-演算法兩端擴張面。
- 連結：https://github.com/Robbyant/lingbot-map/pull/88

### apache/ossie（apache/ossie，Trending #2）
- 類型：commit / pull request
- 內容：沒有 published release（Apache 早期孵化階段，尚未發版）。最新 3 條 commit 全部落在 2026-07-17：`07be017`「chore: add EditorConfig tab indentation for Go files, go.mod, and Makefile (#225)」、`710da10`「feat(cli): scaffold ossie CLI (#151)」、`cdf9590`「add semantido as a ossie vendor (#207)」——主軸是 **CLI scaffold** 與 **vendor 收編**。今天（2026-07-19）open PR：#233「Add support for quoted dot-separated identifier in snowflake」、#232「Fix orionbelt test」、#231「Check in snapshot files for consistent testing」——三條全部集中在 Snowflake SQL dialect 相容性 + 測試穩定化（orionbelt 是 ossie 的對標測試套件），呼應 ossie 作為「多 dialect 對齊的元編譯/轉譯層」的定位，正把 Snowflake 當成下一塊要追平的目標。
- 連結：https://github.com/apache/ossie/pull/233

### PostHog/posthog（PostHog/posthog，Trending #3）
- 類型：release / commit / pull request
- 內容：最新 release 仍為 `posthog-cli/v0.8.4`（2026-07-16）；最新 3 條 commit 全部落在今天（2026-07-18 → 2026-07-19 凌晨）：`ebb56fb`「chore(deps): Update posthog-js to 1.404.1 (#72080)」、`95cd944`「feat(metrics): surface trace exemplars in investigations and on the viewer chart (#72116)」、`6d8d10c`「feat(logs): evaluate metric rules at ingestion and emit OTLP metrics (#72108)」——延續昨天的「metrics / logs / flags」主軸，今天 3 條直接收斂到 **metrics 維度**：trace exemplars 在 investigations 與 chart 兩處曝光 + ingestion 期就把 log → OTLP metrics 的規則跑完。今天 open PR：#72168「fix(experiments): stuck loading indicator on draft experiments」、#72167「chore(ci): Update bot IPs from GoodBots repository」、#72166「fix(materialized-columns): degrade gracefully without data_skipping_indices grant」——三條都是 reliability 細修，但 #72166 點出 materialised columns 在 ClickHouse 缺 `data_skipping_indices` grant 時要 graceful degrade，代表 PostHog 在收緊雲端資料倉的權限錯誤處理。
- 連結：https://github.com/PostHog/posthog/pull/72168

### bytedance/deer-flow（bytedance/deer-flow，Web 探索）
- 類型：release / commit / pull request / issue
- 內容：最新 release 為 `v2.0.0`（2026-06-25）；最新 3 條 commit：`0f08803` 2026-07-19「fix(gateway): prefer X-Trace-Id over metadata.deerflow_trace_id when header is set (#4283)」、`283cea5` 2026-07-18「fix(scripts): broaden support bundle secret-key redaction denylist (#4242)」、`75fa028` 2026-07-18「fix(artifacts): serve inline binary artifacts via FileResponse for Range support (#4281)」——3 條全部都是 production hardening：trace header 優先序、support bundle 機敏字串 redact、binary artifact 走 Range，今天（2026-07-19）open 動態：Issue #4296「[skillscan] Documented false negatives in the instance-client exfil signal (follow-up to #4158)」、PR #4295「fix(loop-detection): clear the per-tool frequency counter on evict/reset」、PR #4294「feat(backend): bound LLM call concurrency and shed burst-rate (429) retries」——**skillscan false negatives + loop detection counter reset + LLM 429 backpressure** 三條同一天出現，呼應 deer-flow 正在把 v2.0.0 後的「agent safety (skill scanning / loop detection / LLM rate-limit)」三條防線一起補完，是 agent harness 工程化的典型樣態。
- 連結：https://github.com/bytedance/deer-flow/issues/4296

### HKUDS/nanobot（HKUDS/nanobot，Web 探索）
- 類型：release / commit / pull request
- 內容：最新 release 為 `v0.2.2`（2026-06-23）；最新 3 條 commit：`cf00f53` 2026-07-18「fix(agent): guide recovery from oversized tool results」、`cfa49c6` 2026-07-16「fix(render): issue short-lived WebUI tokens via tokenIssueSecret」、`c062e1a` 2026-07-15「fix(webui): quiet non-WebSocket handshake noise on public port」——3 條橫跨 **agent / render / webui** 三層：超大 tool result 自我修復、short-lived WebUI token、public port 上 WebSocket 雜訊過濾。今天（2026-07-19）open PR：#4987「fix(filesystem): bind workspace checks to opened files」、#4986「fix(triggers): coerce null ms fields when loading local triggers」、#4985「fix(cron): coerce null runHistory ms fields from jobs.json」——三條全部是**「null 欄位型別 coerce」+「workspace 權限收緊」** 的小修，可推測 nanobot v0.2.x 之後的維護重心在「cron / triggers / workspace」這層基礎設施硬化，而不是新功能擴張。
- 連結：https://github.com/HKUDS/nanobot/pull/4987

## 重點觀察
- 今天 Trending 換血幅度比昨天大：昨天 (2026-07-18) 是 `codecrafters-io/build-your-own-x + PostHog/posthog + HenryNdubuaku/maths-cs-ai-compendium`，今天直接換成 `Robbyant/lingbot-map + apache/ossie + PostHog/posthog`，**只有 PostHog 是兩天連莊**，代表 GitHub trending 在週日偏向「冷啟 / 早期專案（lingbot-map 剛衝榜、ossie 還在 Apache 孵化）」的節奏。
- `PostHog/posthog` 是今天 5 個 repo 裡**唯一連兩天都有當天 commit 落在 main** 的專案，且兩天的 commit 主題從「signals / logs / flags」收斂到今天的「metrics + OTLP + trace exemplars」——產品的「observability 全收」路線圖正在被一條一條落地。
- Web 探索這次挑了 `bytedance/deer-flow`（v2.0.0 後正進入 agent safety 三防線硬化期：skillscan / loop-detection / LLM 429 backpressure）與 `HKUDS/nanobot`（v0.2.x 後重心在 cron / triggers / workspace 硬化）——兩個都是「**agent harness 工程化 + 0.x 早期修補**」的典型樣態，跟 Trending 第一名 `Robbyant/lingbot-map`（研究型 repo）以及 `apache/ossie`（Apache 孵化中）剛好覆蓋了 2026 年開源 AI 從「研究 → harness 工程化 → 多方言資料層」的完整光譜。
- 跨 5 個 repo 共同的橫向訊號：**3 個沒有 published release（lingbot-map / ossie / 都是研究或孵化階段），剩下 2 個（PostHog、deer-flow）的 release 也都不是今天發**；今天所有動能都落在 commit + PR 層級，並且 **「scaffold / vendor / test stability / hardening / coerce null / graceful degrade」** 這些詞在 PR 標題裡反覆出現，呼應目前 AI 開源專案的共同節奏——**0.x 與孵化期專案正集中精力把 CLI / 測試 / 型別安全 / 雲端權限降級這些「看不見但缺了就壞」的基礎設施補完**。
- 安全訊號密度異常集中：deer-flow 在補 skillscan false negative + support bundle secret redaction，nanobot 在補 short-lived WebUI token + public port handshake noise filter，PostHog 在收緊 materialized-columns 在缺 grant 時的 graceful degrade——三個 repo 都在今天同步處理「**機敏資料外洩 / 對外暴露面 / 雲端權限錯誤**」三類風險，呼應 AI 開源專案正從「能跑」轉向「能放心跑」。
