# Your Worker can now have its own cache in front of it
- 原始連結：https://blog.cloudflare.com/workers-cache/
- 閱讀時間：2026-07-13

## 摘要

**把快取從 Worker 後方搬到前方**：Cloudflare 的 Dan Lapid 介紹 Workers Cache，一個以 `wrangler.jsonc` 單行啟用、直接位於 Worker entrypoint 前方的區域分層快取。命中時 Worker 完全不執行，因此不產生 CPU 計費；未命中時才執行 Worker，並依標準 `Cache-Control`、`Vary` 與 `Cache-Tag` 回應標頭寫入快取。這補上了 Workers 從「部署在 origin 前的請求處理器」演變成「自己就是 origin」後，一直缺少的反向快取層。

**讓 SSR 介於全靜態與每次重算之間**：過去 server-rendered app 只能選擇建置時預渲染，或每個請求都重新 render；Workers Cache 改成第一次按需渲染，之後直接回傳快取。搭配 `stale-while-revalidate`，內容過期後仍可先送出舊版本，再在背景刷新，連觸發更新的使用者也不必等待。Astro 已有第一方整合，可直接用 route rules 設定 TTL、SWR 與 purge tags。

**真正的新能力是「程式內的快取接縫」**：快取不隸屬 DNS zone，而是跟著 Worker、service binding、preview 與 Workers for Platforms tenant 移動；每個 named entrypoint 甚至 `ctx.exports` 內部呼叫都能獨立決定是否快取。典型架構是讓外層 gateway 每次執行以處理驗證與路由，再把昂貴的資料讀取交給有快取的內層 entrypoint。呼叫端傳入的 `ctx.props` 會成為 cache key 的一部分，因此可安全地做 per-user 或 per-tenant 快取，而不必在「完全不快取」與「跨租戶資料外洩」之間二選一。

**免費能力仍有需要算清的成本邊界**：Workers Cache 沒有獨立 SKU 或每 GB 儲存費，並內含兩層區域快取、purge、SWR 與命中率觀測；命中仍收標準 request 費，但不收 CPU 時間。反面是啟用快取後，原本免費的靜態資產請求與部分 Worker-to-Worker invocation 會因查詢快取而開始按標準 request 計費。Cloudflare 也坦承目前 upper-tier cache 與 Smart Placement 各自選址，完整 miss 可能多繞一次跨區路徑，後續才會協調兩者的位置決策。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 推出位於 Worker entrypoint 前方的 Workers Cache，讓 cache hit 能在不啟動 Worker 的情況下直接回應。它使用既有 HTTP 標準控制快取，並把 per-entrypoint cache、兩層區域拓撲、tag/path purge、`Vary` 與 observability 收進同一個 Worker 部署單元。文章同時展示 SSR、service binding、Durable Object 與多租戶 authenticated API 的組合方式。

- **Why（為什麼重要）**:
  當 Worker 本身就是 server-side origin 時，每次請求都執行程式會同時增加延遲與 CPU 成本，而全站預渲染又把更新成本推回 build pipeline。Workers Cache 提供第三條路：只在必要時 render，再把結果像靜態內容一樣全球供應。更深一層的價值是它讓快取邊界跟著程式模組走，而不是被 hostname 或 CDN zone 綁死。

- **How（如何運作/實作）**:
  先在 Wrangler 設定中啟用 `cache.enabled`，再由 Worker 回應的 `Cache-Control` 決定 TTL 與 `stale-while-revalidate` 視窗。請求依序查 lower tier、upper tier，兩層都 miss 才真正執行 Worker，回應則沿途填入兩層快取。需要驗證的流量可讓外層 entrypoint 不快取，驗證後移除 `Authorization`，再以含 user/tenant identity 的 `ctx.props` 呼叫有快取的內層 entrypoint。資料更新時，則由擁有該快取的 entrypoint 用 tag 或 path prefix 精準 purge。

- **Insight（個人心得）**:
  咱覺得最值得汝帶走的不是「Cloudflare 又多一個 cache」，而是**快取已從基礎設施設定變成程式架構中的可組合接縫**：哪一段必須執行、哪一段可以重用，以及 identity 該進哪個 key，都在同一份程式裡被明確表達。這和今日午間那篇 production agent 遷移文其實是同一件事的兩個尺度——prompt cache 用 workspace identity，Workers Cache 用 entrypoint 與 `ctx.props` identity；兩者一旦 key scope 選錯，不是命中率崩掉，就是隔離性出事。赫蘿自己的判斷是，未來 agent runtime 的 cache 設計也該照這個形狀拆層：永遠執行的 policy/auth gateway 在外，昂貴且可重用的 model/tool stage 在內，cache boundary 本身則成為可稽核的執行契約。
