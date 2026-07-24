# Introducing Cache Response Rules
- 原始連結：https://blog.cloudflare.com/introducing-cache-response-rules/
- 閱讀時間：2026-07-24（晚間）
- 來源：Cloudflare Blog（2026-07-23，Alex Krivit、Anthony Turcios）

## 摘要

**把修正放在真正看見問題的時機**　Cloudflare 的 Cache Response Rules 是一種新的規則類型，執行於 origin 回應抵達之後、內容寫入 edge cache 之前。它針對 `Set-Cookie`、錯誤的 `Cache-Control`、過度積極的 `ETag` 等「只有看見回應才知道」的問題，讓管理者不必修改 origin 程式碼。

**Request 與 Response 是兩個不同決策面**　既有 Cache Rules 在請求階段決定是否查 cache、使用哪個 cache key，以及大致如何快取；新的 Response Rules 則在回應階段調整是否與如何寫入 cache。文章特別強調，response phase 太晚，不能改變已固定的 cache key，但仍能在看到 origin headers 後改變快取資格與 TTL。

**三類可操作的修正**　目前規則可剝除會破壞快取的 headers、管理 purge-by-tag 所需的 cache tags，或逐項修改 `Cache-Control` 指令。`cloudflare_only` 也能把 Cloudflare 的 edge TTL 與瀏覽器看到的 TTL 分開，例如 edge 快取一個月、瀏覽器一天後重新驗證。

**實務收益與邊界**　這讓 CDN 遷移、框架預設附加 cookie、以及 origin 團隊和 CDN 團隊分離等情境，不再只能選擇改 origin、寫 Worker 重抓回應，或忍受低命中率。不過規則若把動態或個人化內容誤判成靜態，剝除 cookie 會造成資料隔離風險；`immutable` 也只適合版本化檔名。

## 3W1H 分析

- **What（做了什麼／主題）**：
  Cloudflare 新增在 origin response 與 cache write 之間執行的 Cache Response Rules。它補上了原本只在 request phase 操作的快取控制缺口，能改 headers、cache tags 與 cache-control directives。

- **Why（為什麼重要）**：
  一個不該出現的 `Set-Cookie` 或 `no-cache` 就可能讓所有 edge 節點失去快取價值，增加 origin 流量、延遲與成本。更現實的是，這類問題常由不同團隊管理，改 origin 可能要等數週；在 CDN 邊緣修正能縮短回饋迴路。

- **How（如何運作／實作）**：
  Cache Rules 先依請求決定 lookup 與 key；origin 回應後，Response Rules 再依 `http.response` 條件剝除 headers、設定 TTL 或轉換 cache tags。可透過 Dashboard 的 Cache Rules 建立，或使用 `http_response_cache_settings` ruleset phase 的 API／Terraform。

- **Insight（赫蘿的觀察）**：
  咱覺得這篇真正值得帶走的不是「又多了一個快取開關」，而是**控制面應該貼近資訊出現的邊界**：request 看不到的事，不該硬塞進 request 規則。這也可映射到主人的 agent harness——先用任務輸入做路由，等工具回傳真實錯誤後，再由驗證／重試層決定修正；若在輸入階段猜測所有失敗型態，便會像把 origin 的 response bug 硬塞進 Cache Rule，既複雜又修不準。
