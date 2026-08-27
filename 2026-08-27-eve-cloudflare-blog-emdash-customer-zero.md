# Cloudflare Blog – Brought to you by EmDash (Customer Zero Dogfooding)

- 原始連結: https://blog.cloudflare.com/cloudflare-blog-uses-emdash/
- 作者: Kody Jackson, Diogo Carneiro, Amy Dutton
- 發表: 2026-08-24
- 來源: Cloudflare Blog（Customer Zero 系列、EmDash dogfooding 故事）
- 閱讀時間: 2026-08-27（晚間）

## 摘要

**核心故事：Cloudflare 把自家 blog 從舊 CMS 搬到自家 pre-1.0 的 EmDash（Astro + Workers 上的 CMS），這是一次 Customer Zero dogfooding**——產品團隊、infra 團隊、blog 編輯三方一起用 K6 把 EmDash 打到「真實生產會發生的最壞情況」，找出 usability bug、用 ramp/breakpoint/burst 三套 scenario 驗證架構，最後在 8/12 launch 一天內用 service-binding proxy 從 1% → 5% → 15% → 100% 漸進切量，零 downtime、可秒級退回舊站。Lighthouse desktop 數字在新架構下變得 flat（舊 CMS 在尖峰會有 latency spikes），最高實際承載 850 RPS。

**首次把 Cloudflare 自己的「new Workers Cache」用在 major site 上。** 從他們 production architecture 圖（由上到下、靠近使用者遞減）可以看到：edge cache → Workers Cache（這次首次被用於 major site）→ EmDash object cache（建在 Workers KV 上,這次專為 blog 客製）→ Hyperdrive + PlanetScale 的 read replica。靜態檔 99.5% 命中 cache、總請求 70% 命中 cache。這對主人的意義不是「Workers Cache 出來了」，而是 **Cloudflare 自己寫了判例：可以把整站放上 Workers Cache、把 KV 當物件快取、把 Hyperdrive 當 read-replica aggregator，且量級到 850 RPS**——任何後續想做類似架構的下游都可以直接 reference。

**代理切流這條 service binding 細節是 engineering 的真正價值點。** Launch 前他們先部署一個 proxy Worker,把 legacy blog 跟新 EmDash 兩個 backend 並掛起來,用 version cookie 路由。關鍵不是 cookie 本身,而是用 **service binding (`NEW_BLOG`)** 把 proxy Worker 直接 dispatch 給新 blog Worker,跳過 public hostname / DNS / TLS / outbound HTTP——也就是說走 proxy 的讀者只多一跳 internal Worker-to-Worker call,延遲不會被 DNS / TLS handshake 拖累。並且任何 500 都能秒退回 legacy,fallback path 是設計進系統的,不是 ad-hoc。

**EmDash 在「為 agent 設計」這一塊走得很前面。** 兩個 MCP server:一個是 blog 對外的（給 agent 用 `search_posts` / `list_posts` / `get_post` / `list_tags` 四個 tool 讀文章),另一個是 EmDash 對內的（給 blog 作者用 agent 來 browse / create / edit / schedule post / 刪檔）。而且作者特別強調 **「it's available without any additional cost」**——MCP 不是 add-on,是平台的一部分。Agents Week 真實驗證:9 天發 28 篇、3M pageviews、EmDash 編輯端 schedule bug、但 frontend 撐到 450 RPS、還順手吸收 8/10 一波 28,000 RPS DDoS。

**真正能給主人帶走的：這篇文章的格式本身就是一份「我們如何把一個 pre-1.0 系統推到 production」的公開 SOP**。他們的驗收標準寫得很具體：`http_req_failed: rate<0.01`、P95 <500ms、P99 <1000ms、checks rate>0.99;三種 k6 scenario(ramp 3x baseline、breakpoint 0→100 RPS ramp 找斷點、burst 7000 RPS 1 分鐘)、每種都對應一個失敗定義。這套東西對主人做 air-gap downstream 的「runtime 真的能跑嗎」驗證,是可以直接借的形式。

## 3W1H 分析

- **What（做了什麼/主題）**: Cloudflare 在 2026-08-12 將 blog.cloudflare.com 從舊 CMS 遷移到自家 pre-1.0 的 EmDash（Astro-based、跑在 Workers 上的 CMS）。工程團隊用 K6 跑 ramp / breakpoint / burst 三套 scenario 驗證架構承受力,設計了 edge cache → Workers Cache → EmDash object cache（Workers KV）→ Hyperdrive+PlanetScale read-replica 的四層快取,並透過 service-binding proxy Worker 做漸進切流與秒級 fallback。launch 當天從 1% 流量逐步到 100%,Lighthouse 變 flat、實際承載到 850 RPS、Agents Week 期間吸收 28,000 RPS DDoS。同時把 blog 對外與 EmDash 對內各做一個 MCP server,讓 agent 與人類共享同一個 authoring surface。

- **Why（為什麼重要）**: 對主人這條 Hermes / horo-agent / air-gap downstream 的軸線,這篇有兩個直接移植點。第一,**「判例」價值**：Workers Cache + Workers KV object cache + Hyperdrive 的組合在 Cloudflare 自己 major site 跑過了,任何想用類似 stack 的人(包括主人想讓 horo-agent / webui 在 air-gapped 環境跑得夠順)都有了具名 reference case,不用再從文件拼。第二,**Customer Zero SOP 模板**：他們把「pre-1.0 系統推到 production」的驗收條件、失敗定義、k6 scenario 形式、proxy fallback 設計全部寫在文章裡——這是主人日常強調的「以真實 handoff、exit code、live smoke 判定狀態」的官方範本,可以直接拿來當 `horo doctor` 的 acceptance criteria 草稿。

- **How（如何運作/實作）**: 工程流程有三條平行線。(1) **效能驗證線**：k6 三個 scenario(ramp 3x baseline / breakpoint 0→100 RPS 找斷點 / burst 7000 RPS 1 分鐘)對應三個失敗閾值(5xx >0.01%、P95>500ms >5%、P99>1000ms >1%、checks rate<0.99)。通過後才定 production architecture。(2) **代理切流線**：先部署 proxy Worker,接收所有流量,根據 version cookie 決定要 dispatch 給 legacy 還是 NEW_BLOG service binding;service binding 讓 proxy 跟新 blog 都是 internal Worker-to-Worker,跳過 DNS/TLS。launch 從 1% 開始逐步放,任何 500 就 fallback。(3) **內容/快取線**：edge cache 處理公開靜態檔、Workers Cache 處理應用層物件、EmDash object cache（Workers KV）做 page-level cache、Hyperdrive 把 PlanetScale MySQL read replica 變成 edge 可用的 connection pool;靜態檔 99.5% 命中 cache、總請求 70% 命中 cache。

- **Insight（個人心得）**: 這篇讓赫蘿最在意的不是 Workers Cache GA,而是 **「first major site to do so」這五個字被 Cloudflare 自己說出來**——它代表一個工程文化事實:即使是自己的 blog,他們也不願意冒險把一個尚未在其他 major site 用過的功能直接推上去,於是選擇 Customer Zero、選擇 k6 burst 7000 RPS、選擇 service-binding fallback、選擇 1% 起步。主人偏好的「以真實 handoff 判定狀態、保守減法」跟他們這條路徑其實是同源的——差別只在 Cloudflare 已經走到「用自己產品驗自己產品」的成熟度,主人的 horo-agent / air-gap downstream 還在更早的階段。**具體可移植的兩個動作**：(1) 給主人既有的 `horo doctor` 加一條 `bundle-load` 檢查——模擬 k6 breakpoint scenario,從 cold start 把 horo-agent bundle 起來跑 100 個 pseudo request,看 P95 是不是 <主人自訂閾值,失敗就 red flag;這是 Cloudflare 「breakpoint 找斷點」精神的 mini 版。(2) hermes-webui / memory-editor 既然都跑在主人的 Tailscale 環境,可以學他們在 production architecture 加一層 service binding 內網 dispatch(已經是這樣了——透過 tailscale serve),把它寫進文件當 air-gap downstream 對應的 proxy fallback pattern。不要去模仿 EmDash 整套 CMS;要模仿的是他們把「驗證 SOP」本身當成產品的一部分公開寫出來——這樣下一個 reader 不用自己重新發明驗收條件。
