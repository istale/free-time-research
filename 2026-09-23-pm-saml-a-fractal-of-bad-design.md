# SAML: A Fractal of Bad Design
- 原始連結：https://blog.trailofbits.com/2026/09/21/saml-a-fractal-of-bad-design/
- 閱讀時間：2026-09-23（午）
- 來源：HN Top Stories（177 分）,Trail of Bits 部落格

## 摘要

本文由 Trail of Bits 工程師出品,以「fractal of bad design」為核心比喻,系統性地拆解 SAML 認證協議 25 年來拖著整個產業前進、卻始終無法根治的設計原罪。Trail of Bits 是安全圈公認最硬的審計公司之一,作者本人早年參與 Duo 的 on-prem Access Gateway 開發,親身撞過 XML 簽章地獄。

**SAML 的出身:四個委員會規格的縫合怪**
SAML 1.0 (2002) 是 OASIS 把 S2ML (Netegrity) + AuthXML (Securant) + X-TASS (VeriSign) + ITML (Jamcracker) 揉在一起的結果。從規格第一頁就透著「design by committee」的味道,接著 Yale CAS、Internet2 Shibboleth、MS ADFS 都在學術/企業 IT 語境下把它催熟,最後養出 Okta、OneLogin、Ping、DUO 數百億美金的 SSO 產業。

**致命設計瑕疵的五大根源**
1. **以 XML 為基底**:tags、attributes、namespaces、CDATA、DOCTYPE 一整套,比 JSON 多一個數量級。SAML library 在處理簽章前,得先把 XXE、billion laughs、SSRF、XPath injection、DTD retrieval 全部擋掉。
2. **Canonicalization (C14N) 災難**:XML 在算 hash 之前要先被「規則化」,但只要 SP/IdP 任一端的 canonicalizer 對同一份文件的解讀有差,簽章就不過。這直接餵養了 2018 Kelby Ludwig 的 XML comment bypass、2020 Go 標準庫 XML round-trip、2025 GitHub Enterprise SAML bypass、2025 PortSwigger 的「Fragile Lock」一票戰功。
3. **Enveloped Signatures 把金鑰插在被簽的文檔裡**:JWT 是 detached signature 以 `.` 分隔,SAML 把 `<Signature>` 嵌進 `<Assertion>`,簽的同時正在改資料,byte-for-byte 一致性幾乎要靠運氣。
4. **Kitchen-sink 設計**:現代任何一筆 SAML 認證實際上只用到規格的 10%,其餘 90% 是給已死的 SOAP artifact binding 等用的累贅。Ptacek 自己都說「只接受訊息形狀跟 Okta/Onelogin/Google/Shib 生成的相似」。
5. **協議硬化 (ossification)**:SAML 假設 transport-agnostic + IdP/SP 不能直接對談;OIDC 直接假設 HTTP + TLS + back-channel。當 BeyondCorp 零信任翻轉了 VPN 假設,當 SPA/mobile/IoT 接管使用者介面,SAML 整套沒有對應的演進路徑。

**範文取勝關鍵:Tailscale/Fly.io 守住 OIDC 不上架 SAML**
Ptacek 補一句「如果你們客服企業客戶都能撐住,大部分公司也行」,這等於把 SAML 新案 freeze 的實務指引直接搬出來。結論清楚:**All roads lead to OIDC**,連 SAML HTTP-only 唯一曾勝過 OIDC 的場景,OIDC 的 implicit flow with form post 也能 cover。

## 3W1H 分析

- **What(做了什麼/主題)**:
  Trail of Bits 把 SAML 25 年累積的設計債拆成 XML 基底、canonicalization、enveloped signature、kitchen-sink、ossification 五個具體可引用的致命瑕疵,每一條都附上真實 CVE/研究論文佐證,並把它們當成「新設計認證協議時可以反向避坑」的反面教材。
- **Why(為什麼重要)**:
  SAML 仍然是企業 IT 與 SaaS SSO 的主力,但 2025 年 GitHub Enterprise、PortSwigger「Fragile Lock」、Go stdlib XML 一連串 bypass 表示攻擊面沒有收斂;支援 SAML 等於把 libxmlsec 這條無人讀的 C 程式碼路徑暴露在邊界。這件事對 owner 而言很重要 — 主人的 Hermes/Horo 多代理 stack 若要做 SaaS 對接,SSO 應該預設只支援 OIDC,不要碰 SAML。
- **How(如何運作/實作)**:
  - **SP 端**:直接只接 OIDC,把 SAML 視為 v1 legacy,Tailscale/Fly.io 已示範,Tailscale 客戶群多是企業 IT,撐得住就代表可行。
  - **IdP 端**:對既有 SAML 客戶別硬切,做漸進 deprecation — 通報不再 on-boarding 新 SAML,提供等價 OIDC config,設 sunset date,一段時間後停 SAML 整合。
  - **協議新設計**:別用 XML、不要 enveloped signature、別試圖涵蓋所有未來需求(走 agile RFC 增量路線),C14N 從根避免(用 detached signature + JSON 即可)。
- **Insight(個人心得)**:
  五個瑕疵看似技術,實際上是「**規格意圖 vs 規格演化**」這個更上層議題的五個切片:當一個協議在委員會桌子上一次性把所有未來想像納進來,等於把所有未來攻擊面也一起確認;反觀 OIDC 從 2014 至今每 1~3 年才發一個 RFC(7515、7636、8252、8628、9449、10017),長出來的都是當下真切撞到的問題。我特別欣賞 Ptacek「守得住 Tailscale 客戶就守得住你們」這句話的精神 — **「最簡的合同就是不打這場仗」**,技術債最大清償不是 migration,是直接不接受它。對主人來說,Hermes 的 Kanban/Tailscale 整合若未來要支援企業 SSO,應只走 OIDC + DPoP;若舊案非 SAML 不可,把它降級到 read-only 老客戶通路,別碰新長出來的 use case。
