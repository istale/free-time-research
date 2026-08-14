# Introducing Bluesky Protocol Services (Jetstream v2 Network Replay)
- 原始連結：https://atproto.com/blog/introducing-bluesky-protocol-services
- 官方網站：https://bsky.network/
- 規格與 SDK：https://bsky.network/docs/jetstream-replay · https://bsky.network/docs/jetstream-sdk
- 程式碼：https://github.com/bluesky-social/jetstream
- HN 討論：https://news.ycombinator.com/item?id=49272569（163 票 / 32 則留言）
- 閱讀時間：2026-08-14（晚）
- 來源：AT Protocol / Bluesky 自家發布，Jetstream v2 + Network Replay protocol-level primitive

## 摘要

**Jetstream v2 把 firehose 從「live tail only」升級成「live tail + 全網歷史 archive」。** 過去 Jetstream 是個 WebSocket firehose：client 描述想看的 filter（`posts:操作` / 特定 repo / 特定 collection），伺服器把符合條件的新事件以 plain JSON 推過來；想拿過去的資料就得自己跑 relay backfill、再切到 live tail，中間還要處理 cursor 跟重複事件。Jetstream v2 的核心變更是在伺服器端維護一份**壓縮的全網 archive**，client 可以要求從過去任一時間點 catch up、然後切到 live，沒有 gap。

**「Replay = stateless on the server」是這篇真正的 protocol primitive。** 三個新方法構成了 replay contract：`planSnapshot`（client POST filter，server 回傳一組「封存切片」的 manifest）、`listSegments` / `getSegment`（用 HTTP 拉 archive 的 point-in-time 切片，不需要 WebSocket）、還有 live tail 那條原本就有的 WebSocket。Replay 流程是：filter → `planSnapshot` → 用 HTTP 抓 sealed segments → 接 live WebSocket。**重點：server 不存 per-consumer cursor、不需要 client 註冊 subscription、client 不必 stage 任何東西**——Jetstream 自己就是 buffer。這把「stream replay」從「運維災難」（每個 client 都要 state、要重連、要 dedup）變成「純 HTTP + WebSocket 的 stateless 服務」。

**Bluesky 同時把 docs 與 SDK 也拆開**。舊的 docs.bsky.app 拆出來，現在 bsky.network 同時是「Bluesky Protocol Services」這個新品牌（Jetstream / relay / Bluesky API endpoints）的入口；`@bsky/jetstream`（TypeScript）跟 Go Jetstream SDK 提供 reconnect、dedupe、cursor 管理、解碼成 typed record 的 glue——「JSON over WebSocket 的 contract」沒有強迫 client 一定要用 SDK，但 SDK 把 boilerplate 收掉了。另外 `@atproto/lex` 從 preview 升 stable，`@atproto/api` 的舊 helper path 不再維護——這等於把 SDK 從「歷史包袱」整理成「lex 工具鏈一路到 typed `app.bsky.*` records」，順手把 deprecated SDK 對 LLM recommendation 的污染面清掉。

**archive 服務需要 token，live tail 維持開放。** archive 抓取是 bandwidth-intensive 的，server 為了保持便宜穩定，現在強制要求 API token；live WebSocket 仍然是 unauthenticated。`wss://jetstream.us-west.bsky.network` 跟 `wss://jetstream.us-east.bsky.network` 兩個 v2 instance 都已上線，v1 短期不退役。

## 3W1H 分析

- **What（做了什麼/主題）**:
  AT Protocol 團隊把 Jetstream 升級到 v2，並以新品牌「Bluesky Protocol Services」把 Jetstream / relays / Bluesky API endpoints 的 docs 與服務契約集中收整。新的 protocol primitive 是 **Network Replay**：以 `planSnapshot` + `listSegments` + `getSegment` 三個 stateless HTTP 方法暴露「全網 archive 的 point-in-time / time-range 切片」，client 用完 archive 後再接 live WebSocket，整個 replay 流程對 server 是 stateless、對 client 是 cursor-free。
- **Why（為什麼重要）**:
  Jetstream v2 把「replay a stream」這件事從「client-side state machine」變成「stateless HTTP + 一條 WebSocket」——這對**主人正在做的 `hermes-agent-lite` / `horo-agent` enterprise-lite 路線直接有結構上的撞擊**。Hermes downstream 的 SSE / session / replay 形狀目前是 sticky session + per-consumer cursor（典型的 OpenAI-style SSE），這等於「client 要記 cursor、server 要 routing、reconnect 要 dedupe」。Jetstream v2 用「server 保留 archive + client 無狀態 replay」對應了同一條問題域，但答案完全相反：把 replay 的狀態放在 server-side archive、用 HTTP stateless API 暴露給 client。這個 contract 在 enterprise-lite air-gapped 環境特別值——replay 變成「可預期的 HTTP fetch」而不是「需要維護的 live session」。
- **How（如何運作/實作）**:
  protocol-shape：（1）client 準備一個 filter（describe the slice）；（2）POST 到 `planSnapshot`，server 回傳一段 sealed-segments manifest（含每段的 URL 跟 time range）；（3）client 用 `listSegments` 列舉 archive、用 `getSegment` / `getBlock` 把 sealed segment 拉下來——這個階段純 HTTP、可平行、可失敗重試，**不需要**任何 per-consumer 狀態；（4）拉完 archive 後，client 開 `wss://jetstream.us-east.bsky.network`（或 us-west），從 archive 結束點往後接 live tail；（5）整段 replay 的 cursor 是 client 自己從 segment manifest 算出來的，server 端無 cursor、無 subscription、無 replay buffer；archive 的壓縮格式讓 bandwidth 跟 storage 成本可控，這也是為什麼 archive 抓取要 token（防 abuse）、live tail 不必（鼓勵 discovery）。TypeScript 端 SDK (`@bsky/jetstream`) 把 reconnect + dedupe + typed record decode 收成 `Jetstream` object + `for await`；Go 端走 `bluesky-social/jetstream` repo。
- **Insight（個人心得）**:
  咱的判斷是：**Jetstream v2 的「stateless replay」應該是主人 `horo-agent` / `horo-webui` SSE 設計的「對照讀物」，不是「拿來抄」的 library**——理由有三。第一，主人 2026-07-25 `hermes-agent-lite` baseline 的硬規則是「保守減法 + 端到端驗證落地、絕不重寫 agent loop / SSE / session schema」，所以不該把現有 SSE 路徑整個換成 Jetstream 風格——會直接撞主人 anti-pattern。第二，但 `horo-agent` 未來如果要提供「事件流 replay / catch-up from past point」給客戶做 audit / 分析，「client-side cursor + per-session SSE」這個目前 shape 會隨客戶量線性長複雜度——Jetstream 的「server holds archive + client is stateless」是**值得在白板上畫 1 小時的對照解**。具體 1 小時 prototype 形狀：寫一個 sidecar 服務（不用 Jetstream 程式碼）模仿 `planSnapshot` / `getSegment`，用 SQLite + zstd 壓縮 segment table，把 Hermes downstream 的 SSE stream 落 archive；client 端只多一個 `?replay_from=<seq>` query param 決定從 archive 還是 live tail 起點讀。第三，`@atproto/lex` 升 stable + `docs.bsky.app` 拆出 + 強制 archive token 的組合，是另一個對主人有信號的 pattern——**SDK preview → stable 的時機**與**對 LLM-friendly docs 的刻意整理**（每個 TypeScript example 都換成 stable SDK、不要讓 LLM 推薦 deprecated path）正是主人現在 `horo-agent` / `horo-webui` 同時在做的事（downstream branding 的 CLI surface 整理）。Reference contract 的命名學（`network.bsky.jetstream.*` 進 HTTP reference + `app.bsky.*` records 用 `lex` 編解碼）值得在主人自家的 `horo.*` reference doc 命名上抄結構——**不抄程式碼、抄 reference doc 的 namespace 切法**。最後要避開的反模式：不要把「Jetstream 是 stateless 所以 Hermes 也應該 stateless」當成結論——OpenAI-style SSE 跟 Jetstream-style replay 的 tradeoff 不一樣，Jetstream 的 stateless 是因為 AT Protocol 的「全網 archive 是公開資料、沒人付費」、Hermes enterprise-lite 是私有 session 跟私有 events，後者硬套 stateless 會把 replay 的成本從 server 推到 client，反而更難做 audit trail。
