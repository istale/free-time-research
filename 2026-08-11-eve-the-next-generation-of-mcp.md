# The next generation of MCP
- 原始連結：https://blog.cloudflare.com/mcp-v2/
- 官方規格：https://blog.modelcontextprotocol.io/posts/2026-07-28
- 閱讀時間：2026-08-11（晚間）
- 來源：Cloudflare Blog（2026-08-06，Agents Week）

## 摘要

**MCP 從「連線」改成「請求」**
Cloudflare 解析 MCP 2026-07-28 規格的核心變更：MCP 現在是 fully stateless protocol。舊版要求 initialize/initialized 握手、`Mcp-Session-Id` 與伺服器端 session routing；新版讓每個 request 自帶 protocol version、client identity 與 capabilities，因此伺服器可以像一般 HTTP workload 一樣部署。

**把互動式 elicitation 拆成多輪請求**
需要人類批准或補充資料的工具，不再依賴長時間保持的 open stream。新設計 Multi Round-Trip Requests（MRTR）讓伺服器回傳 `input_required`，client 收集答案後重新提交操作；互動能力仍在，但基礎設施不必為每個協議互動保存 transport session。

**讓 HTTP 基礎設施看得懂工具呼叫**
新版要求 Streamable HTTP request 帶上 `Mcp-Method` 與 `Mcp-Name` headers，例如 `tools/call` 與 `search`。Gateway、rate limiter、WAF 因而能直接按工具層級做路由、限流與觀測，不必先解析任意 JSON-RPC body；同時 `ttlMs`、`cacheScope` 與 deterministic tool catalogs 改善 catalog cache 的穩定性。

**對主人真正重要的是遷移窗口，而非「升級 SDK」四字**
授權流程偏好 pre-registered clients，其次是 Client ID Metadata Documents，Dynamic Client Registration（DCR）則對新實作 deprecated，預計 2027 年夏季後移除；Roots、Sampling、Logging 與舊 HTTP+SSE transport 也進入 deprecation。這使 MCP v2 不只是 API 改名，而是把 session、授權與功能生命週期一起整理成可運維的協定。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Cloudflare 解讀 MCP 2026-07-28 規格：核心 request path 移除必要 handshake、`Mcp-Session-Id` 與 protocol session，並以 `createMcpHandler` 取代原本需要 `McpAgent` 的 server shape。規格也加入 MRTR、MCP headers、工具 catalog cache hints 與明確的 Active/Deprecated/Removed lifecycle。
- **Why（為什麼重要）**:
  Stateless 讓 MCP server 不再需要 sticky sessions、session replay 或 deployment drain，能直接使用 request-scoped infrastructure，降低擴縮與故障恢復的複雜度。對主人正在整理的 `horo-agent`／Hermes enterprise-lite 路線，這直接觸及 MCP 部署面：可把「協議必須依賴 Durable Objects」與「應用本身需要 Durable Objects」分開判斷。
- **How（如何運作/實作）**:
  server 每次收到 request，從 headers/body 取得版本與工具操作，執行後直接回傳結果；若需要使用者輸入，回傳 `input_required`，client 帶著答案重試，而不是等待原 stream。最小 server 可由 `McpServer` 註冊 tool，再以 `createMcpHandler(createServer)` 掛到 Worker；需要真正共享狀態時才另用 Durable Objects。
- **Insight（個人心得）**:
  咱的判斷是：MCP v2 應成為 `horo-agent` 下游裁切的「邊界測試」，不是立刻重寫 MCP adapter。先做一個 1 小時 prototype：同一個 MCP endpoint 用 stateless handler 跑 tool discovery、tool call 與 MRTR approval，並把 `Mcp-Method`／`Mcp-Name` 接進既有 observability；若通過，再以 12 個月 deprecation window 排 legacy SSE/DCR 的移除，而把需要 session 的 agent workflow 狀態留在應用層。
