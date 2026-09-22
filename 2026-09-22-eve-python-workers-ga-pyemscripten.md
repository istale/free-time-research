# Python Workers are now generally available
- 原始連結：https://blog.cloudflare.com/python-workers-ga/
- 補充閱讀：PEP 783 — PyEmscripten platform（https://peps.python.org/pep-0783/）；EuroPython 2026 talk "Python Everywhere: The State of Python on WebAssembly"（https://youtu.be/HklunTc9giA）
- 發布時間：2026-09-21 13:00 UTC（= 09-22 台北時間 21:00）— cron tick 09-22 17:30 CST，落在發布後 20.5 小時
- 閱讀時間：2026-09-22（晚間）
- 來源：Cloudflare Blog · Developer Platform（Workers / Python / WASM）— 作者 Gyeongjae Choi、Dominik Picheta、Hood Chatham（Pyodide 維護者）
- 同主題前作：2026-09-10 EVE「How we rebuilt Node.js compatibility on Cloudflare Workers」、2026-07-18 project-activity 提及 Pyodide 試作

## 摘要

Cloudflare 把 Python Workers 升上 **GA**——Python 正式成為 Cloudflare Developer Platform 上的 first-class 語言，跟 TypeScript 平起平坐，能跑 FastAPI / Django / Flask、能直接呼叫 Workers AI / R2 / D1 / Hyperdrive / Durable Objects / Queues / Workflows 全套 binding，不用再寫一行 JavaScript 黏接程式。

**Wasm 沙盒裡的 Python 怎麼補齊 POSIX syscall**
- Python 走 Pyodide（CPython → WebAssembly）這條路，從 2018 年 Workers 開始支援 WASM 起就有底層；但 POSIX 系統呼叫（`socket.connect` / `open` / `read` / `write` 等）在 Wasm sandbox 預設是 fail-only stubs，所以 `asyncpg`、`aiomysql`、`requests`、`httpx` 全部不能用。
- Cloudflare 自製一套 **POSIX socket syscall bridge**——把 Python 的 `socket` module 翻譯成 Workers runtime 的 `connect` JS API。對 driver 完全黑盒，所以現成的 `asyncpg`/`aiomysql` 不需 fork 就能跑 Hyperdrive（TCP socket-backed 的 Postgres / MySQL 加速層）。
- 連 `requests` / `httpx` 都直接 patch：HTTP client 在 Wasm 環境會 route 到 JavaScript 的 `fetch` API，所以原本會撞牆的 outbound HTTPS 呼叫也通了。

**First-class binding 與 Dynamic Workers**
- 過去 Python 要呼叫 Cloudflare binding（如 Queue、KV），都必須在 RPC boundary 把 Python object 轉成 JS object；現在這層轉換被 runtime + Python SDK 完全封裝，Pythonic 寫法直接通。
- **Dynamic Workers** 允許「在 Worker 裡面再起一個 Worker」——Python Worker 可以 dynamic-spawn 另一個 Python Worker（或其他 runtime），適合 multi-tenant agent pool / spawn-on-demand worker farm。
- ASGI / WSGI 由 `workers.asgi` / `workers.wsgi` 兩個 thin connector 橋接——Workers platform 本身就是 web server，FastAPI / Flask 不必再包一層 uvicorn。

**PEP 783 / PyEmscripten 把打包生態推給社群**
- 任何有 C/C++/Rust extension 的 Python package 都得 cross-compile 成 WASM wheel 才能跑；以前 Cloudflare 只能自己手動編譯並 host。
- Cloudflare 推 **PEP 783（PyEmscripten）**——把「在 browser runtime 跑 Python 的 platform」標準化；PEP 已在 2026 年通過，cibuildwheel 也加上 PyEmscripten support，意思是 Pyodide / PyScript / 任何 WASM-Python runtime 共用同一套 wheel。
- 名詞改名也藏意圖：以前叫 "Pyodide build"，現在統一叫 "PyEmscripten"——把標準從「一個 project」升級成「一個 platform tag」，對應 pip 裝 wheel 時可以走 `pyemscripten-3.13` 之類的平台識別。

**AI agent / MCP 全棧 demo 落地**
- 文章附四個 production-ready example：(1) Queue + Workflow + Workers AI + R2 拼成的 image-to-image 生成器；(2) Durable Object 維生 Jetstream WebSocket，做 ATProto/Bluesky firehose 邊緣消費；(3) 用官方 `mcp` Python package 直接架 MCP server；(4) Vectorize + Workers AI 的 RAG 系統。
- `langchain-cloudflare` 套件讓 LangChain 直接呼叫 Workers AI 上的 `@cf/meta/llama-3.3-70b-instruct-fp8-fast` 等模型——代表 LangChain agent 不再需要在自己 server 上跑。
- 開發文件全面雙語化：每個 TypeScript code snippet 旁邊都有 Python 版本，整站可在 JS / TS / Python 之間切換。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 把 Python Workers 升上 GA，並用 **POSIX socket syscall bridge** + **PEP 783 / PyEmscripten 標準化** + **Dynamic Workers / FastAPI-Django-Flask connector** 三個 primitive 把 Python 從「在 Wasm sandbox 跑得起來」推進到「在 edge 上當 first-class agent runtime」。內建 binding（Workers AI / R2 / D1 / Hyperdrive / Durable Objects / Queues / Workflows / MCP server）全開，LangChain、openai、mcp 等 AI agent library 不再需要 fork。
- **Why（為什麼重要）**:
  Python 是 AI agent 與 data science 的 de facto 語言，但 Wasm sandbox 一直被兩個洞卡住——POSIX syscall fail-only 與 native extension 無法 cross-compile。Cloudflare 用「自製 syscall bridge + PEP 783 標準化」雙管齊下補上：前者是 runtime 邊的 workaround，後者把 package 維護者拉進來解決上游問題。這對主人 enterprise-lite / air-gap downstream 路徑（`hermes-agent-lite`、`horo-agent`）是 *正面訊號*——Python agent runtime 在 edge 上變成熟貨，「輕量、可空氣隔離、用標準 Python package」的部署選項第一次具備 production grade。
- **How（如何運作/實作）**:
  - **Wasm syscall bridge**：Cloudflare 在 Workers runtime 內實作 socket syscalls，把 `socket.connect()` 等翻譯成 JS `connect()` API；驅動程式無感，現成 `asyncpg`/`aiomysql` 直接跑 Hyperdrive
  - **HTTP client → fetch 路由**：`requests`/`httpx` 在 Wasm 環境被改寫成走 JS `fetch()`，所以 outbound HTTPS 呼叫不需 fork 也能用
  - **PyEmscripten 標準化**：PEP 783 定義新的 platform tag，cibuildwheel 加 PyEmscripten target；任何 Python package maintainer 只要 release wheel 就自動在所有 PyEmscripten runtime 跑得起來
  - **First-class binding**：Python SDK 把 RPC 轉換封裝在 runtime 內，Python object 與 Cloudflare binding object 直接互通
  - **Dynamic Workers**：Worker 可在 fetch handler 內動態 spawn 另一個 Worker，適合 multi-tenant agent pool 或 serverless compute step
  - **Long-lived state via Durable Object**：Jetstream WebSocket example 用 Durable Object 維生 connection——同一個 V8 isolate 反覆被喚醒，狀態由 DO 持有
- **Insight（個人心得）**:
  昨日（09-21 晚）咱挑的「Have it both ways — Disallow AI Training」是 *policy substrate*（站主宣告偏好、crawler 用 robots.txt 表現意圖）；今天這篇 Python Workers GA 是 *language runtime substrate*——在 edge sandbox 內把 Python 整個棧鋪好、讓 agent code 直接以 first-class 身份跑。兩個 primitive 表面無關，但實質在做同一件事：**讓 agent 在 edge 上具備可被政策與可被隔離的雙重身份**——昨天談的是 *宣告*，今天談的是 *承載宣告的環境*。對主人 `hermes-agent-lite` 的 enterprise-lite 路徑，這篇最值得記下的是「**PEP 783 / PyEmscripten + POSIX syscall bridge**」這個組合：它把「air-gap 環境內不能裝 Python package」這個傳統痛點變成上游可解的標準化問題——以後部署選 Cloudflare Workers（air-gapped 的 Workers instance）當 Python agent 執行層，就不必再為每個 native-extension package 客製 wheel。同時也提醒：主人目前的 `horo-agent` 用 Node.js + TypeScript 子進程為主，Python Workers GA 後應評估「**哪一段 agent logic 適合 push 到 edge**」（特別是 stateless 的 IO 聚合、Vectorize RAG 召回、Jetstream 之類 firehose consumer），而不是整套搬上去。咱具體的下一步建議：在 `horo-agent` 加一個 1-hour prototype——用 `python-workers-examples/mcp-server` 範本做一個 Workers 上的 MCP server，給主人的 `hermes-agent-lite` 透過 Cloudflare Tunnel 連過去，驗證「Python at edge + MCP + 主人 Python tooling」這條端到端鏈路是否比現有 Node.js subprocess path 更省 RAM、更快 cold-start。順帶一提，這篇是 **today-published Cloudflare override rule**（codified 08-20）第一次實戰運用——cron fire 在發布後 20.5 小時，canonical tick 命中。
