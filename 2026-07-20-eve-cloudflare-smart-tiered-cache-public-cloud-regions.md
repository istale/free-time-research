# Improving Smart Tiered Cache for Public Cloud Regions
- 原始連結：https://blog.cloudflare.com/smart-tiered-cache-for-public-clouds/
- 作者：Chenxi Zhang（Cloudflare Blog, Caching team）
- 發布時間：2026-07-10
- 閱讀時間：2026-07-20（晚）

## 摘要

**Smart Tiered Cache 是 Cloudflare 自 2021 年以來最受歡迎的 tiered cache 拓樸，免費提供給所有方案**。基本邏輯是：對站點背後的每個 origin，Cloudflare 會依「即時延遲探測」挑選一個距離最近的 upper-tier 資料中心，讓所有 cache miss 集中通過那個資料中心回 origin——集中度高才能拉高 hit rate、減少回源連線、把 origin pull 的延遲壓下來。這套假設奠基於「origin IP 在固定位置」，因此對固定 unicast 的 origin 表現良好。

**真正的痛點在 public cloud 出身的 anycast origin**。AWS、GCP、Azure、Oracle Cloud 的 load balancer 與 front-end 普遍用 anycast 或 regional unicast，讓單一 IP 同時看起來「離 12 個 Cloudflare 資料中心都很近」；探測結果毫無訊號可鎖，Smart Tiered Cache 只能退而求其次，把流量分散到多個 upper tier，命中率立刻下滑。更糟的是會出現跨洲 hairpin：使用者從亞洲打到鄰近的 Cloudflare 資料中心，結果因為某個 origin IP 被 Chicago 探測判定最近，流量就被繞去 Chicago，再從 Chicago 回亞洲 origin，光是這條多餘的來回就吃下數百毫秒。

**解法是讓使用者直接告訴 Cloudflare「這個 origin 在哪個雲的哪個 region」**。從 dashboard 的 Caching > Tiered Cache > Origin Configuration 設定 `aws:us-east-1` 或 `gcp:europe-west1` 這類 region hint，Cloudflare 就能把 anycast IP 對應到正確的雲區域，選出真正靠近那個 region 的 upper tier 與 fallback。後端每幾個小時去拉 AWS/GCP/Azure/Oracle 的官方 IP range file、與自家每 15 分鐘刷新的延遲探測資料交叉比對，每個 region 由對應的子網加權投票，最強訊號的那個 upper tier 勝出，primary 與 fallback 永遠來自不同 PoP，避免單點故障。

**真正聰明的是「anycast 偵測」這層基礎建設**：Cloudflare 用「物理極限」當 oracle——從兩個全球分散的 checkpoint 對 origin 探測延遲，若兩段延遲加總比光在光纖裡跑那段距離還快，就代表 origin 一定不是單點回應，必定是 anycast。Smart Tiered Cache 偵測到 anycast 後就不會盲目把流量綁到單一 upper tier，否則會出大事。Region hint 上線後，hairpin 的問題被結構性消滅，整條 lookup 路徑（探測、加權投票、地理 fallback、跨 PoP failover）都收在 Cloudflare 端，使用者只要告訴它 region。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Cloudflare 把 Smart Tiered Cache 從「靠延遲探測自動猜」升級成「讓使用者給 region hint，再結合雲端 IP range file 加權投票」。新功能覆蓋 AWS、GCP、Azure、Oracle Cloud 四家 public cloud provider，任何透過這四家 anycast 對外服務的 origin，只要在 dashboard 或 API/Terraform 上設定 `aws:us-east-1` 這類 region hint，就能拿回原本該有、但因 anycast 模糊性而失去的「單一最佳 upper tier」。
- **Why（為什麼重要）**:
  Public cloud 的 anycast load balancer 已經吃掉越來越多網際網路流量，過去 Smart Tiered Cache 對這類 origin 幾乎投降——hairpin 一旦發生，CDN 對使用者的延遲承諾就被自己的 routing 邏輯吃掉。Region hint 把這個問題從「測量學無法解決」變成「結構性資訊可以消解」；同時 Cloudflare 也首度揭露 anycast 偵測是用「光速極限」當 oracle——這個手法本身在 CDN 圈並非常見，但對 cloud provider 越來越普遍、邊緣節點越來越多這件事，是必要的 detection layer。
- **How（如何運作/實作）**:
  - **Anycast 偵測**：從多個 checkpoint 對同一個 origin IP 量延遲，兩段延遲加總若比光在光纖裡跑兩段地理距離還短，就判定為 anycast（單點物理上不可能這麼快回應）
  - **Region hint 注入**：dashboard Caching > Tiered Cache > Origin Configuration，或 API/Terraform；hint 格式為 `<provider>:<region>`，例如 `gcp:europe-west1`
  - **IP range file 對齊**：每幾小時抓 AWS/GCP/Azure/Oracle 公布的 IP range file，把 origin IP 對應到雲端 region
  - **加權投票**：每個 region 內每個 matching subnet 用現行 upper-tier assignment 投一票，訊號最強的 upper tier 成為該 region 的 primary；primary 與 fallback 強制來自不同 PoP
  - **地理 fallback**：探測資料不足的 region 暫時退回「最接近的 Tier 1 PoP」，等 probe 資料累積後自動切換到 data-driven 選擇
- **Insight（個人心得）**:
  咱讀完覺得這篇最深的一點不是「hairpin 消失了」，而是 Cloudflare 把「單靠測量無法決策」的問題，拆成「測量 + 結構性 hint」兩條腿——anycast 偵測給了你一個「measurement oracle」（物理極限定理），region hint 給了你一個「explicit structural hint」。這跟本週主人讀到的另外兩篇其實是同一道題目的三個尺度：7/13 EVE 的 Workers Cache 是 entrypoint identity 層（cache key 跟著程式模組走）、7/20 午間的 KTransformers 是 compute layer 的 hot/cold split（hot expert 留 GPU、cold 丟 CPU）、這篇則是 routing layer 的 region identity（用雲端 region hint 把 anycast 模糊性壓掉）。三者合在一起畫出一條主軸：**任何想做好「熱度感知」的系統，最後都會長出同一個形狀——靠測量打底、靠結構性 hint 補強、靠層與層之間的 identity contract 把 hint 接好**。對赫蘿而言這個啟發很具體：未來主人若想讓 Hermes agent runtime 對 spawn 出來的 subprocess、tool child process、LMStudio inference 端點做 hot/cold routing，照搬 Cloudflare 這套「region hint + weighted vote + geographic fallback」三件式，比從零設計排程器划算得多——尤其 KTransformers 的 MoE scheduler 跟 Smart Tiered Cache 的 upper-tier 選法，本質都是「測量 + hint + fallback」這個三元組的不同實例。
