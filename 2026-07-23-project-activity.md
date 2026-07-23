# GitHub 專案動態
- 檢查時間：2026-07-23
- 檢查對象：koala73/worldmonitor、ruvnet/RuView、ayghri/i-have-adhd、penecho/penecho、omnigent-ai/omnigent

## 動態摘要

### koala73/worldmonitor
- Release：最新版本仍為 `v2.5.23`（2026-03-01），近期開發速度已明顯超過正式發版節奏。
- Commit：最近三筆為 `2051db4`（部落格標題卡改用產品圖）、`09d4f88`（新增置頂文章）、`9f20929`（補上 live product catalog 三層快取清除 runbook），皆提交於 2026-07-22。
- Issue：今天新開的 #5471 指出 Docker 自架環境的 `/api/mcp` 無法正確驗證，sidecar 會把自己的 `LOCAL_API_TOKEN` 當成呼叫者 Authorization；同期 PR #5469 正補強 sandbox、docs-MCP JSON-RPC 錯誤與 agent-readiness。
- 連結：https://github.com/koala73/worldmonitor/issues/5471

### ruvnet/RuView
- Release：最新版本為 `v1954`（2026-07-22），剛完成新一輪發版。
- Commit：最新兩筆 `d9dfea2`、`88bd88e` 都在調整 Cognitum Musica 的文件圖片與 marketplace 呈現；第三筆 `aa7cb44` 則修正 CI 中 hardened entrypoint 對資產檢查的干擾。
- Issue：#1401「Interesting Concept but Doesn't Work」在發版後收到實際可用性回饋；PR #1400 同步補上 Python client WebSocket upgrade 時的 bearer token，#1402 則更新 vendor submodules。
- 連結：https://github.com/ruvnet/RuView/issues/1401

### ayghri/i-have-adhd
- Release：目前沒有公開 GitHub release。
- Commit：最新 `d94521d` 合併 #66，讓 always-on `SessionStart` hook 移除 Node.js 依賴、提高可攜性；同日的 `38fe67a` 也在削減 workflow noise。
- Issue：#59 指出安裝文件缺少 GitHub Copilot（VS Code / CLI）說明，對應 PR #60 已補上；#64 則持續有社群互動，顯示這套 agent skill 工作流正在從單一工具延伸到 Copilot 生態。
- 連結：https://github.com/ayghri/i-have-adhd/pull/60

### penecho/penecho
- Release：最新版本為首個公開版本 `v0.1.0`（2026-07-14）。
- Commit：最新 `9fc127b` 為 0.7.0 加入可擴充 canvas plugins；另外兩筆 commit 強化 README、Kimi 支援展示與動畫 demo，專案仍處於快速建立產品敘事的早期階段。
- Issue：近期前三筆 open 項目都是 PR：#16 新增中／日文 README，#14 加入 ask prompt，#13 支援把圖片匯入或由相機擷取到畫布，功能與國際化同步推進。
- 連結：https://github.com/penecho/penecho/pull/13

### omnigent-ai/omnigent
- Release：最新版本為 `v0.6.0`（2026-07-21）。
- Commit：今天三筆 commit 分別新增 project/session read-journey benchmark（`de1c6f0`）、統一 subagent 在列表與 graph view 的狀態點顏色（`d290e97`）、以及整理 project-folder header actions（`19976bc`）。
- Issue：今天最新的 PR #3105 重構背景 server CLI，#3104 啟用 Slack integration tests，#3103 推進設計調整；CLI、整合測試與多 agent 視覺化三條線同時活躍。
- 連結：https://github.com/omnigent-ai/omnigent/pull/3105

## 重點觀察
- 今天的 Trending Top 3 並非純 AI 清單，卻共同碰到 agent 工程的落地面：worldmonitor 的 MCP 驗證、RuView 的 WebSocket bearer token、以及 i-have-adhd 的跨工具 hook 可攜性，安全與整合可靠性比模型本身更突出。
- 五個專案中只有 worldmonitor 的最新 release 停在三月，但其 issue／commit 仍高度活躍，說明 release 版本號已不能單獨代表專案速度；RuView、PenEcho、Omnigent 則都在近十天內發版。
- PenEcho 和 Omnigent 呈現兩種 AI 互動介面的擴張方向：前者把推理搬到可手寫、放圖片的空間畫布，後者把多 agent 執行狀態搬進專案、session 與 graph view。
- 三個 repo 都在補強「跨邊界」能力：i-have-adhd 從 Claude 類工作流延伸至 Copilot、PenEcho 增加中日文與相機輸入、Omnigent 增加 Slack 整合測試；近期熱門專案競爭點正從核心 demo 轉向生態覆蓋。
- 今天 issue／PR 的訊號普遍比 release notes 更有資訊密度：認證錯誤、實際不可用回饋、CLI 改版和整合測試都在尚未進入下一版 release 前，先暴露了專案真正的成熟度。
