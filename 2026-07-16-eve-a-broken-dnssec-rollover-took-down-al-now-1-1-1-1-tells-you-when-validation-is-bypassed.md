# A broken DNSSEC rollover took down .al. Now 1.1.1.1 tells you when validation is bypassed
- 原始連結：https://blog.cloudflare.com/dnssec-nta-ede-33/
- 閱讀時間：2026-07-16（晚）
- 作者：Sebastiaan Neuteboom（Cloudflare, 1.1.1.1 / DNS）
- 發布時間：2026-07-14

## 摘要

**一次 TLD 層級的 DNSSEC 事故**
2026 年 7 月 3 日，阿爾巴尼亞 .al 頂級網域進行 DNSSEC key rollover 時，根區的 DS record 仍指向已停止提供的舊 DNSKEY，造成驗證中的 recursive resolver 對整個 .al 網域回覆失敗。Cloudflare 1.1.1.1 先看到 SERVFAIL，直到 .al operator 移除 root zone 的 DS record、讓整個 TLD 暫時回到 unsigned 狀態後，解析才恢復。

**Negative Trust Anchor：先救可用性，再承擔風險**
Cloudflare 在等待 operator 修復期間，對 .al 套用 Negative Trust Anchor（NTA），告訴 resolver 將該 zone 視為 unsigned、暫停 DNSSEC 驗證。這讓政府服務、銀行與媒體網站重新可達，但代價是回覆在 NTA 生效期間沒有原本的加密驗證保證；更麻煩的是，過去 client 從 DNS response 本身看不出這個降級。

**EDE 33 把降級狀態放回 response 裡**
Cloudflare 與 Quad9 合作提出新的 Extended DNS Error code：EDE 33（Negative Trust Anchor）。在 .al incident 中，1.1.1.1 對受影響回覆同時附上 EDE 9（DNSKEY Missing）與 EDE 33；例如 `google.al` 仍能得到 A record，但 client 也能知道「這個答案是 resolver 暫時繞過 DNSSEC 後提供的」，而不是把 NOERROR 誤解成完整可信。

**從 out-of-band 狀態頁走向可機讀的透明度**
NTA 本身是必要的 operational escape hatch，尤其 TLD 故障會同時影響整個網域樹與所有 validating resolver；然而只靠 status page 或公開公告，監控工具與應用程式無法在查詢當下判斷 trust level。EDE 33 將這個資訊變成協定層的訊號，目前已被 `kdig` 辨識，Unbound 也有 pull request 審查中，草案則提交到 IETF DNSOP Working Group。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 說明 .al 的 DNSSEC key rollover 如何使整個 TLD 失效，以及 1.1.1.1 如何用 NTA 暫時恢復解析。文章的核心不是事故本身，而是把「此回覆是在跳過 DNSSEC 後產生」透過 EDE 33 直接帶進 DNS response，讓 client、監控與 operator 都能機讀這個信任降級。

- **Why（為什麼重要）**:
  這是一個典型的 availability 與 authenticity 取捨：回覆 SERVFAIL 會讓服務不可用，直接放行又可能讓使用者誤以為答案仍經過完整驗證。把降級狀態標在 response 裡，才能讓上層系統依風險決定是否接受答案、告警、重試或改走其他 resolver，而不是把事故藏在 resolver 內部。

- **How（如何運作/實作）**:
  1. resolver 偵測到 DS 與 DNSKEY chain 斷裂後，對故障 zone 部署 RFC 7646 定義的 NTA，暫停該 zone 的 DNSSEC validation。
  2. NTA 生效時，1.1.1.1 仍回覆可用的 DNS answer，並附上 EDE 9 說明底層 DNSSEC 問題、EDE 33 說明 NTA 已介入。
  3. 上層工具可讀取 EDNS Extended DNS Error；NTA 移除或 zone 恢復正常後，這個降級訊號也隨之消失。

- **Insight（赫蘿的觀察）**:
  咱覺得這篇真正值得主人帶走的，不是 DNSSEC 的事故細節，而是 **「fallback 必須攜帶自己降低了多少保證」**。這與主人最近看的 agent optimizer 很像：RELAI-VCL 用 regression gate 告訴系統哪些能力不能被新修補換掉；EDE 33 則把 resolver 的「我先放行，但現在不是完整驗證」明白標出來。對 Hermes 或本地 agent 的設計，凡是 timeout fallback、弱化安全檢查、使用未驗證快取或降級模型，都不該只回傳一個看似成功的結果——應把 trust level 一起放進結果，否則最危險的 bug 不是失敗，而是帶著錯誤自信的 NOERROR。
