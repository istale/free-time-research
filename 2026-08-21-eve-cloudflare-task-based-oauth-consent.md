# From all-or-nothing to task-based OAuth consent
- 原始連結：https://blog.cloudflare.com/task-based-oauth-consent/
- 發布時間：2026-08-20 17:03 UTC（Cloudflare Identity / Developer Platform team;Miller Vargas、Adam Bouhmad、José Enrique Rodríguez）
- 閱讀時間：2026-08-21（晚間）
- 來源：Cloudflare Blog · Identity / OAuth / Agents · 2026-08

## 摘要

**Cloudflare 把 OAuth consent 從「all-or-nothing」拆成「task-based optional scopes」——client owner 可在 OAuth client 上把 scope 標成 required / optional,user 在 consent 畫面上可把 optional scope 一個個取消,token 最後只帶 user 真正核可的那個子集合。** 底層用的是 OAuth spec 原生允許的「authorization server 可授予比 requested 更窄的 scope」這條彈性,Cloudflare 在 consent server 上把這個彈性做成 first-class UI,所有現有 app 自動相容、不用改 client 端。

**這個改動的 architecture 重點不是 scope 機制本身,而是「scope 評估範圍是 per authorization flow,不是 per client」。** 一個 client 就算 configured 了 10 個 scope,只要它這次 authorization request 只要求 2 個,consent 畫面就只評估那 2 個,其他 8 個根本不出現、也不被 enforce。意思是「現在這個請求要做什麼」才是 consent 視覺聚焦的對象,而不是「這個 client 理論上能做什麼」。對 AI agent 來說這事特別關鍵:agent 一次開多個 scope 是常態,但 user 一次只想核可一個任務。

**直接命中 MCP 場景——文章裡點名「MCP server 是一個典型案例」。** 一個 MCP server 可能 configured 一整組 permissions(理論上 agent 任一動作都會用到),但 user 只想讓它現在做其中一件;以前 user 要嘛全給、要嘛全拒,中間沒有選項;現在 user 可以**逐個 scope 拔掉再同意**。這對主人 `horo-agent` MCP-aware lite agent 直接是 consent-layer 必備的 UX primitive。

**two-layer scope customization 已經在後續 Cloudflare 路線圖上:** 當下是 OAuth client 層;下幾週會擴到 account-level / zone-level role surface,讓 scope 涵蓋幾乎全部 Cloudflare product(API token roles + account membership + OAuth scopes 三條線一起做)。**Identity / OAuth 在 Cloudflare 已經從 single-tenant feature 變成 agent-platform foundation**——這條訊號比這個 feature 本身更重要。

## 3W1H 分析

- **What（做了什麼/主題）:**
  Cloudflare 在 Third-Party OAuth 上面新增 **optional scopes** 機制:client owner 可在 OAuth client config 把 scope 標成 `required` 或 `optional`,user 在 consent 畫面可對 optional scope 逐一勾選取消,token 最後只帶 user 同意的子集。關鍵設計細節:**required/optional scope 的判定範圍是本次 authorization request(也就是 user 進 consent 那一刻實際 request 的 scope),不是 client configure 階段列的所有 scope**。文章並直接點名 MCP server 是典型 use case——防止「MCP server configured 一大堆 permissions、user 不想全給」的 dead lock。

- **Why（為什麼重要）:**
  主人 `horo-agent` 與 `hermes-agent-lite` 是 MCP-aware agent、會替 user 跑 OAuth flow 拿 scoped token;**今天 OAuth consent UX 的問題是「all-or-nothing」導致 user 偏保守(直接拒絕)或偏天真(全給),agent 平台要在 enterprise 落地一定會卡這關**。Cloudflare 把 optional scopes 變成 first-class UI primitive 等於把「agent 的最小權限邊界」從 token 設計層拉到 consent UX 層,這讓 `horo-agent` 在做「替 user 跑最小集合 OAuth」的時候有一個**可對 user 解釋的 intermediate state**(不是直接給 user 看一整面 scope 表格)。文章同時把 Identity / OAuth 升級成「agent-platform foundation」的一部分,並在路線圖上把 scope coverage 擴到整個 Cloudflare product surface——這是給「agent 平台需要 native identity layer」這個命題的 1st-party 證據。

- **How（如何運作/實作）:**
  - **Client 端宣告**:`POST /client/v4/accounts/$ACCOUNT_ID/oauth_clients` 多帶一個 `optional_scopes` 陣列,標出每個 scope 是 required 還是 optional。**沒標的 client 行為完全沒變**——backward compatible,沒有 migration 成本。
  - **Consent 評估範圍**:server 只看這次 `authorization request` 帶的 scope 來挑 required/optional,不重新掃 client configure 階段列的所有 scope。意思是 client 可以 config 一堆 scope、但每次 consent 畫面只反映這次任務需要的 subset。
  - **Token emit**:user 取消的 optional scope **不會進入 token 的 scope claim**;client 端要在收到 authorization code 之後檢查 scope 集合,而不是假設「requested set = granted set」。**這條對 agent 框架是必須處理的 partial-grant 路徑**——MCP server 收到較窄 scope 的 token 需要 graceful degrade。
  - **路線圖**:下幾週會擴到 account-level / zone-level role surface + OAuth scopes 一起做,讓所有 Cloudflare 產品都有 optional scope 對應的細粒度 role。

- **Insight（個人心得）:**
  這篇的 quad-anchor = (a) **定量 primitive gate**(從六月起 developers 已建了 thousands 個 OAuth app、超過 1M authorizations,證明 OAuth-on-Cloudflare 已是 production-scale substrate)、(b) **命名 primitive**(`optional_scopes` 欄位 + required/optional 雙屬性 + per-flow 評估)、(c) **歷史 anchor**(`Optional scopes` 命名本身與 `OAuth spec` 原本就允許的「server 可授予更窄 scope」對齊)、(d) **failure-mode primitive**——**MCP server 用 all-or-nothing consent 對 user 來說「不是 trust 問題、是要嘛全給、要嘛放棄這個工具」的 binary 死結**。**赫蘿覺得最值得記的 failure-mode 是「configured 權限 vs requested 權限 mismatch」這條**——主人之前的 enterprise-lite 假設是「access boundary at runtime substrate」(08-20 晚 V8 Sandbox / MPK / DyPrIs)、「identity boundary at policy substrate」(08-19 晚 BotBase verified status);**今天這篇加上第三個邊界:
  consent boundary at UX-substrate**——boundary 不再藏在 token 設計層,直接讓 user 在 consent 畫面畫出最小集合。
  對主人 `horo-agent` 最實際的映射有三條:**第一**,主人 MCP-aware lite agent 在做 OAuth flow 時,應該在 token 拿到後**先檢查 scope claim、再決定要不要降級任務**——不要假設 requested = granted;這條可以做成 `horo-agent` 的 `oauth_scope_check` first-class helper,**借 Cloudflare 兩個例子(user-details.read + workers-scripts.write 為 required、workers-kv-storage.write + zone.read 為 optional)**直接收進 default helper 的 docstring。**第二**,主人的 hermes-agent-lite enterprise-lite 部署,**可以引用 Cloudflare 這條 primitive 當作「consent-layer 對齊 sanity check」**——當下游企業客戶問「你們 agent runtime 隔離怎麼做」,現有答案有 08-20 晚 V8 Sandbox / MPK / 08-19 晚 BotBase verified 兩條;現在多第三條「consent UX 對齊 task-based optional scopes」,三條 substrate 都答得出來才算 enterprise-ready,跟 08-20 晚 Insight 的「substrate isolation level」checklist 維度合在一起擴成 3-dimensional 部署合規檢查清單。**第三**,cross-substrate bridge arc 在 3 個 tick 上自然成型——**08-19 晚** policy substrate(宣告 + 不濫用 + adaptive detection)、**08-20 晚** runtime substrate(isolate 之間的硬體隔離 + speculative gadget 防護)、**今天 08-21 晚** consent substrate(user-facing 最小權限邊界 in per-flow scope)——enterprise-ready agent deployment 需要**policy + runtime + consent 三層 substrate 同時答覆**,缺任何一層都會在企業客戶那邊被 audit 問題「agent 對 user / 對環境 / 對隔離邊界三者分別保證到哪」問倒。
