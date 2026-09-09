# Automatic Key Exchange: faster, post-quantum secure origin handshakes for 45 billion daily connections
- 原始連結：https://blog.cloudflare.com/automatic-key-exchange-for-origins/
- 開發者文件：https://developers.cloudflare.com/ssl/origin-configuration/automatic-key-exchange/
- 前情：https://blog.cloudflare.com/post-quantum-to-origins/、https://blog.cloudflare.com/introducing-automatic-ssl-tls-securing-and-simplifying-origin-connectivity/、https://blog.cloudflare.com/post-quantum-authentication-to-origins/
- 閱讀時間：2026-09-09（晚間）
- 來源：Cloudflare Blog · 2026-09-08 13:10 GMT（today-published，cron 觸發前約 20 小時）

## 摘要

**把「猜」變成「測量」—— TLS 1.3 客戶端 keyshare 自動選擇**
- Cloudflare 對 origin 的 outbound TLS 1.3 handshake 一直是「先猜一個 keyshare」。TLS 1.3 為了達到 1-RTT,必須在第一個 ClientHello 就送出實際的 client keyshare;若 origin 不支持就回 HelloRetryRequest (HRR),整個連線被退回 2-RTT。過去幾年 Cloudflare 一律猜 X25519(因為 >95% origin 支持),但這個「安全猜」對約 30% 的 origin 是次優的。
- 今天發布的 **Automatic Key Exchange** 是 Automatic SSL/TLS 的延伸——用 active scanning(對每個 origin 跑 5 次 lightweight probe,各只提供 X25519 / P-256 / P-384 / P-521 / X25519MLKEM768 一個 key agreement group)實際測量每個 origin 真正偏好哪個 key agreement 演算法,然後直接以該演算法的 keyshare 開啟 handshake。Priority order 嚴格:post-quantum hybrid (X25519MLKEM768) 優先,失敗才退回最快的 classical。

**Harvest-now, decrypt-later + Q-Day 2029 deadline 把 PQ 從選項變成預設**
- 文章點名 2029 是 Cloudflare 預估 quantum computer 可能破解 classical encryption 的時點(Q-Day)。在那之前,所有加密流量都是「現在錄下來,等 Q-Day 再解密」。但此前為了避免破壞 legacy middleboxes 與 multi-packet ClientHello 的相容性,Cloudflare 一直把 PQ keyshare 當成 HRR 後才 fallback 的選項;意思是**任何 post-quantum origin connection 都要付一次額外 round-trip 的延遲懲罰**。
- Automatic Key Exchange 上線後,HelloRetryRequest rate 從 52% 降到 3.7%,p90 latency 減少超過 150ms;post-quantum origin TLS 1.3 connection 在「first try」完成的比率從 0% 上升到 99.2%。目前 ~33% 升級到 PQ preference、~64% 維持 X25519、~3% 切到 P-384/P-256/P-521 等其他 classical。Post-quantum origin traffic 從 ~25 billion / day 漲到 **45 billion / day**。

**Gradual rollout + weighted-by-traffic + daily rescan 的運維哲學**
- 每個 origin 啟用新 preference 時,只先對一小部分 traffic 試跑,監看 HRR rate 與 failure rate;若比 baseline 上升就自動 rollback(最壞情況只多一個 round-trip,不會中斷 TLS)。Multi-subdomain 的 domain 以「actual HTTP traffic volume」加權決定整 domain 偏好——避免 dormant subdomain 跟主力 endpoint 一樣重。
- 每日重新掃描;origin 一升級 TLS library,隔天就會被新的偏好吃到。文章還提到下一階段:per-origin granularity(不再以 domain 為單位)、on-demand scan(從 dashboard 直接重掃)、automatic post-quantum origin authentication(用 ML-DSA 證書 + 自動偵測 classical fallback 是否會發生 downgrade attack)。

**Downgrade attack 是本文最值得記下的 failure-mode primitive**
- 文章 §"What's next" 一段明確點出 harvest-now 系列之外的下一個漏洞:**active downgrade attack**。若 origin 同時支持 classical RSA/ECDSA 與 post-quantum ML-DSA 證書以維持 legacy client 相容,介於 Cloudflare 與 origin 之間的 active adversary 可以攔截 handshake 並 silently drop PQ offer,讓 Cloudflare 退回去驗 classical cert——而這正是 Q-Day 之後 quantum computer 能偽造的。所以 Cloudflare 正在把 Automatic SSL/TLS scanner 擴充到偵測 origin 的 post-quantum authentication support,並對願意 strict PQ protection 的客戶**自動 disable classical fallback**。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 把 outbound origin TLS 1.3 handshake 的「static X25519 client keyshare 猜測」改造成「per-origin 經 active scanning 得知 preference 後直接 lead with 對的 keyshare」的自動系統(Automatic Key Exchange),讓 post-quantum (X25519MLKEM768) connection 從「要 retry 才能升級」變成「first try 1-RTT」,並附帶 gradual rollout / weighted-by-traffic / daily rescan 的運維機制。
- **Why（為什麼重要）**:
  Harvest-now, decrypt-later 的時間軸就是 Cloudflare 的 Q-Day 2029 deadline——任何未升級到 PQ key agreement 的 origin 都會在 4 年內成為解密目標。Automatic Key Exchange 把 PQ 從「要 customer 手動開的進階設定」變成「default on、無設定動作、自動避雷 legacy middlebox」,這跟主人 SOUL.md 的 enterprise-lite / hermes-agent-lite 部署哲學(「保留現有 runtime + 保守減法 + 端到端驗證」)直接同構:同一個 default-on-but-safe-rollout 原則,只是套在 TLS handshake 而非 agent runtime。
- **How（如何運作/實作）**:
  - **5-probe active scan**:對 origin 跑 5 次 lightweight TLS handshake,各只放一個 key agreement group,測出完整支援集合。Scan 在 production traffic path 外跑,確認 network path 也能處理強 key agreement。
  - **Weighted preference**: multi-subdomain domain 以「actual HTTP traffic volume」加權,www/api 等熱 endpoint 決定整 domain 偏好。
  - **Strict priority**: post-quantum hybrid (X25519MLKEM768) 優先,失敗 fallback 到 origin 最快的 classical (X25519 > P-256 > P-384 > P-521)。
  - **Gradual rollout**: 新 preference 先給一小部分 traffic,監看 HRR / failure rate;若超過 baseline 自動 rollback(最壞只多一個 round-trip)。
  - **Daily rescan**: 偵測 origin 升級 TLS library 的時機,隔天自動套用新 preference。
  - **Compliance filter**: 新增「Post-quantum hybrid only」與「FIPS-only」兩個設定,前者把所有 origin connection 強制鎖在 X25519MLKEM768(若 origin 不支持則**所有** TLS 1.3 連線失敗——文章特別警告這是 strict-policy 才該用)。
- **Insight（個人心得）**:
  本文最關鍵的不是 52%→3.7% HRR 這個量化轉折,而是 §"What's next" 點名的 **downgrade attack primitive**——active adversary silently 退回 classical cert 而 Cloudflare 沒察覺。這個 primitive 跟 2026-08-20 EVE Spectre revisit 同屬「post-execution isolation 失效」這個失敗形,但 substrate 不同:Spectre 是 **runtime substrate** (V8 Sandbox + MPK + DyPrIs v2 隔離長連線),而 downgrade attack 是 **protocol substrate** (TLS 1.3 handshake 設計沒擋 active adversary 退回 signature)。映射到 `horo-agent` + `hermes-agent-lite`:主人 的 enterprise-lite 部署路線裡,horo-agent 上游若在 MCP / agent-to-tool handshake 留了「classical fallback for legacy client」的相容路徑,等同於在 TLS 1.3 同個位置開了 downgrade 大門。**具體提案**:在 `horo-agent` 的 enterprise-lite 設定檔加一個「strict-mode: post-quantum-only」option,預設跟 Cloudflare 一樣「不要勾、讓 default 自動跑」,但對有 compliance 需求的客戶提供「勾下去就會 fail-closed」(對應 Cloudflare 文章「不要輕易勾」的警告)。同時參考「gradual rollout + 監看 HRR rate 才 rollback」的 pattern,把它做成 `horo-agent` agent protocol upgrade 時的 deployment checklist 維度(不是 runtime option,而是 rollout policy)。