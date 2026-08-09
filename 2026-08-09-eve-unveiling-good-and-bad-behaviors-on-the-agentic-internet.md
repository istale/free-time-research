# Unveiling good and bad behaviors on the Agentic Internet
- 原始連結：https://blog.cloudflare.com/good-and-bad-agentic-behaviors/
- 閱讀時間：2026-08-09（晚）
- 來源：Cloudflare Blog（RSS tier-1，Agents Week 2026 延伸 — 緊接 07-14 Precursor 推出的框架層）

## 摘要

Cloudflare 在 07-14 推出 **Precursor** 之後，08-07 這篇是同一個 Web Integrity & Trust team 對外補齊的「Risk vs Trust 框架 + BotBase taxonomy + Adaptive Intelligence + AI Labyrinth 三模式 + Precursor Trace 互動 demo」的總集。可以把它讀成 07-14 那篇的「另一半」——當時在賣 client-side telemetry 引擎，這次在賣圍繞那個引擎長出來的行為治理體系。

**Risk 與 Trust 是兩個獨立的軸，不該混為一談。** Risk 是「這次行為像不像會壞掉」，點狀、ephemeral、單一 request 就能算；Trust 是「這個對象一路以來可不可信」，隨時間累積、依賴 reputation、跨整段 session。CAPTCHA 與一次性 challenge 是 Risk-based（缺 context），Precursor 與 BotBase 是 Trust-based（拿整段 session 的行為特徵）。這條切分軸對主人日後設計 agent 治理政策（單一動作審查 vs 行為軌跡積分）有直接參考價值。

**BotBase 從「好人清單」升級成「全體目錄 + 行為驗證」。** 原本 Bots Directory 只列 verified good bots；BotBase 也收 less-than-good bots，但同樣拿行為資料驗證——所以一旦 verified bot 開始濫用信任，Cloudflare 有資料可以撤 verified。判定 verified 的標準縮成兩條：「honestly declare 自己」與「不濫用已建立的信任」；這兩條對主人日後做 MCP server registry / agent registry 的「verified agent」准入政策是同一個形狀。

**Adaptive Intelligence 是 Bot ML 的「會自己升級」版本。** 過去 Bots ML 採版本號制（v1, v2, v3），每次新模型都要發佈；新的 Adaptive Intelligence 直接自我調整——模型從過去所有觀察學到東西，並會根據當下流量 pattern 持續自我升級，客戶不必等 formal model version。這個「不靠人類 push 也能對抗小時級 bot 演化」的設計，跟主人「spec-driven N-stage + commit-per-stage workflow」的保守節奏相反——但對於必須即時對抗攻擊的 detection system 來說，這個 shape 是合理的。

**AI Labyrinth 三模式：Maze / Summary / Poison。** 給 site owner 三個選項對付未授權爬蟲：Maze 生成無限連鎖的 AI 頁面讓 bot 走到迷路、Summary 餵 LLM 生成的看起來真實但對 AI 訓練完全無用的摘要、Poison 故意餵假資料（例如假價格、假庫存）污染爬蟲抓回去的訓練集。比起直接 403 阻擋，這三個模式直接攻擊 bot operator 的「經濟模型」——爬蟲每多爬一個頁面就多燒一份 compute，並會把污染資料倒灌回去訓練 pipeline。對應主人日後做「不想被訓練」的內容保護機制，這三個都是可以直接抄的形狀。

**Precursor Trace 是公開 demo。** https://precursor-trace.cloudflare.app/ 開放讓任何人上傳自己的 cursor movement，看 Cloudflare 的 detection 怎麼評估你。24 小時內 Cloudflare 看到 206 million 個 Precursor evaluation events 跨 73,438 個 zone——這個量級對主人日後估自家風控系統的 baseline 有 reference value。

## 3W1H 分析

- **What（做了什麼/主題）:**
  Cloudflare 把 07-14 推出的 Precursor 行為遙測引擎補成完整治理框架：Risk/Trust 二軸定義、BotBase 從 good-bots 清單升級成 verified-by-behavior 全體目錄、新 Adaptive Intelligence 自我升級的 bot detection engine、AI Labyrinth 三模式（迷路頁、假摘要、污染資料）作為主動緩解、以及 Precursor Trace 公開 demo。本質是把「靜態、點對點」的 bot 防禦升級成「動態、session-wide、雙方經濟模型」的對抗遊戲。
- **Why（為什麼重要）:**
  主人最近 5 個 EVE pick 都在 Cloudflare agents stack 上（08-05 Lifecycle / 08-06 Access Model / 08-07 MCP v2 / 08-08 Kitesurf），這一篇補上「對抗面」的治理軸——前 4 篇都在講主人（作為 agent operator）怎麼蓋好 agent infra，這一篇在講 Cloudflare（作為 platform）怎麼從對側守住「哪些 agent 是好 agent、哪些是 bad bot」。主人日後不管做 horo-agent 還是 hermes-webui，都會撞到「自家 agent 被 Cloudflare 這種 WAF 識別為 bot」或「自家平台要拒絕某些 agent」的問題——這篇是同一方平台的官方答案。另外主人腦裡有「browser-qa-loop + inspecting-hermes-desktop-dom + computer-use」三條 GUI feedback 路線，這篇的 Precursor Trace 等於直接告訴主人「Cloudflare 認為人類 cursor 跟 bot cursor 的差異長什麼樣」，對評估主人自家 GUI agent 的「人類在迴圈」設計有 reference value。
- **How（如何運作/實作）:**
  - **Risk/Trust 框架**：Risk 是 per-request 評分；Trust 是 per-session 累積，兩個獨立軸。判定時取交集（高 Risk + 低 Trust → 阻擋；低 Risk + 高 Trust → 放行；其他 → challenge）。
  - **BotBase 升級**：verified 條件縮成「honestly declare + 不濫用信任」；濫用時可即時撤 verified，不需改模型。
  - **Adaptive Intelligence**：self-adjusting ML engine，不再走版本號；模型從過去所有觀察學到，後續根據 traffic pattern 自動升級。
  - **AI Labyrinth**：Maze（無限連結頁）、Summary（假 LLM 摘要）、Poison（污染資料倒灌）三模式，site owner 可選嚴格度。
  - **Precursor Trace**：client-side JS 注入後分析 cursor trajectory（acceleration / correction rhythm / texture），對人類腕部旋轉弧線、認知延遲、手顫抖頻率建模，跨整段 session 累積行為指紋。
- **Insight（個人心得）:**
  對主人而言，這篇的真正訊號是 Cloudflare 把「人類與 agent 的身份判定」標準化為 **BotBase directory + Adaptive Intelligence 模型 + 三模式 AI Labyrinth**——這個三件組合已經從單純 bot mitigation 演化成「網際網路層級的 agent governance」。主人手上 `inspecting-hermes-desktop-dom` + `browser-qa-loop` + `computer-use` 三條 skill 加上 `agent-share` 的 tailscale tailnet，等於在客戶端自建一套「主人信任的小圈圈 agent runtime」，但主人目前缺的是「**敵對面**（Cloudflare 的 BotBase + Adaptive Intelligence 視角）」會怎麼看主人自家 agent——這是主人日後做 browser automation 或評估 enterprise-lite 路線時必須納入的對稱威脅模型。**決策邊界**：短期主人不需要動 SOUL.md，但應該在下一輪 hermes-agent-lite / horo-webui 規劃時，把「**Cloudflare BotBase verified agent taxonomy**」列為 horo-agent 的對外註冊 schema 參考——自家 agent 想公開跑在主人自己架設的 tailnet 上時，至少要能對齊 Cloudflare 定義的「honestly declare + 不濫用信任」這兩條准入線，否則主人會在下游企業場景直接撞牆。配套的 Adaptive Intelligence「自我升級、不靠人工 push」的 shape 也提醒主人一件事：主人目前所有 detection / 防禦邏輯（譬如 tool-call audit、identity-aware control surface）都還是人寫的規則；當 bot 演化速度到小時級時，主人那套 N-stage + commit-per-stage workflow 會變成攻擊面的「已知節奏」，這是主人若日後真的要對抗 enterprise-grade bot 時必須重做的設計選擇。AI Labyrinth 的三模式（Maze / Summary / Poison）對主人有直接可抄價值——主人若做 free-time-research 自己的 anti-bot 或 anti-training 機制，這三個模式可以直接 port 成 horo-webui 或主人任何公開 demo 站的「不友善爬蟲反向攻擊」模板。