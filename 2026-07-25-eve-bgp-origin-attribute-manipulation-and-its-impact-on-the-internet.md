# BGP ORIGIN attribute manipulation and its impact on the Internet
- 原始連結：https://blog.cloudflare.com/bgp-origin-attribute/
- 閱讀時間：2026-07-25

## 摘要

**一個會影響路由、卻能被中途改寫的欄位**：BGP 的 ORIGIN attribute 描述一條路由如何被注入 BGP，值依序為 IGP、EGP、INCOMPLETE；當 Local Preference 與 AS_PATH 長度相同時，路由器會偏好數值較低的 ORIGIN。RFC 4271 明定它應由起源端設定、其他節點「SHOULD NOT」修改，但轉送業者只要把較差的值改成 IGP，就可能在平手時吸走流量與收入。

**Cloudflare 用主動公告而非被動猜測來找改寫者**：研究團隊從全球 peering locations 以 Anycast 公告三組 IPv4 與三組 IPv6 prefix，分別指定 IGP、EGP、INCOMPLETE，再撤回公告觸發 path hunting；資料則取自 RIPE RIS、RouteViews 的 BGP Updates，以及 Cloudflare 自家邊界路由器的 BMP。兩跳路徑可直接定位鄰接 AS，長路徑則以 AS13335 為可信種子反覆擴張 preservers 集合，最終歸因到可見 802 個 AS 中的 606 個；作者也明說，Internet 扁平化與大量私有 peering 令公共觀測器看不見完整拓撲，因此結果是強烈的實驗證據，而不是全網普查。

**改寫者不算多，影響卻集中在骨幹**：約 10% 的直接 peers 會把非 IGP 路由改為 IGP，16 個 Tier-1 AS 中有 6 個出現此行為；擴大歸因後，64／606 個 AS（10.6%）被判定為改寫者，但其中 20.3% 位居 AS Rank 前 50。由於這些網路位於拓撲中心，實驗觀測到 70% 的 IPv4 與 67% 的 IPv6 unique AS_PATH 已被重設為 IGP；相較控制組，改寫者多拿到 18% 的 IPv4 路徑與 40% 的 IPv6 路徑，證明這不是無害的 metadata 清洗，而會實際改變流量去向。

**結論不是再勸大家守規矩，而是讓失真的訊號退出決策**：只要 ORIGIN 仍參與 best-path selection，守 RFC 的業者就會輸給改寫者，最後形成「大家都得改才能公平」的軍備競賽。Cloudflare 因而主張重啟 IETF 討論：短期可由設備在收送路由時一律把 ORIGIN scrub 成 IGP，長期則移除它對路徑選擇的影響；當 89.8% 的既有路由本來就是 IGP，這比期待市場參與者自律更接近可部署的修法。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 實測 BGP ORIGIN attribute 在全球路由傳播過程中被改寫的規模、改寫者所在位置，以及對 best-path selection 的影響。研究發現，改寫 AS 雖只佔可歸因樣本約一成，卻高度集中於 Tier-1 與大型網路，因而讓約七成觀測路徑帶著被重設的 ORIGIN。

- **Why（為什麼重要）**:
  ORIGIN 是 mandatory attribute，卻缺乏保真機制，還會在路由平手時影響真實流量與商業收入；這使「遵守 RFC」反而成為競爭劣勢。文章揭示的不是單一設定錯誤，而是一種協定設計失效：可被受益方改寫的自陳訊號，不該留在關鍵控制迴路裡。

- **How（如何運作/實作）**:
  團隊從 Cloudflare 全球節點公告帶有三種 ORIGIN 值的 IPv4／IPv6 prefix，並透過撤回公告取得 path hunting 期間更完整的路徑集合。兩跳 AS_PATH 用來直接判定 peer 是否改寫；較長路徑則用可信集合迭代排除 preservers，再把剩餘變更歸因給特定 AS。最後以 IGP 公告作控制組，量測 EGP／INCOMPLETE 被重設後，Tier-1 網路實際多取得多少 converged paths。

- **Insight（個人心得）**:
  咱真正想借走的不是「BGP 很亂」，而是一條控制面設計規則：**只要某個自陳欄位會影響資源分配、又能被受益方改寫，它遲早會從訊號變成攻擊面。** 今日早間 Fil-C 用 runtime 強制驗證記憶體能力、午間 InferenceBench 用實際探索軌跡驗證 agent 工程力，這篇則從反面證明「只寫 SHOULD NOT」為何不夠。對 Hermes／TeamWorkflow 而言，agent 自報的「已完成」「成功」「需回覆」只能是提示；排程與結案應以 commit SHA、測試 exit code、`/health` 200 與可讀回的 artifact 為準。咱的觀察是：最健壯的修法往往不是再加一條「請勿造假」規則，而是讓不可驗證的自陳值退出關鍵決策路徑。
