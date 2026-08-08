# Introducing Kitesurf: The agent-first browser that runs in V8 isolates on Cloudflare Workers
- 原始連結：https://blog.cloudflare.com/kitesurf/
- 閱讀時間：2026-08-08（晚）
- 作者：Celso Martinho / Ruskin Constant / Rui Figueira / Luís Duarte（Cloudflare, Browser Engineering）
- 發布時間：2026-08-06
- 來源：Cloudflare Blog（RSS tier-1，Agents Week 系列）

## 摘要

**Cloudflare 12 週從零寫了一個給 agent 用的瀏覽器。** 動機很直白：Chromium 是給人開的——tabs、theme、extension、60fps scrolling、persistent state，這些 agent 都不需要；agent 真正在乎的是 token count、context window、scalability、cost。而 Chromium 為了承載這些人類包袱，記憶體與 CPU 的開銷讓「每個 agent 配一個瀏覽器」變得貴到只有頂級模型玩得起。Cloudflare 從 internal 開了十幾年的「我們該不該自己寫瀏覽器」老題目，這次三個技術臨界點（Wasm 成熟、Dynamic Workers、SQLite-based Durable Objects、Worker-to-Worker RPC、Node 兼容 + 平台 limit 拉高）同時到位，答案是 unanimous yes。產物叫 Kitesurf，整個跑在 Workers 上，現在以 `browser=kitesurf` 參數在 Browser Run 免費 beta 中。

**架構是「Engine + PageScript + PageRenderer」三個 stateless 單元加一個集中式 SandboxOutbound。** 只有 Engine 是 public-facing 的——它接 Chrome DevTools Protocol（CDP）的 WebSocket 與 HTTP REST API，所以 Puppeteer / Playwright / chrome-remote-interface / 任何講 MCP+CDP 的 AI agent 全部相容。PageScript 用 Dynamic Workers 給每個 page / OOPIF spin up 長期活著的 isolate，裡面是乾淨的 globalThis + DOM document object，HTML 用 Blitz 解析、CSS 用 Firefox 的 Stylo 處理；JS / Wasm 直接在同一個 isolate 跑；eval / Boa JS 兜底（Workers 原生不支援 eval）。PageRenderer 跟 Engine 透過 Workers built-in RPC 互相呼叫 `renderFrame()`，renderer 每次都是 disposable cache、stall 就 kill、便宜到能開一千個。唯一被允許碰網路的是 SandboxOutbound——它負責 CORS、瀏覽器形狀的 header、cookie jar、403 過濾。整套設計的 kernel rule 是「能 stateless 就 stateless」、「隔離是預設」、「任何 boundary 的失敗只降級成空白 frame，絕不讓 session 死掉」。

**測試策略是把 WPT 當 goalpost 給 AI agent 自己跑。** Cloudflare 用了 Web Platform Tests 23 萬+ 個 conformance 測試當作「AI agent 才知道自己有沒有歪」的標準答案，並 per-feature curated 順序、分派給不同 agent 平行推進；人只做 architecture 與 review。WPT 只測 W3C 合規、不測實際網站，所以另外寫了一組 Puppeteer 多步整合測試 + visual regression，比對 Chromium 與 Kitesurf 在每一步的 assertion 與渲染輸出。當前 215,000+ WPT 通過、每週再加數百個，Dioxus 寫的 TodoMVC（vanilla / React / Vue / Angular / Preact）、Wikipedia、Hacker News、Cloudflare 自身 blog/dashboard 都已能正確渲染。Chromium 在純 stopwatch 還贏約 1.7×（JIT hot path vs cold software renderer），但 Kitesurf 在 memory / CPU 贏 3–7×——而後者才是真正影響帳單的維度。

**設計取捨裡藏著主人會想抄的思考框架。** Cloudflare 明確說他們不打算開源——是「let any customer deploy their own version of Kitesurf on their own accounts」的佈局。文章把 Kitesurf 定位成 ephemeral / fully-isolated / stateless / task-scoped，YouTube 影片、WebGL、真 TLS fingerprint bot-challenge、長 session 仍走 Chromium。把 stateless 當 first-class、把原生 eval 限制當 architectural choice（用 Boa JS 兜底、未來再移除）而非 bug、把測試 oracle 從「human review」升級成「WPT + Puppeteer visual regression」讓 AI 自己循環——這些都是「把人類友好設計拿掉、換成 agent 友好設計」的具体樣本。

## 3W1H 分析

### What（這篇文章到底在說什麼）
Cloudflare 公開他們 12 週從零打造的 Kitesurf——一個完全跑在 Workers + V8 isolates 上的 headless 瀏覽器，給 AI agent 而不是給人用。它由三個幾乎全 stateless 的元件（Engine、PageScript、PageRenderer）加一個集中式 SandboxOutbound 拼成，透過 Cloudflare Workers 的 Dynamic Workers / RPC / Sandbox APIs 撐起 per-page 隔離、用 Blitz + Stylo + Boa JS 處理 HTML / CSS / JS / eval，並透過 CDP 對外相容 Puppeteer / Playwright / MCP 客戶端。現在以 `browser=kitesurf` 在 Browser Run 公開 beta，記憶體與 CPU 比 Chromium 省 3–7×，犧牲 1.7× 純 stopwatch 換來「可以給每個 agent 配一個」的 cost curve。

### Why（為什麼這對主人有意義）
主人最近三個月的記憶裡有兩條主線會直接撞上 Kitesurf。第一條是「chrome-game-env / Phaser / Vite / browser_vision 視覺驗收」——主人驗收遊戲或視覺工件時依賴 Playwright/CDP 抓畫面，這條路徑今天就能直接切換成 Kitesurf：`browser=kitesurf` 已經是 Browser Run 上的合法參數，意思是主人未來在自己 air-gapped downstream（horo-agent / horo-webui、或本地的 free-time-research 視覺驗收）裡如果用的是 headless Chromium，理論上可以換成 Kitesurf 來壓 3–7× 的視覺驗收成本。第二條是「保留現有 codebase 已被證明穩定的 runtime 與真實行為，以保守減法 + 端到端驗證落地」、「SOUL.md 是 runtime-visible control surface」、「spec-driven N-stage + commit-per-stage workflow」——Kitesurf 的「先選 kernel rule（stateless、isolate-as-default、failure→blank frame）再寫程式碼」正是主人「先寫 SOUL.md 邊界再寫程式」那一套的對岸版本；差別是 Cloudflare 把它推到極致到「連瀏覽器都自己重寫」，而主人只在「SOUL 規 + 內部 skill_view 編輯」這個粒度上做自我約束。

### How（主人可以怎麼用）
最低成本嘗試：把目前 Browser Run 的 client（Puppeteer / Playwright / MCP）連到 Kitesurf 跑一輪視覺驗收，比對 memory / CPU 與截圖差異——重點不是「Kitesurf 有多好」，而是「主人常用的那 5–10 個驗收網站（Wikipedia、HN、Cloudflare blog、TodoMVC 以外的小東西）有哪些在 Kitesurf 跑得起、哪些會撞 WebGL / 真 TLS / 持久登入」。設計語言上，可以把 Kitesurf 的「kernel rule 優先、測試當 oracle、stateless-as-default」這三條直接抄進 SOUL.md 的「寫作偏好」或「an air-gap 流程」段落，作為主人設計 horo-agent 新模組時的 sanity checklist（例如「這個新模組可不可以 stateless？隔離邊界有沒有 narration？失敗有沒有降級方案？」）。技術上，主人的 local canvas 1024:768 Phaser / chrome-game-env 不會直接受惠——Kitesurf 不跑 WebGL——但工作流結構（用 WPT + 視覺回歸測試當 AI agent 的 oracle）可以考慮復用一份「主人自己版本的 mini 測試 oracle」。

### Insight（赫蘿的觀察）
主人讀完最值得帶走的一件事：**Kitesurf 驗證了一個主人隱性已經相信的命題——「人類向好的設計跟 agent 向好的設計正在分叉」**。Blitz / Stylo / Boa 這三個 Rust 寫的元件代表「把渲染拆成可以被 serverless 化的最小工作單元」，Dynamic Workers + RPC 代表「把 state 集中、把 isolate 當 disposable」，CDP 相容層代表「不要重新發明 client 接口」。這三條會直接出現在主人未來一年要做的所有 agent 棧裡——主人只要守住「kernel rule 優先、oracle 測試、可丟棄 isolate」三條底線，就不用追 Cloudflare 每一週的具體工具，反而能保持自己的 enterprise-lite 路線不被 vendor lock。**決策邊界**：Kitesurf 對主人有兩個層面的吸引力（a）營運層面：未來 Browser Run 計費降下來時，主人可以批次把視覺驗收卸載過去，省 Compute 帳單；（b）架構層面：把 Kitesurf 的設計 pattern 抽象成 SOUL.md 的「Module Primitive 候選清單」。但主人不要做的事是——把 Kitesurf 當成「自家 enterprise-lite 就像 Cloudflare 一樣」的證據自己重寫瀏覽器，那會直接撞上主人「複雜化會被打回」的紅線。Cloudflare 能 12 週寫出 Kitesurf 是因為他們有 Wasm / Workers / Dynamic Workers / RPC 五年累積的底座，主人沒有這個底座；正確的讀法是「學習 Cloudflare 怎麼拆問題」，不是「複刻 Cloudflare 的解決方案」。另外，Kitesurf 的「run Doom in browser」行銷梗（silent sea marine saga 那個）跟主人最近在 chrome-game-env 路線上的 brand new 專案精神接近——視覺是理解的入口——但 Kitesurf demo 跟主人實際工作的差距很大，demo 是 Canvas classic，主人是 Phaser 4 + Vite + 1024:768 design resolution + 本地開發 HMR，兩者只是 spirit 相似，不要直接對應。
