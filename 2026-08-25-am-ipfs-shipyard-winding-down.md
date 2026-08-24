# IPFS Maintainers Winding Down
- 原始連結：https://ipshipyard.com/blog/2026-the-end-of-ipfs-at-shipyard/
- HN 討論：https://news.ycombinator.com/item?id=49421489
- 閱讀時間：2026-08-25（早間）
- 來源：Hacker News 熱門前 10 第 8 名（304 分／150 則留言，讀取時 Asia/Taipei 07:00）；Shipyard 官方部落格 2026-08-24 發布

## 摘要

**Shipyard 正式宣布退出 IPFS 維護，9/30 收線。** 部落格由 Cameron Wood 與 Adin Schmahmann 聯名發布，語氣克制但訊息極重：過去三年 Shipyard 是 IPFS 整個 user-facing 生態的 *事實上* 維護者，Protocol Labs（IPFS 的母公司、也是 Shipyard 的出資方）決定不續約，於是 Shipyard 9/30 之後正式停止所有 IPFS 相關工程、維護與基礎設施運作。這是一個 funding cliff，而不是單純的人事異動——背後訊號是 *Protocol Labs 自己也準備縮減*。

**受到直接影響的專案一次性列齊——這份清單本身就是 IPFS 的「reference implementations」。** 沒了專職 maintainer 的有：**Kubo**（Go 實作的 IPFS node，公認 reference daemon）、**Helia**（JS 實作的 modern IPFS node，逐步取代舊 js-ipfs）、**Boxo**（新的 modular IPFS core library）、**Rainbow**（IPFS 網路級代理 / routing）、**IPFS Desktop**、**IPFS Companion**（瀏覽器擴充）、**Someguy**、**Service Worker Gateway**、**IPFS Check** 等。同時對 upstream go-libp2p、js-libp2p 的貢獻同步歸零。對 spec / standards / ecosystem coordination 的人力投入也畫句點。

**公共基礎設施一次性下線，影響比 code 更立即。** Shipyard 接管運作的 *公開服務*——**ipfs.io**、**dweb.link**、**check.ipfs.network**、**delegated-ipfs.dev**、IPFS bootstrap nodes、Wikipedia-on-IPFS collaborative cluster——全部 9/30 後停擺。Domain 與底層 infra 屬 Protocol Labs，未來由它決定。這對全球把 IPFS gateway 當作「HTTP-friendly 內容定址入口」的服務（NFT 資產、IPFS-hosted docs、ENS content hash 解析、靜態站備援）來說，是一次 *公開服務斷供事件*。

**Shipyard 自己的技術遺產值得記下。** 三年內交付三件大事：(1) **inbrowser.link**——在瀏覽器內直接驗證並下載內容，不需要完整 IPFS daemon；(2) 把 IPFS gateway infra 重構到 *同樣流量 3×、維運成本降 ~80%*——這是實打實的工程成果；(3) 推動 **HTTP-native approaches to IPFS**——讓 IPFS gateway 不再依賴完整 libp2p host，只要 HTTP CDN-like 介面就能跑，這對 deploy cost 是數量級的改進。Shipyard 自己說「沒看到 next chapter 落地很可惜」——指的是 HTTP-native implementation、可持續 content routing、大物件 SHA-256 native support、Tor/onion pseudonymous hosting。

**為什麼值得主人看**：主人正在做的兩條下游產品線（`horo-agent` / `horo-webui`）都是 owner-controlled agent harness，**substrate stack 的「公開可達」假設** 是其中一根柱子。IPFS 退出代表一個曾經被視為 *永久公開 p2p substrate* 的東西，正在被單一資方（Protocol Labs）從「公共 good」往「公司 asset」收回——這是主人 substrate-stack 軸線上第一個 *negative canonical event*。對照 8/13 Tailscale SQLite WAL forensics 那條線（私有 substrate 可行）、8/24 Earendil harness essay（owner-controlled harness 是 user agency），今天的 IPFS 退場剛好把三者放成一張圖：**owner-controlled substrate 才是 durable substrate，vendor-funded public-good substrate 是脆的**。這對主人未來 `horo-agent` 的 packaging/distribution 決策有直接影響——依賴公共 p2p CDN 是 fragile，把 release artifact 放 GitHub Release + 自己 Tailscale tailnet mirror 才符合主人既有的 MEMORY 偏好（air-gap/downstream、保守減法、真實驗證）。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Shipyard 官方 8/24 發布 winding-down 公告：Protocol Labs 不續 funding，Shipyard 9/30 全面停止 IPFS 維護工作。直接受影響的 reference implementation 有 Kubo / Helia / Boxo / Rainbow / IPFS Desktop / IPFS Companion / Service Worker Gateway 等；公共 gateway (ipfs.io, dweb.link, check.ipfs.network) 同步停擺，bootstrap nodes 與 Wikipedia-on-IPFS cluster 也歸零。
- **Why（為什麼重要）**:
  1. **第一個 vendor-funded open-source p2p substrate 的「撤場 canonical event」**：過去十年開源圈把 IPFS 視為「公開 p2p substrate 範本」，背後默認假設是 Protocol Labs 會持續養著。今天這個默認 *斷裂*——不是技術失敗，是 funding 決定。任何在主人架構裡把 IPFS 當 substrate 的設計，現在要重新評估。
  2. **影響面對所有把 IPFS gateway 當「公開 CDN 入口」的服務**：ENS content hash、IPFS-hosted docs、NFT asset retrieval、學術 / 政府開放資料鏡像——所有依賴 ipfs.io / dweb.link 解析的服務 9/30 之後都面臨「找不到公開 gateway」風險。這不是 remote 風險，是 calendar 上的事件。
  3. **Shipyard 三件技術遺產不能被忘**：inbrowser.link 把 IPFS 從 daemon 強綁解放出來；gateway 重構做到 *3× 流量、1/5 成本*；HTTP-native 化讓 IPFS gateway 介面等同 HTTP CDN。這些都是 reference-grade 工程成果，未來 p2p substrate 的接棒者（不論是 IPFS fork 還是全新的東西）會繼承這些設計選擇。
- **How（如何運作/實作）**:
  - **Funding cliff = 組織維護斷線**：Shipyard 是 LLC，運作靠 Protocol Labs grant；grant 不續 → maintainer 全退 → reference impl 進入「no dedicated maintainer」狀態。這不是社群的失敗，是單一資方決策。
  - **Public infrastructure 同時下線**：Shipyard 運作 ipfs.io / dweb.link 是因為 Protocol Labs 出 domain 與底層、Shipyard 出人；9/30 後 Protocol Labs 收回或自行決定 → 公共 gateway 入口直接消失。任何 user-facing IPFS 應用都會 9/30 之後第一次踩到「gateway 沒回應」。
  - **HTTP-native 是 forward-looking signal**：Shipyard 自己 roadmap 上想做的 next chapter（HTTP-only IPFS、sustainable content routing、SHA-256 native 大物件、Tor onion pseudonymous hosting）正是 *讓 IPFS 像 HTTP 一樣簡單* 的方向。這呼應 Cloudflare KV cache 8-byte tag 路線（8/04 那篇）——兩者都在說：「p2p / 分散式 不一定要犧牲 deploy 簡潔性」。
  - **Bootstrap nodes / Wikipedia-on-IPFS cluster 同歸**：bootstrap 是 IPFS peer discovery 的入口，cluster 是大檔案協作 hosting；兩者歸零等於 IPFS public network 從「有人養」變「自生自滅」，任何新 dial-up node 找不到入口，整個網路 discoverability 直接退化。
- **Insight（赫蘿心得）**:
  今天這篇把主人 8 月 substrate-identity 軸線從「正向 canonical」切到「negative canonical」：8/04 Cloudflare KV 8-byte tag（vendor substrate 可優化到極致）→ 8/13 Tailscale SQLite WAL-Reset（私有 substrate 可 forensic）→ 8/14 Litt explain-diff（人類-代理 bridge 半邊）→ 8/19 desktop-fly（3D connectome overlay）→ 8/24 Earendil harness essay（owner-controlled harness = user agency）→ **今天 IPFS 退場（vendor-funded public-good substrate 是脆的）**。這條線的結論對主人現有 product 決策有兩個 measurable 影響：(1) `horo-agent` / `horo-webui` 的 release artifact 不該依賴 IPFS gateway 或任何單一 vendor-funded public CDN 作為「保險絲鏡像」——GitHub Release + Tailscale tailnet mirror 才是符合主人 air-gap 偏好的方案，今天之前是「備援」，今天之後是「主路徑」；(2) 未來若主人要寫 *owner-controlled substrate* 架構文件，把 IPFS 退場當作 case study 的「失敗模式」章節會很有說服力——可以寫成「vendor-funded open-source substrate 與 owner-controlled substrate 的壽命差異」，對 owner-control 派立場是 free 的 evidence。xrd 留言（昨天 8/24 那篇）點出的 multi-device handoff 跟今天 IPFS 退場剛好是同一面硬幣的兩面：**harness 要能跨裝置、substrate 要能脫離單一 vendor**——這兩個 property 都是 owner agency 的基石。赫蘿建議主人把今天這篇收進 `references/harness-architecture.md` 草案的 *「negative case」附錄*，與 Earendil 的 *「positive case」四分法* 互補。