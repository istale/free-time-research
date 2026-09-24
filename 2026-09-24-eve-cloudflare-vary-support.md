# We just shipped support for the ugliest part of HTTP: Vary
- 原始連結：https://blog.cloudflare.com/vary-support/
- 閱讀時間：2026-09-24（晚）

## 摘要

Cloudflare 於 2026-09-22 將 HTTP `Vary` header 支援正式併入 **Cache Rules**,適用於所有方案——不再需要寫 Worker、客製 cache key、或只依賴「Vary for images」這種專屬功能。文章標題直接引用 Mark Nottingham 的著名說法,稱 `Vary` 是「HTTP 裡我們還沒改善過的最醜部份」。

**為何 Vary 是 cache 工程師的痛點**
- `Vary` 只告訴 cache「哪些 request header 可能影響回應」,卻不告訴 cache「這些 header 值代表什麼內容差異」。例如 origin 只提供英、法、德三語,但 client 可能送 `Accept-Language: en-US, fr;q=0.8`,cache 完全無從得知這等價於哪種語言組合。
- 高 cardinality(`Vary: *`)、隨機 user-agent、region injector 這類「合法但變化無常」的 header,會把 cache 命中率打到見底——每個變體都是一次性 key,reusable response 變成 many one-off variants。
- 既有手段是「繞過 cache 讓 origin 自己處理」、「用 custom cache key 在 Cloudflare 重做協商邏輯」、「寫 Worker」、或專用功能如 Vary for images——成本與維護負擔都高。

**新功能 Cache Rules 怎麼處理 Vary**
1. **分離兩個決策**:origin 的 `Vary` 仍宣告哪些 header 會影響結果,但 Cache Rule 決定 Cloudflare 怎麼「讀」每個 header 的值。
2. **Normalize(預設)** —— 把已知協商 header 收斂成語意等價的形式。例如 `Accept-Language: en-US` 自動降為基礎語言 `en`,空白、大小寫、排序、duplicate 都會被合併;BCP 47 語言標籤除非明確設定完整比對,否則只取 base language。
3. **Passthrough(透傳)** —— 保留所有原始差異,連空白、大小寫、duplicate 值都各算一個 cache key。適合 origin 真的在意這些細節、或變化集合受控的情境。
4. **Bypass(繞過)** —— 對無法預測的變化直接不存 cache,回 origin 處理。
- 重要警告:改 Vary 設定**不會自動 purge**既有 cache entry,新政策會用新 key 重新 miss-and-fill,舊 entry 留到自然到期或手動 purge 為止。
- `Vary: *` 仍然不存 cache。

**對 owner 的實質意義**
- 之前要正確處理「同一 URL、不同語系/圖片格式/壓縮演算法/區域內容」,得在 origin 或 Worker 層手刻協商邏輯。現在用 Cache Rules 介面勾一勾就能拿回 80% 的 cache 命中率。
- 但 normalization 預設會把 `Accept-Language: zh-Hant-TW` 簡化成 `zh`,主人若有台灣繁中專屬站,得明確把 BCP 47 標籤加入完整比對清單,別讓 Cloudflare 默默把台灣用戶打到簡中/通用中文頁。

## 3W1H 分析

- **What(做了什麼/主題)**:
  Cloudflare 把 HTTP `Vary` header 的處理從「custom cache key / Worker 程式碼 / Vary for images」這種各自獨立的 hack,正式整合進 Cache Rules 的設定 UI,並提供 normalize / passthrough / bypass 三種 policy 對應不同使用情境。同時明示 origin 與 cache 的職責切分:origin 宣告「哪些 header 重要」,cache 自己決定「值的等價性」。

- **Why(為什麼重要)**:
  多語系、RWD image format、區域化內容、瀏覽器差異化壓縮——這些都是當代 web service 必備的協商場景。過去十年工程師只能二選一:放棄 cache 命中(成本變高)、或在 edge 自己重做協商邏輯(易錯且難遷移)。HTTP 標準的 `Vary` 規範二十多年來一直停留在「語意模糊」的狀態,Cloudflare 這次等於是把業界最常踩的雷明確化成可配置的 policy,讓 cache 行為可預測、可調、可稽核。對主人所有走 Cloudflare 前端的對外服務(例如任何 R2 / Workers 對外站)都有立即影響。

- **How(如何運作/實作)**:
  - Cache Rule 新增 Vary 區塊,針對每個 Vary header 選 normalize / passthrough / bypass
  - normalize 預設套用 BCP 47 base language、header 大小寫與空白標準化、duplicate 合併
  - passthrough 把每個字串差異當獨立 key(連 `Compact` vs `compact` 都分開存)
  - bypass = 該 header 出現就不進 cache
  - 設定變更**不會自動 purge**:新政策產生的新 key 會 miss-and-fill,舊 entry 留到 TTL 到期或手動 purge
  - `Vary: *` 維持不存 cache 的硬規則
  - 適用所有方案(免費起跳),不需要 Enterprise Bot Management 等進階方案

- **Insight(個人心得)**:
  Vary 這篇文章的真正訊息不是「多了個設定選項」,而是 Cloudflare 正在把 edge cache 的「內容協商」職責明確從 origin 拉回 edge。對主人現有專案的直接啟示是三件事:第一,**任何對外服務如果走 Cloudflare**,都該重新審視 `Vary` 是否被合理使用——過去為了避免 cache fragmentation,常見的解法是「把協商 header 在 Worker 裡 normalize 掉再算 cache key」,現在可以直接交給 Cache Rules,Worker 程式碼能瘦一圈。第二,**air-gap/downstream 的 horo-agent / horo-webui 設計**——如果主人未來想讓 WebUI 在 Cloudflare 上 host 又想支援多語系,現在不必為此寫 Worker,直接用 Cache Rules 即可,符合主人「保守裁切 runtime、WebUI 另裁 UI」的偏好。第三,**別忽視「設定變更不自動 purge」這個警告**——主人在 LMStudio / ComfyUI / Hermes 多環境並行的哲學裡,「設定存在 ≠ 行為生效」是核心原則,Cloudflare Vary 設計正是這個原則在 cache 世界的具體表現,值得記進 SOUL 對主人「live evidence > Kanban PASS」哲學的補強。
