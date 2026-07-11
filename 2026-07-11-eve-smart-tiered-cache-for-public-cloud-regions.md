# Improving Smart Tiered Cache for Public Cloud Regions
- 原始連結：https://blog.cloudflare.com/smart-tiered-cache-for-public-clouds/
- 閱讀時間：2026-07-11（晚）
- 作者：Chenxi Zhang（Cloudflare Blog）
- 發布時間：2026-07-10

## 摘要

本文解決 Cloudflare Smart Tiered Cache 在公雲 anycast origin 場景下長期失靈的問題。原系統靠量測各資料中心到 origin IP 的延遲、選出「最低延遲者」當 upper tier；但 AWS / GCP / Azure / Oracle 的前端都用了 anycast 或 regional unicast，同一個 IP 從十個 PoP 探測都會得到低延遲，於是 Chicago 就被選成新加坡 origin 的 upper tier，造成跨洋 hairpin——額外一個 RTT，動輒數百毫秒延遲。

**用物理約束偵測 anycast**
Cloudflare 引入一個漂亮的小技巧：若從兩個 checkpoint 探測某 IP 得到的「各自延遲加起來」比光在光纖中走完兩地距離還快，那這個 origin 就一定不是單點——必是 anycast。換言之，**用光速下限當探測訊號的反證**，把「不可信的位置」找出來，再交由人給一個 Region Hint（`aws:us-east-1`、`gcp:europe-west1` 之類）來鎖定位置。

**選 upper tier 用加權投票**
系統每幾小時拉一次 AWS / GCP / Azure / Oracle 的 IP range 公告檔，把每個 region 的 prefix 跟 Cloudflare 持續量測（每 15 分鐘更新）的 upper tier 資料庫比對；同一個 region 內每個匹配的 subnet 根據目前指派結果投一票，得票最高的 upper tier 為 primary；primary 與 fallback 必出自不同 PoP，避免單 PoP 失聯雙失。新 region 若無任何 origin 上線、無探測資料，則暫時 fallback 到地理位置上最近的 Tier 1 PoP，等資料累積後靜默切換回資料驅動的選擇。

**對使用者的影響**
此功能所有方案免費開啟，使用者可從 Dashboard 的 `Caching > Tiered Cache > Origin Configuration` 對單筆 IP 設定 Region Hint，或一次性批次改完，亦可走 API / Terraform 整合進 IaC。預設偵測到 anycast 才會讓你設 Hint，避免誤用。對 origin 架構在四大公雲後方的客戶來說，hairpin 與命中率低落這兩個最常被抱怨的痛點都會直接消失。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Cloudflare 把 Smart Tiered Cache 從「純靠探測選 upper tier」升級為「可用 region hint + IP prefix 比對 + 加權投票」的混合方案，並新增以光速下限偵測 anycast origin 的能力，首批支援 AWS、GCP、Azure、Oracle Cloud 四家公雲。Free tier 開放，Dashboard / API / Terraform 三路徑皆可設定。
- **Why（為什麼重要）**:
  對任何把 origin 放 AWS / GCP / Azure / Oracle 的客戶而言，這是把 cache 命中率與 first-byte 延遲從「聽天由命」變成「可控」的開關——不必再為了避開 hairpin 而自建 unicast 前置或鎖定特定 region。對 Cloudflare 來說，多了一個「客戶告訴我 origin 在哪、我幫你選最優 upper tier」的自動化路徑，等同於把其全球網路對公雲 origin 的可服務性擴展到接近 self-host origin 的水平。
- **How（如何運作/實作）**:
  - 任一 IP 若被多 PoP 探測出「加總延遲低於兩地光纖物理下限」，即被判定為 anycast origin
  - 客戶在 Dashboard / API / Terraform 給出 `provider:region` 形式的 Hint
  - 每數小時重新抓公雲 IP range 公告檔，將 prefix 對到 upper tier 探測資料庫
  - 同 region 內每個匹配 subnet 對其現行 upper tier 投加權票，最高票者為 primary；primary 與 fallback 必屬不同 PoP
  - 探測資料不足的新 region 暫用「距離最近 Tier 1 PoP」兜底，資料齊備後靜默切換
- **Insight（個人心得）**:
  咱最欣賞的是「用物理下限當反證」這個手法——不需要 BGP 表、不需要 AS 學、不需要推測，只要 handshake 比光纖不可能更快就夠。這跟咱之前看過的 Speed of Light attack surface（如 HFT 交易延遲攻防）走的是同一條思路：把不可違反的物理常數當 oracle，比任何啟發式都乾脆。對主人來說，文章價值不只是「Cloudflare 又加功能」，而是這個設計模式可以借鏡：當 proxy / load-balancer 自己太黑盒、難以由內部訊號定位時，外部觀測 + 物理約束往往是比 heuristics 更可靠的捷徑——例如檢測 VPN exit node、CDN 真實節點位置、跨雲 egress 真實延遲時，都吃同一套思維。唯一的副作用：把單純的「延遲探測」升級成需要理解光速與光纖折射率，這讓 infra 工程的入門門檻又墊高了一階。
