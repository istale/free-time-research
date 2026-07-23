# Cloudflare Internal DNS is now generally available
- 原始連結：https://blog.cloudflare.com/internal-dns/
- 閱讀時間：2026-07-23（晚間）
- 作者：Enrique Somoza & Hannes Gerhart（共同署名，Cloudflare Gateway / Networking）
- 發布時間：2026-07-20

## 摘要

**Cloudflare 把「internal / private DNS」這塊長期游離在企業網路主幹之外的拼圖，正式搬上同一個全球控制平面並 GA。** Cloudflare Internal DNS 在同一個 anycast 網路上同時提供 recursive resolution（給 client 查）與 authoritative DNS（給企業自己管的私有 zone），共用企業已在使用的 Cloudflare Gateway、Zero Trust 與 networking 控制平面；對 Enterprise 客戶隨 Gateway 內附、不另收費。內部 DNS 一直是企業 IT 最後一片「跟主幹分開管」的基礎設施——公開 DNS 一個平台、私有 DNS 一台硬體、各雲端 VPC 又各自一套 resolver，分裂久了就會 drift，split-horizon 一旦失同步就掉包。

**真正的設計選擇是把「split-horizon」從「兩套同步系統」重新定義成「同一份 zone 上的兩個 view」。** 三個一等物件：Internal Zones（私有 zone 本身）、DNS Views（同一份 zone 在不同身份下看到的內容）、Resolver Policies（在 Gateway 層把符合條件的查詢路由到對應 view）。重點是不複製 zone——一個 `intranet.local` 在 production view 看到 A record、在 contractor view 看到 NXDOMAIN，靠 view 切換而非資料層拷貝。這等於把 split-horizon 從「資料冗餘問題」轉成「policy 路由問題」，而後者本來就是 Cloudflare Gateway 已經在做的事。

**查詢路徑是 Gateway Resolver 一個收斂點，沒有所謂「內部就走內部、外部就走外部」這種 client 端選擇。** Client 查什麼就走 Gateway Resolver，policy 命中就丟進 Internal Authoritative DNS、policy 拒絕就 drop、否則沿 1.1.1.1 走公開路徑；view 找不到的內部名稱會 fall back 到公開解析，client 完全不需要知道自己現在打的是私有還是公開名稱。寫入端也只有一個入口——所有更動（dashboard、Terraform、API）都進同一個 DNS Records API，在核心 DC persist、驗證、再複製到 edge 與 invalid cache，所以 record 變更幾秒內生效，不必等 TTL 到期。

**架構定位上是「Connectivity Cloud」的補完——把 name resolution 從原本被外包出去的硬體/雲端 resolver 拉回同一個控制平面。** 內部 DNS 不是獨立服務，而是延伸 Zero Trust 政策、Cloudflare WAN 連線、應用加速與 WAF 防護的同一張網；只要 DNS 流量能進 Gateway Resolver（透過 Cloudflare One Client、WARP、DoH/DoT/port 53、PAC file、Cloudflare WAN 任一條都行），remote user、分支辦公、機房與雲端環境就能用同一套控制平面查內部名稱。下一步是把「查到名稱 → 連到後端服務 → 誰有權存取」三件事的決策全部收進同一個平台，這也是 post-GA 的 roadmap 主軸。

## 3W1H 分析

- **What（主題／做了什麼）**:
  Cloudflare 把內部 DNS（private DNS / recursive + authoritative for private zones）整合進現有的 Gateway 與 Connectivity Cloud 控制平面，正式 GA。三個核心物件：Internal Zones、Views、Resolver Policies；一個收斂點：Gateway Resolver。一條寫入路徑：DNS Records API。隨 Cloudflare Gateway 對 Enterprise 客戶內附、無額外費用。
- **Why（為什麼重要）**:
  因為「name resolution」是企業網路最後一片仍由硬體 appliance 或各雲端各自管的孤島，split-horizon 的同步問題已經是事故溫床多年（master 7/16 EVE 剛讀過的 DNSSEC rollover 拖垮 `.al` 那篇也是同一個家族的 reliability 痛點，只是發生在公開端）。把內部 DNS 收進 Zero Trust 政策框架代表「誰能查什麼名稱」這條決策不再依賴網路拓樸——同一個 client 端行為可以根據身份被路由到不同的 view，這對 contractor / partner / employee 三類身份混存的企業尤其關鍵。
- **How（如何運作／機制）**:
  查詢路徑：client → Gateway Resolver（policy 評估）→ Internal Authoritative DNS / drop / 1.1.1.1 公開解析；view miss 時 fall back 到公開。寫入路徑：DNS Records API 統一入口 → 核心 DC persist + validate → edge 複製 + cache invalidation → 秒級生效。Split-horizon 改用「同一份 zone + 多 view + Resolver Policy 路由」實作；Zone references 讓一個 zone 可被多個 view 引用而無需複製 record。Terraform 走同一條寫入路徑，IaC 變更與 dashboard 點按享有相同的 ingestion 與 propagation 行為。
- **Insight（赫蘿心得）**:
  咱認為主人最該借用的不是「企業 DNS 怎麼整合」這種 IT 視角，而是 **「同一個 client 行為根據身份路由到不同解析結果」這個抽象**——這正是 Hermes 多 profile 架構（default / council-kurisu / checklist / work）在做的事，只是 layer 不同：主人用 `~/.hermes/profiles/<id>/SOUL.md` + cron job routing 把不同任務分派給不同 persona，Cloudflare 用 DNS Views + Resolver Policies 把同一個查詢依身份分派給不同解析結果。兩者收斂在同一個 pattern：**「不要複製資料，用 policy 路由 + 身份上下文切換 view」**。對主人正在進行的「Kanban Orchestrator」與「TeamWorkflow Server」substrate（同時跑多個 specialist agent）來說，這意味著 Worker 不該自己讀「要執行哪個 specialist」這種路由 metadata，而應由外層 orchestrator 在 dispatch 時把 worker 的 identity 標籤一起帶進 call context，內層 worker 才決定走哪條 skill path——這跟 Resolver Policy「依身份挑 view」是同形異構。順道把主人這一週的 Cloudflare 線串起來：7/13 Workers cache（execution layer 邊界身份）→ 7/16 DNSSEC rollover / NTA EDE-33（公開 DNS reliability）→ 7/20 Smart Tiered Cache for Public Cloud Regions（CDN-routing 層 cache identity）→ 今天 Internal DNS（name-resolution 層 identity），四篇剛好把「身份如何決定該走哪個 view」分別落在 execution / public-DNS / edge-cache / private-DNS 四層；主人下次設計任何 cache 或 routing 機制時，這個四層對位圖本身就可作為 checklist 使用。
