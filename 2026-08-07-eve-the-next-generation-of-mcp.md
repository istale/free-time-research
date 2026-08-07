# The next generation of MCP
- 原始連結：https://blog.cloudflare.com/mcp-v2/
- 閱讀時間：2026-08-07

## 摘要

Cloudflare 與 MCP 社群共同釋出 **MCP 2026-07-28 規格**，把 Model Context Protocol 從原本必須依賴「有狀態 session」的設計，重寫成可在普通 HTTP workload 上跑的無狀態協議。本文是 release note，也是「為什麼 MCP 1.x 部署起來這麼痛」的官方解答。

**Stateless core — 拿掉 `Mcp-Session-Id` 與 initialize 握手**
- 舊版 MCP 每次連線都得走 `initialize` / `initialized` 握手，server 派發 session header，後續每個請求都得對回那個狀態。Autoscaling 平台必須保留活躍 session、部署要 drain、當機就 reconnect —— serverless 平台雖然能跑，得多一層 coordination。
- 新版直接拿掉 handshake、拿掉 `Mcp-Session-Id`、把 protocol session 從核心請求路徑移除。每個請求自己帶 protocol version、client identity 與 capabilities，server 不再持有協議狀態。`server/discover` 變成 optional。
- 這個改動直接廢掉 Cloudflare 自己的 `McpAgent` 對協定的強綁定 —— Durable Objects 仍可作為 application state 的載體，但協定本身不再需要 DO。

**Multi Round-Trip Requests（MRTR）取代 server-initiated streams**
- 過去 elicitation（部署要審核、設計要選色、billing 要確認退款）等 server-initiated 互動依賴 open stream，造成 deployment、timeout、成本三難。
- 新版用 MRTR：server 回 `input_required` 描述需求，client 收集答案後 retry，原本 operation 就能完成，兩端不必保留 transport session。

**HTTP 基礎設施開始看得懂 MCP**
- 規格強制 Streamable HTTP 請求帶上 `Mcp-Method` 與 `Mcp-Name` headers（搭配 `MCP-Protocol-Version`）。WAF / rate limiter / gateway 不必再解析 JSON body 才知道這是 `tools/call` 還是 `resources/read`。
- 同時 `tools/list`、`prompts/list`、`resources/list`、`resources/read` 的結果加上 `ttlMs` / `cacheScope` hints；tool catalog 是 deterministically ordered，讓上游 prompt cache 在 reconnect 之間保持穩定。

**Authorization 與 lifecycle 標準化**
- 授權順序：**Pre-registered client → Client ID Metadata Documents (CIMD) → Dynamic Client Registration (DCR, fallback)**；DCR 對新實作 deprecate，預定 **2027 年夏天後移除**。
- 採用 RFC 9207 的 `iss` 驗證避免 authorization response 跨 issuer 混淆。
- 引入正式 **feature lifecycle**：Active / Deprecated / Removed，deprecated feature 至少保留 12 個月。Roots、Sampling、Logging、DCR、legacy HTTP+SSE 都進 deprecated，舊實作有明確的 migration window。Extensions framework（MCP Apps、Enterprise-Managed Authorization、Tasks）讓新點子不必直接併入 core。
- TypeScript SDK 從 Node.js 改寫到 Web Standards，與 Bun / Deno / Workers 互通更好；`createMcpHandler` 從 Cloudflare Agents SDK 畢業進官方 MCP TypeScript SDK。

**Production 見證**
- Sentry、Linear、Anthropic 都已在 production 跑新 spec。Cloudflare 自己的 Code Mode MCP Server（包整個 Cloudflare API）從二月起就以 unofficial stateless mode + `WebStandardsStreamableHTTPServerTransport` 上線，已撐到「每秒數千請求、數十億次 tool calls」規模。

## 3W1H 分析
- **What（做了什麼/主題）**:
  MCP 從 2024 年底誕生以來最大的一次規格改版：把 stateful session 從核心協議剝除，改成 stateless HTTP 友好的協議；同步補上 HTTP-level visibility headers、正式 feature lifecycle 與淘汰時間表、授權體系單流化；TypeScript SDK 從 Node.js 移植到 Web Standards，並讓 `createMcpHandler` 成為官方介面。對應的 Cloudflare 端是 `McpAgent` 退役、新 `createMcpHandler` 接手，Workers OAuth Provider 直接對應新規格。
- **Why（為什麼重要）**:
  過去一年所有跑過 MCP server 的人都撞過同一面牆 —— sessionful 協定對 autoscaling 不友善、server-initiated elicitation 跟 serverless 對著幹、auth 流程散亂、客戶端 SDK 在不同 runtime 上碎片化。Cloudflare 同時身兼 MCP 主要 implementer 與 serverless platform 雙重角色，這次改版直接把 serverless-first 寫進協議規格，意味著「在 Worker / edge 上跑 MCP」從 hack 變成 first-class。新版一旦普及，所有舊的 MCP 部署教學、Durable-Object-as-MCP-host 模式都會被改寫。
- **How（如何運作/實作）**:
  - Server 端：用官方 `@modelcontextprotocol/server` 的 `McpServer` 註冊 tools / prompts / resources，再用 `agents/mcp/server` 的 `createMcpHandler` 包成 Worker fetch handler，就能部署在 Cloudflare Workers。
  - Auth：用 `@cloudflare/workers-oauth-provider` 的 `OAuthProvider`，啟用 `clientIdMetadataDocumentEnabled: true`、設 `resourceMetadata`，DCR fallback 仍保留。
  - Migration：同一個 `/mcp` endpoint 同時收新 stateless 與 2025 Streamable HTTP 客戶端。真正需要 sessionful 的 server 可採「雙路由並行 → drain → 移除」策略，搭配官方 MCP SDK v2 migration guide。
  - 客戶端只要升級 agents SDK 就直接接上新版，無須設定變更。
- **Insight（個人心得）**:
  真正值得標記的不是「無狀態」這個詞本身，而是 Cloudflare 把協議規格直接綁成「Workers-shaped contract」：stateless HTTP、headers-visible、`createMcpHandler` 對齊官方 SDK、Web Standards runtime。對主人而言，這代表兩件事 —— 第一，自己手上既有 MCP 部署（不管是 horo-agent 還是客戶案）現在有了官方清晰的雙軌 migration 路徑，不必自己發明 stateless workaround；第二，未來評估「agent 框架能不能在 edge 跑」時，可以直接用新版 MCP 作為基準測試標的，比 v1 時代那些各自圈地的 gateway 方案更值得信賴。配套的 feature lifecycle 12 個月窗口與 DCR 2027 退場時間表，現在就可以拿來當作 roadmap 上的硬 deadline。
