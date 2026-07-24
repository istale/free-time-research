# GitHub 專案動態
- 檢查時間：2026-07-24
- 檢查對象：block/buzz、koala73/worldmonitor、shiyu-coder/Kronos、earendil-works/pi、mcp-use/mcp-use
- 來源：Trending Daily Top 3 + 自由探索 Web Search

## 動態摘要

### block/buzz（蜂鳴代理）
- 類型：release / commit / pull request
- 內容：最新 release 為 `v0.4.24`「Buzz Desktop v0.4.24」（2026-07-23），重點是 Windows console / WSL alias 修正（#2587）；今天（2026-07-24）連續三筆 commit 落地：gzip git smart-HTTP 解壓（#2670）、agent harness 預設值文件化（#2601）、mobile release check 放寬（#2636），同時間 PR #2681 / #2680 / #2679 也集中在同一天合入，顯示專案目前正處於密集迭代、桌面端細修 + 行動端鋪路的階段。
- 連結：https://github.com/block/buzz/releases/tag/v0.4.24

### koala73/worldmonitor（世界監控台）
- 類型：security fix / commit / pull request
- 內容：最新 release 仍為 `v2.5.23`（2026-03-01，已停滯約 4 個月），但 main 分支活動非常活躍：今天（2026-07-24）就出現 `c4cb091` 安全修正（升級 find-my-way 至 9.7.0，緩解 GHSA-c96f-x56v-gq3h HTTP/2 DDoS，#5xxx 系列），前兩天則補上 API 每日上限回傳 429 與 EIA forecast grace 修正；PR #5534 / #5533 / #5532 都是 2026-07-24 當天更新的付費 / onboarding 微調，屬於「沒發版、但 main 上拼命修」型。
- 連結：https://github.com/koala73/worldmonitor/commit/c4cb091

### shiyu-coder/Kronos（克羅諾斯）
- 類型：issue / pull request / 其他
- 內容：此 repo 仍**沒有任何 GitHub release**，最新 commit 仍停在 2026-04-13 連續三筆 Merge PR（#243、#234、#232），主要在收 topk / dimension 訓練相關的修補；真正值得注意的是今天（2026-07-24）出現的 issue #352「train Kronos Large model」，以及近兩天推上來的 TradingView-like 介面 PR（#350），社群仍有期待但 repo 主線活動明顯降溫。
- 連結：https://github.com/shiyu-coder/Kronos/issues/352

### earendil-works/pi（π 代理工具組）
- 類型：release / commit / issue
- 內容：最新 release 為 `v0.81.1`（2026-07-21），上一個 release `v0.82.0` 在 commit `083e616`（2026-07-24）才剛切版號 — 也就是說今天 main 已經走在 v0.82.0 之上，但 GitHub release 頁還停在 v0.81.1；今天同時合入了「generated image models 更新」與「[Unreleased] 區塊」整理。Issue 方面 #7048、#7047 都是 2026-07-24 剛開的真實 bug（compaction 摘要被截斷、google-generativeai 把 Gemini 3.x tool-call ID 吃掉），代表這個工具組在 production 環境正在被實際使用。
- 連結：https://github.com/earendil-works/pi/issues/7047

### mcp-use/mcp-use（MCP 全端框架）
- 類型：release / commit / pull request
- 內容：最新 release 為 `mcp-use@1.34.3`（2026-07-08），今天（2026-07-24）的 PR 集中在伺服器端底層：#2007 降 Node runtime overhead、#2006 升級 hono 4.12.23→4.12.27、#2001 把 provider audience 與 MCP resource 分開處理（OAuth proxy 修補）；最近一筆 commit（2026-07-21）剛補上 inspector 的 self-hosting guide，整體走的是「穩定迭代、補依賴與 OAuth 安全面」的路線。
- 連結：https://github.com/mcp-use/mcp-use/pull/2001

## 重點觀察
- 今天五個 repo 全部都有 2026-07-24 的 commit / PR 活動，但只有 `block/buzz` 與 `earendil-works/pi` 同時有新 release — 也就是說 release 發行節奏與 commit 密度並不完全掛勾，新專案常見「先在 main 上狂修、發版延後」的模式。
- 安全修補是今天最一致的訊號：`koala73/worldmonitor` 升 find-my-way 緩解 HTTP/2 DDoS、`mcp-use/mcp-use` 拆開 OAuth provider/resource audience、`block/buzz` 修 WSL alias / Windows console — 都是「被真實使用後回流的細修」。
- `earendil-works/pi` 出現了 Gemini 3.x tool-call ID 被 google-generativeai SDK 吃掉的 issue（#7047），這是個跨 SDK 整合警訊，後續若其他代理框架也撞到同一個問題，值得追蹤。
- `shiyu-coder/Kronos` 主線明顯降溫（main 停在 4 月），但社群今天仍在開「train Kronos Large model」議題，repo 目前的瓶頸可能在於缺乏大型訓練資源或主要 maintainer 投入。
- `koala73/worldmonitor` 是今天五個裡最值得「關注節奏」的 repo：main 活動極熱、release 卻四個月沒動，下一個 release 很可能會是一次跨半年的 changelog 大雜燴，建議下次巡邏順便去看 release 頁是否已更新。