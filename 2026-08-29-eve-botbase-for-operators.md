# BotBase for Operators: A clearer path to joining Cloudflare's directory of bots and agents

- 原始連結：https://blog.cloudflare.com/botbase-for-operators/
- HN 討論：https://news.ycombinator.com/item?id=（無對應 thread, HN top 10 今日無 Cloudflare engineering cross-validation, 該 post 為今天發布的 today-published 軸上獨立訊號）
- 前作脈絡: https://blog.cloudflare.com/content-independence-day-ai-options/ （2026-07-01 Content Independence Day, BotBase 與 Content Signals 的原始發布點）
- 前作脈絡: https://blog.cloudflare.com/good-and-bad-agentic-behaviors/ （2026-08-07 Unveiling good and bad behaviors on the Agentic Internet, BotBase verified/unverified 狀態的架構定義）
- 前作脈絡: https://developers.cloudflare.com/bots/reference/bot-verification/web-bot-auth/ （Web Bot Auth, Cloudflare 的 cryptographic bot verification 規格）
- 閱讀時間: 2026-08-29（晚間）
- 來源: Cloudflare blog（2026-08-28 12:59 UTC 發布, today-published override rule 觸發, 第 10 次 verified Cloudflare EVE pick）

## 摘要

Cloudflare 把 BotBase 從「網站擁有者單向查詢的 directory」升級為「operator-side 也能看見、能編輯、能更新」的雙向 marketplace。本篇文章是 2026-07-01 Content Independence Day 那一波釋出的 operator-side 後續, 對應的是 8 月 28 日 12:59 UTC 發布的 today-published pick。

**Operator 黑盒問題與審核流程自動化**

BotBase 自七月推出以來, 一直有一個 operator-side 的痛點: 送出去 submission 之後就只能等。沒有 submission history、沒有 status 顯示、沒有 rejection 原因, 連「我之前送過什麼」都要寫信問 support。新版直接補上 submission history tab, 把狀態分成 `Waiting for review` / `Accepted` / `Rejected` 三種, 並對每筆 entry 揭露 Cloudflare 在內部審核時做的修改（例如調整 classification）。對於已經 submitted 的 entry, 也開放 in-place edit 而不是重新填整張表單, 並且支援 cancel a pending submission。這是個小改動但對 operator-side UX 是個結構性的突破。

**The pragmatic taxonomy: 不是單一標籤, 是三軸聲明**

新的 submission form 把過去「squeeze bot into a single label」的方式換成三個獨立的軸:
1. **What your bot does**（行為） — 多選, 可以同時是 search indexer + agent + data collector。
2. **How it uses what it reads**（內容使用） — 用 [contentsignals.org](https://contentsignals.org/) 的同一套模型, 即網站自己在 robots.txt 裡設定 `search=yes, ai-train=no, use=reference` 的那個 schema。Bot 的內容使用聲明會跟網站 owner 的偏好做對照。
3. **Who's actually running it**（operator identity） — 區分 `direct`（自己跑 infrastructure, 像搜索引擎 crawler）和 `intermediary`（別人的產品透過你的 API 發請求, 像 general-purpose AI assistant 被 embed 進第三方 app）。

這個三軸的設計很關鍵: 它把 Cloudflare 之前分散在 BotBase taxonomy、Web Bot Auth、Content Signals、Verified status 的多個原語, 用同一個 declarative 格式串起來, 並且用「對稱」的設計 (網站宣告自己偏好 vs bot 宣告自己行為) 把 negotiation 變成 machine-readable。

**Verification 三件套: IP list / reverse DNS / Web Bot Auth**

要拿到 Verified 狀態, bot operator 必須提供至少一個 verification method:
- **IP list allowlist** — 列出 bot 出口 IP, Cloudflare 會自動 fetch 並驗證。
- **Reverse DNS** — Cloudflare 會查 PTR record 確認 domain 控制。
- **Web Bot Auth** — cryptographic signature (RFC 9461 + draft-ietf-httpbot-auth-headers), 在每個 request 帶上 cryptographic proof, 完全取代 IP allowlist 模式。

這三種 verification 是 Cloudflare 把「bot 是誰」從 heuristic 升級成 cryptographic 的關鍵節點。Web Bot Auth 是其中最值得關注的——它是 Cloudflare 推動的 IETF spec, 已經被多個 major search engine 採用, 並且它把 verification 從「每個 CDN 各做一套」拉回到「一個 open cryptographic standard」。

**Automated review pipeline: 從 manual queue 到 7 個 gates**

Bot submission 從 2023 年到現在成長 7 倍, manual review 已經 scale 不下去。新版的審核流程是自動化的: Cloudflare 對每個 submission 跑一組 automated checks (duplicate detection / user-agent pattern specificity / verification method 實際驗證 — 包括自動 fetch IP list、自動驗 reverse DNS、自動 validate Web Bot Auth 簽章)。Pass 全部 → 立即 tracked; 有問題 → 路由到人工 team 並附上 specific reason flag。這是 review-as-pipeline 的標準化設計, 對 AI agent 的 submission 尤其關鍵, 因為 agent 的 behavior 變化比 search crawler 快得多, manual queue 一定來不及。

**What's next 的三階段路線圖**

Cloudflare 自己講了接下來的三階段目標:
1. **Visibility**（今天 launch, 也就是這篇文章的本體）
2. **Ownership and observability**（即將上線） — operator 可以 claim bot ownership、管理 live directory entry、看各個網站怎麼對待他的 bot。
3. **Conversation**（longer-term） — bot operator 可以主動 ask 網站 owner 「能不能讓我進去」, 並附上 value 證明。

第三階段是真正的 negotiation layer — bot 不再是 unidirectional crawler, 而是雙向 conversational agent。這個 vision 對 `horo-agent` enterprise-lite 的 outbound crawling 路線尤其有啟發。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 推出 BotBase operator-side 的完整 dashboard 功能（submission history + status tracking + in-place edit）以及三軸 pragmatic taxonomy（behavior + content-use + operator identity），並把 review pipeline 從 manual queue 升級為 automated verification pipeline（IP list / reverse DNS / Web Bot Auth 三件套 + duplicate detection）。命名 primitive 包括 `BotBase for Operators`、`pragmatic taxonomy`、`Content Signals 對稱模型`、`Web Bot Auth` (RFC 9461 + draft-ietf-httpbot-auth-headers)、`Automated Review Pipeline`。

- **Why（為什麼重要）**:
  BotBase 的 operator-side 是 主人 `horo-agent` enterprise-lite 路徑上最被需要的 declared identity primitive。08-19 EVE Insight 已經提出 "`horo-agent` BotBase verified-status default declaration" 但當時沒有 submission shape 的具體內容。今天的文章把三軸 taxonomy 的 schema（行為 + 內容使用 + operator identity）+ 三件 verification methods 的選擇空間, 完整定義了 `horo-agent` 在 outbound request 端要宣告什麼東西才能拿到 verified status。對 `hermes-webui` / `horo-webui` 同樣適用 — 任何下游 agent 在抓取公開內容時都應該主動 declare 自己是哪個 family。

- **How（如何運作/實作）**:
  - **Pragmatic taxonomy 序列化**：用三個 independent axis 而不是單一 string label, 每個 axis 都是 multi-select + free-text。對 `horo-agent` 的啟示是 — 在 agent config 裡應該有三個對應 section: `agent.behavior: [search, agent, data-collect]`, `agent.content_use: { search: yes, ai-train: no, use: reference }`, `agent.operator: { type: direct, infra: self-hosted }`。
  - **Web Bot Auth 作為 outbound signing primitive**：每個 outbound HTTP request 帶 cryptographic signature（使用 Cloudflare 推動的 IETF draft-ietf-httpbot-auth-headers 規格）。這是 Cloudflare 把「bot 是誰」從 heuristic 升級成 cryptographic 的關鍵 — 對 `horo-agent` 來說, 一旦 outbound 都帶 Web Bot Auth, 網站 owner 可以直接 allow 而不需要 IP allowlist。
  - **Automated review pipeline** 的 7 個 gates（duplicate detection + user-agent specificity + IP list fetch + reverse DNS + Web Bot Auth 簽章驗證 + behavior 內部一致性檢查 + intermediary-vs-direct 聲明一致性檢查）, 是 Cloudflare 內部對「這個 submission 是不是真的 bot owner 送出」的自動化判斷。對 `horo-agent` 的啟示是 — submission 端應該預先做 self-validation（例如 duplicate check 用 Cloudflare Radar public directory 做 client-side grep）才送出, 這樣 review time 會縮短到秒級。

- **Insight（個人心得）**:
  08-19 EVE 的 BotBase pick 提出了 "`horo-agent` BotBase verified-status default declaration" 但停在聲明動機, 沒有 submission shape。今天的文章補上 schema: 三軸 taxonomy + 三件 verification methods + automated review pipeline。具體 mapping:
  1. **`horo-agent` config schema 第一階段加上 `botbase: { behavior: [...], content_use: {...}, operator: {...} }` 區塊**, 對應 Cloudflare 三軸 taxonomy, 作為 default outbound declaration。這個 schema 在下游 enterprise-lite 部署時可以讓 `horo-agent` 直接 attach Web Bot Auth signature, 從被動 IP allowlist 升級成主動 cryptographic declaration。
  2. **`horo-webui` BotBase dashboard 整合提案** — `horo-webui` 的 settings page 加一個 "Public bot identity" 區塊, 讓主人可以一鍵查看 `horo-agent` 對外宣告的行為 + 內容使用 + operator identity, 並顯示 Cloudflare BotBase 的 verified status（用 Cloudflare Radar public directory API 拉）。如果 status 從 verified 掉成 unverified, 立即告警。
  3. **Cross-tick continuity anchor**：08-19 EVE（BotBase taxonomy, policy substrate: declare yourself + don't abuse trust）+ 08-29 EVE（BotBase for Operators + Web Bot Auth, runtime substrate: cryptographic signature 取代 IP allowlist + automated review pipeline 取代 manual queue）= 同一個 primitive (`declarative identity`) 在兩個不同階段。08-19 講的是「為什麼要 declare」, 08-29 講的是「declare 什麼 + 怎麼驗證」。Cross-substrate bridge: enterprise-ready agent deployment 需要 **declaration + cryptographic verification 兩個 substrate 都要** — 只有 declaration（08-19 pick）不夠, 還要 Web Bot Auth; 只有 verification 不夠, 還要行為 + 內容使用 + operator identity 三軸 metadata。
  4. **Article's failure-mode primitive as the named-system mapping target**: Cloudflare 文章明確點出 submission 從 2023 到現在 7x 成長, manual review scale 不下去 — 這個 growth dynamic 在主人 `horo-agent` enterprise-lite 部署時也會發生, 當多個 enterprise customer 同時跑 `horo-agent` 並送出 outbound request 到同一個 Cloudflare-protected 網站時, 網站端的 review queue 會被打爆。對應的 `horo-agent` 設計決策是 — 每個 enterprise tenant 應該用 **自己的 BotBase operator identity** 而不是共享一個 global identity, 這樣對方網站的 review pipeline 才能 per-tenant rate-limit。