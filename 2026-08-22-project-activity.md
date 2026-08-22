# GitHub 專案動態
- 檢查時間：2026-08-22（14:00 台北時間）
- 檢查對象：mattpocock/skills / mahlernim/google-timeline-visualizer / harry0703/MoneyPrinterTurbo / microsoft/agent-framework / block/buzz
- 來源組合：GitHub Trending today（Tier-A 3）+ delegate_task constrained search（Tier-B 2，5th verified path 1，第 2 次 exclude 強化去重）

## 動態摘要

### mattpocock/skills
- 類型：release / trending（昨日 1-day repeat）
- 內容：trending top 1（3,362 stars today，比 08-21 的 2,192 stars today 增 53% — viral 持續）；最新 release `v1.2.3`（2026-08-06，16 天前）；230k★ 累計、MIT、Shell scaffold。
- 連結：https://github.com/mattpocock/skills/releases/tag/v1.2.3

### mahlernim/google-timeline-visualizer
- 類型：release（今日剛發）
- 內容：trending top 2，1,053 stars today；最新 release `v2.2.13`（**2026-08-22 00:44 UTC = 台北時間今天上午 8:44 剛發**）；2,310★ 累計、MIT、Kotlin + Python desktop backend。
- 連結：https://github.com/mahlernim/google-timeline-visualizer/releases/tag/v2.2.13

### harry0703/MoneyPrinterTurbo
- 類型：release / trending（5 天內第 4 次 repeat — 08-18/19/20/22）
- 內容：trending top 3，1,201 stars today；最新 release `v1.3.4`（2026-08-12，10 天前）；111k★ 累計、MIT、Python + Streamlit WebUI。
- 連結：https://github.com/harry0703/MoneyPrinterTurbo/releases/tag/v1.3.4

### microsoft/agent-framework
- 類型：release（昨天）
- 內容：Tier-B web 探索 1，delegate_task 推薦、HTTP-200 + metadata 驗證通過（13,034★ / MIT / Python + .NET / 593 open issues）。最新 release `python-1.15.0`（**2026-08-21 23:08 UTC = 台北時間昨天深夜**）；同週 2026-08-18 還有 `dotnet-1.18.0`。1.x 系列是 2026-08-03 從 AutoGen + Semantic Kernel 合併升級 GA 後的 stable runtime 層。
- 連結：https://github.com/microsoft/agent-framework/releases/tag/python-1.15.0

### block/buzz
- 類型：release（昨天）
- 內容：Tier-B web 探索 2，delegate_task 推薦、HTTP-200 + metadata 驗證通過（29,352★ / Apache-2.0 / Rust / 2,989 open issues）。最新 release `desktop-v0.5.18`（**2026-08-21 17:19 UTC = 台北時間昨天**）；同週 08-18 還有 `desktop-v0.5.17`、08-15 `desktop-v0.5.14` — 3 個 desktop release / 7 天內，desktop 端節奏比 backend relay 還快。Block（Jack Dorsey 的 Square 公司）開源的 Nostr-based 自架協作平台。
- 連結：https://github.com/block/buzz/releases/tag/desktop-v0.5.18

## 重點觀察

- **週末 trending 仍照跑，但 viral 加速**：今天週六（08-22）Tier-A trending top 3 = `mattpocock/skills`（trending rank 1、3,362★/day，比 08-21 的 2,192★/day 漲 53%）+ `mahlernim/google-timeline-visualizer`（trending rank 2、新進榜、1,053★/day） + `harry0703/MoneyPrinterTurbo`（rank 3、1,201★/day）。**0/3 fresh tier-A picks** — 跟前天（08-21）的 0/3 + 昨天（08-20）的 0/3 形成 3-consecutive 自然 floor，呼應 08-20 codification 的「0/3 = 健全結構」讀數；但「昨天有 3 個重複（mattpocock/skills 1-day + MoneyPrinterTurbo 5-day）」的 part-of-day repeat 仍然存在，符合 08-16 codification 的「5-day window 比 1-day window 更穩」原則。
- **5/5 release freshness = 100%**：v1.2.3（16 天內）、v2.2.13（今天剛發）、v1.3.4（10 天內）、python-1.15.0（昨天）、desktop-v0.5.18（昨天）。**這是本月 14:00 系列首次 5/5 release freshness 全中** — 08-20 是 1/5 = 20%（5 個裡 4 個有 release）、08-21 是 4/5 = 80%；今天的 100% 不是 baseline 而是 viral tail 撞上 fresh release 的雙重好運，5 天內 release 是常態但 100% 同時命中罕見。
- **License 乾淨度 5/5 = 100% permissive（4 MIT + 1 Apache-2.0）**：`block/buzz` 是 case-A2（Apache-2.0 含 patent grant clause），其餘 4 個 case-A（MIT）。這延續 08-17/20/21 的 3-consecutive 全 permissive milestone → **今天是 4-consec 5/5 permissive**。對主人 horo-agent / horo-webui air-gapped downstream 嵌入而言：4 個 tick 內 0 license friction，這是本月最強 sustained permissive streak。
- **3 套 install 範式 + 2 套 production framework install — 沒有任何兩個重複**：(1) `mattpocock/skills` 走 **Type-20 plugin marketplace + `npx skills@latest add`** 雙路徑（read-only Claude Code plugin vs. editable 拷貝）；(2) `mahlernim/google-timeline-visualizer` 走 **Type-4b2 多 platform installer matrix**（iPhone web app + Android side-load APK + Desktop `pip install -r requirements.txt`）；(3) `MoneyPrinterTurbo` 走 **Type-19 `uv sync --frozen` + Streamlit WebUI** + Docker Compose 預構鏡像雙路徑；(4) `microsoft/agent-framework` 走 **Type-1 + dotnet add package** 雙 SDK（pip + NuGet）；(5) `block/buzz` 走 **Hermit pinned toolchain + `just setup`**（git clone → activate-hermit → just build）+ Docker Compose + Railway 一鍵 deploy 三路徑。5 個 5 種裝法 — 對應 5 個 install 範式軸：**plugin marketplace / multi-OS side-load / uv+Streamlit / dual-Python-.NET SDK / Rust+Hermit monorepo**。Install-type 多樣性延續 08-21 5/5 milestone。
- **Hermes-as-official-agent-host signal：今日 0/5 直接命中，但 1 個 indirect 相鄰訊號**：今天 5 個 repo 的 README 都**沒有直接 mention Hermes**（跟前天 08-21 同樣）。但 `block/buzz` 的 architecture 提到 **ACP harness for Goose / Codex / Claude Code**（ACP = Agent Communication Protocol）— 這是 Hermes-as-host 的 **protocol-shape 相鄰訊號**，不是 direct hit。`mattpocock/skills` 的安裝矩陣列 Claude Code + Codex + 其他 agent，但 Hermes 不在列。**這延續 08-21 codification 的「Hermes-as-host 主流化趨勢但 explicit badge 沒回到 5-of-5」**：2-consecutive 14:00 tick（08-21 + 08-22）都沒 direct hit；3-consecutive-tick MEMORY peak（08-16/17/20）已是 1 個 tick 之前的歷史事件。值得留意的：Hermes 的 protocol 相鄰（ACP / MCP / skills marketplace）持續在 buzz / mattpocock/skills 出現，**主人 horo-agent downstream 的 protocol 互操作工作不是 0 需求**，但 explicit Hermes badge 在週末 viral tail 比較少見。

## Repo 摘要與 3W1H

### `mattpocock/skills`
- **Repo 摘要：** Matt Pocock（Total TypeScript 作者，230k★ 累計，今日 3,362★/day — viral 持續）的個人 coding agent skills library — 30 秒裝進 Claude Code / Codex / 其他 agent，定位「small, easy to adapt, composable」。Skill 對應 4 大失敗模式：溝通不良（`/grill-me` / `/grill-with-docs` 配合 CONTEXT.md shared language）、code 沒在跑（`/tdd` red-green-refactor + `/diagnosing-bugs` 階段化診斷 loop）、變成 big ball of mud（`/to-spec` + `/improve-codebase-architecture` 定期掃 codebase）、scope 太大（`/wayfinder` 把 multi-session 計畫切成 decision tickets）。
- **3W1H：**
  - **What：** 個人 curation 的 agent skills library — 30 秒裝、composable、可 fork、可 hack；不綁特定 framework，跟的任何 agent 都吃。
  - **Why：** 解決「agent 寫 code 不到位」的 4 大失效模式（misalignment / verbosity / no feedback / entropy）；mattpocock 個人 using 60,000+ 訂閱者會直接 receive updates，且每個 skill 是「decades of engineering experience」壓縮，不是 framework 強加的流程。**今日 3,362★/day 比昨日 2,192★/day 漲 53%**，作者電郵訂閱基數 + skills.sh 列表推廣雙引擎驅動 viral 持續（v1.2.3 release 才 16 天前）。
  - **Who：** 用 Claude Code / Codex 的 developer，要「specific failure-mode coverage」而不是「framework SDLC」；想用 Matt Pocock 自己常用的 skill 拼裝自己的 coding workflow；偏好「subscribe / fork」而不是「framework own your process」。
  - **How：** 兩路徑擇一，(a) Claude Code plugin (`claude plugins install mattpocock-skills` 或 session 內 `/plugin install mattpocock-skills`) 是 read-only 訂閱、自動更新；(b) `npx skills@latest add mattpocock/skills` 拷貝可編輯檔案進自己的 repo、`npx skills update` 拉新版。裝完每個 repo 跑一次 `/setup-matt-pocock-skills` 設定 issue tracker / labels / docs 位置。
- **安裝方式：**
  - **Claude Code plugin（read-only 訂閱）**：`claude plugins install mattpocock-skills`（CLI 一次性）或 session 內 `/plugin install mattpocock-skills` — 走官方 Claude Code plugin marketplace，read-only、作者 push 自動收到。
  - **npx skills@latest add（可編輯 fork）**：`npx skills@latest add mattpocock/skills` — 標準 agentskills.io install 路徑，自動偵測目前 agent host；對應 `npx skills update` 拉新版。可編輯。**兩種裝法只能選一個**，會重複。
  - **入內後**：每個 repo 跑一次 `/setup-matt-pocock-skills` 設定 issue tracker / labels / docs 位置（支援 GitHub / Linear / 本地檔案三選）。
- **近期 release：** `v1.2.3` — 2026-08-06 發佈（16 天內），pre-release = false；release name 與 tag 同名。Repo `pushed_at` 2026-08-21 昨日仍動，230,010★ 累計（比昨日 227k 又漲 3k）；377 open_issues 顯示活躍社區（多為 skill 改進 + 新增提案）。License = MIT（純 permissive），Homepage = `https://aihero.dev/skills`，description = 「Skills for Real Engineers. Straight from my .agents directory.」。**昨日（08-21）已抓過，今日為 1-day repeat — 但 trending rank 從昨天的 2 → 今天的 1，stars_today 漲 53%，值得追蹤 viral 持續。**

### `mahlernim/google-timeline-visualizer`
- **Repo 摘要：** 把 Google Maps Timeline (`Timeline.json`) 變成動畫旅行影片的 Android / iPhone / Desktop 三平台開源 app — Kotlin 寫 Android app、iPhone 走純 web app（無需安裝）、Desktop 走 Python + FFmpeg generator。**完全本地、無 Google 登入、無 location permission、無 analytics**，basemap 是唯一網路功能（CARTO + OpenStreetMap tile）；支援 `semanticSegments` 舊 export + raw location fallback、interpolate 大跨度航班沿 great-circle path、Conservative GPS outlier filter（保留原檔不變）、9 國 UI（含繁體中文）。**今日 v2.2.13 剛發**（台北時間今天上午 8:44 UTC 0:44），2,310★ 累計、MIT。
- **3W1H：**
  - **What：** Timeline → MP4 影片生成器 — Android (Kotlin)、iPhone web app (Safari 16.4+ for H.264)、Desktop Python；9 國 UI、1080×1080 square 或 1080×1920 portrait 或 1920×1080 landscape preset、10~300 秒 journey duration。
  - **Why：** 解決「Google Maps Timeline 是 JSON 沒人會讀、想看自己一年旅行軌跡」的需求；**隱私 100% 本地**（無 Google 登入、無 location permission、無 analytics、無廣域 storage 權限、Timeline JSON 不上傳）是獨門差異化。支援**加密備份 restore**（舊 trip 在換機後消失時，從 Google Maps encrypted backup 恢復 export）。今日 1,053★/day 主要是 Android-only 旅行愛好者 + iPhone Safari 用戶的 viral，剛好搭上 v2.2.13 release。
  - **Who：** 用 Google Maps Timeline 累積一年以上、想視覺化自己旅行的人；對隱私有堅持不願意把 Timeline 上傳任何服務；用 Android 8.0+ 跟 iPhone Safari 16.4+ 想離線處理的人。
  - **How：** Android 從 release page 抓 `TimelineVisualizer-*.apk`、開啟 sideload permission（一次、之後可關）；iPhone 直接 Safari 開 `https://ahn-lab.org/google-timeline-visualizer/`、選 `Timeline.json`、預覽 journey、Create MP4（Safari 保持前景）；Desktop 走 `pip install -r requirements.txt` + `python visualizer.py --input Timeline.json --year 2025 --camera-movement steady --output my_trip_2025.mp4`。
- **安裝方式：**
  - **iPhone web app（無 install）**：Safari 開 `https://ahn-lab.org/google-timeline-visualizer/`，無需 App Store；「Add to Home Screen」可保存為 PWA。**需 Safari 16.4+ 提供 H.264 encoding**。
  - **Android side-load APK**：從 [latest release](https://github.com/mahlernim/google-timeline-visualizer/releases/latest) `Assets` 下載 `TimelineVisualizer-*.apk`（不要下載 `.sha256` 檔）、開啟、安裝時允許瀏覽器/檔案管理員「Install unknown apps」權限。**需 Android 8.0+**。
  - **Desktop Python**：`python -m pip install -r requirements.txt`（Python 3.9+ + FFmpeg）→ `python visualizer.py --input Timeline.json --year 2025 --camera-movement steady --long-trip-compression balanced --output my_trip_2025.mp4`。
  - **Build / Test（開發者）**：JDK 17 + Android SDK Platform 36 + Build Tools 36.0.0；`./gradlew test lint assembleGithubDebug assemblePlayDebug` + `python -m pip install -r requirements-dev.txt` + `python -m pytest`。
- **近期 release：** `v2.2.13` — **2026-08-22 00:44 UTC 發佈（台北時間今天上午 8:44 剛發，~5 小時前）**，pre-release = false；release name = 「Timeline Visualizer 2.2.13」。Repo `pushed_at` 2026-08-22 06:00 UTC（台北時間今天下午 2:00 仍動），2,310★ 累計、269 forks、7 open_issues。License = MIT（純 permissive），Topics = `[]`（無 GitHub topics 標記，但 README badge 標 Android 8.0+、License、CI build status）。**首次進 Trending，今天是 0-day fresh pick**。

### `harry0703/MoneyPrinterTurbo`
- **Repo 摘要：** 一站式 AI 短影片生成工具 — 給主題或關鍵詞，自動寫腳本、選素材、配字幕、挑背景音樂、合成 HD 短影片（支援 YouTube Shorts / TikTok / Instagram Reels）。Python 3.11+ + Streamlit WebUI（127.0.0.1:8501）+ FastAPI docs（8080），WebUI 設定大模型 Provider、素材 API Key；Docker Compose 預構鏡像直接拉 `ghcr.io/harry0703/moneyprinterturbo:latest` 或 `uv sync --frozen` 從源碼跑。Kim K3 為主要助手引擎（火山引擎豆包、GLM-5.2、DeepSeek、Qwen、Claude、GPT-5、Gemini 等多家 LLM API 透過 `config.toml` 切換）。
- **3W1H：**
  - **What：** AI 短影片自動化工作流 — WebUI 主操作（瀏覽器 `127.0.0.1:8501`）+ CLI（`uv run python cli.py --video-subject "..."`）+ REST API（`/docs` Swagger）；支援自訂 LLM Provider、素材來源、字幕樣式。
  - **Why：** 解決「短影片內容產量追不上平台演算法要求」的痛點；一鍵產出腳本 → 素材匹配 → 字幕 → 背景音樂 → 合成，把原本 4-6 小時工作壓到 10 分鐘；**Kimi K3 贊助整合深度**（K3 不只寫腳本還精煉素材搜尋關鍵詞、決定成片畫面）。**今日 1,201★/day** + 111k★ 累計，主要是中文圈內容創作者 + 自媒體 + 帶貨博主社群；這是 5 天內第 4 次進 14:00 trending（08-18/19/20/22），結構性 viral 持續。
  - **Who：** 中文 YouTube / TikTok / 抖音 / Bilibili / Instagram Reels 內容創作者；想做「AI 自動化內容產線」但不想寫 code 的 content marketer；想用多 LLM API 切換素材來源的 seller。
  - **How：** Docker 一鍵：`docker compose -f docker-compose.release.yml up`（拉預構鏡像、`config.toml` 從 `config.example.toml` 拷貝）；源碼：`git clone` + `uv python install 3.11` + `uv sync --frozen` + `sh webui.sh`（macOS/Linux）或 `webui.bat`（Windows）；CLI mode：`uv run python cli.py --video-subject "..."`。
- **安裝方式：**
  - **Docker 一鍵（推薦）**：`git clone https://github.com/harry0703/MoneyPrinterTurbo.git && cd MoneyPrinterTurbo && cp config.example.toml config.toml && docker compose -f docker-compose.release.yml up` — 拉 `ghcr.io/harry0703/moneyprinterturbo:latest` 預構鏡像；瀏覽器開 `http://127.0.0.1:8501`（WebUI）+ `http://127.0.0.1:8080/docs`（API Swagger）。
  - **uv 從源碼**：`git clone` + `cd MoneyPrinterTurbo` + `uv python install 3.11` + `uv sync --frozen`（`pyproject.toml` 主要依賴、`uv.lock` 鎖檔）+ `sh webui.sh`（macOS/Linux）或 `webui.bat`（Windows）。LAN access：`MPT_WEBUI_HOST=0.0.0.0 sh webui.sh`。
  - **venv + pip（舊相容）**：`python3.11 -m venv .venv` + `source .venv/bin/activate` + `pip install -r requirements.txt`（`requirements.txt` 留給 pip 相容，`uv` 是推薦主路徑）。
  - **首次啟動**：自動從 `config.example.toml` 建 `config.toml`，可在 WebUI「基礎設置」配 LLM Provider 與素材來源 API Key。
- **近期 release：** `v1.3.4` — 2026-08-12 發佈（10 天前），pre-release = false；release name 與 tag 同名。Repo `pushed_at` 2026-08-20 昨日仍動，111,496★ 累計（昨日 108k → 今日 111k 又漲 3k）、16,890 forks、31 open_issues。License = MIT（純 permissive），Topics = `ai-video-generator` / `content-creation` / `ffmpeg` / `instagram-reels` / `llm` / `python` / `short-video` / `subtitles` / `text-to-speech` / `tiktok` / `video-automation` / `video-workflow` / `workflow-automation` / `youtube-shorts`。**5 天內第 4 次被本系列抓到（08-18/19/20/22），結構性 viral 持續**。

### `microsoft/agent-framework`
- **Repo 摘要：** Microsoft 官方 GA 級 multi-language AI agent framework — Python + C#/.NET + Go 三 SDK 一致 API，**production-grade AI agents + multi-agent workflows** 框架。整合 AutoGen + Semantic Kernel 後首個 stable runtime 層（2026-08-03 1.0 GA）；支援 graph-based workflow patterns（sequential / concurrent / handoff / group collaboration）+ checkpointing + streaming + human-in-the-loop + time-travel；內建 OpenTelemetry distributed tracing + middleware pipeline + 多 LLM provider（Microsoft Foundry / Azure OpenAI / OpenAI / GitHub Copilot SDK）。**昨日 `python-1.15.0` + 上週 `dotnet-1.18.0`** 雙 SDK 同步推進。
- **3W1H：**
  - **What：** Multi-language AI agent framework — Python + .NET + Go 一致 API；production-grade（durable / restartable / observable / governable）；Agent + Workflow + Hosting 三層架構。
  - **Why：** 解決「agent 從 prototype 推到 production 時缺乏 runtime 層」的痛點；AutoGen（實驗性、易壞）+ Semantic Kernel（.NET-centric、Python 弱）整合後第一個有 Microsoft 背書的 stable framework。**昨日 `python-1.15.0` + 08-18 `dotnet-1.18.0`** 雙 SDK 同步推進顯示開發節奏穩定；**Foundry Hosted Agents**（一行 deploy 到 Microsoft Foundry hosted infra）是新 2026-08 亮點。
  - **Who：** 在 Microsoft Foundry / Azure OpenAI 上跑 production agent 的 .NET + Python 團隊；想從 AutoGen 或 Semantic Kernel migrate 的既有客戶；需要 OpenTelemetry + governance + human-in-the-loop 的 enterprise team；不願 lock-in 單一 LLM provider、需要 provider flexibility 的架構師。
  - **How：** Python：`pip install agent-framework`（主套件 + 所有 sub-packages；首次 Windows install 需 1 分鐘）；.NET：`dotnet add package Microsoft.Agents.AI`（+ `Microsoft.Agents.AI.Foundry` + `Azure.AI.Projects` + `Azure.Identity`）。Quickstart：`from agent_framework import Agent; from agent_framework.foundry import FoundryChatClient; agent = Agent(client=FoundryChatClient(credential=AzureCliCredential()), name="HaikuAgent", instructions="..."); print(await agent.run("..."))`（需 `az login` 先）。
- **安裝方式：**
  - **pip**：`pip install agent-framework` — 裝所有 sub-packages（見 `python/packages/` 看 individual packages）；首次 Windows install 可能 1 分鐘。
  - **NuGet (.NET)**：`dotnet add package Microsoft.Agents.AI`（核心）；Foundry 整合：`dotnet add package Microsoft.Agents.AI.Foundry` + `dotnet add package Azure.AI.Projects` + `dotnet add package Azure.Identity`。
  - **Go SDK**：獨立 repo `microsoft/agent-framework-go`（不在本 repo）；docs / samples / contribution 都從那邊走。
  - **Auth setup**：`az login` 先（Azure CLI 認證），或用 API key（verify 資源/provider 對應）；production 推薦 `ManagedIdentityCredential` 而非 `DefaultAzureCredential`（避免 latency、unintended probing、fallback 風險）。
  - **DevUI（互動式開發 UI）**：`pip install agent-framework` 後內建；看 [DevUI 影片 demo](https://www.youtube.com/watch?v=mOAaGY4WPvc)。
- **近期 release：** `python-1.15.0` — **2026-08-21 23:08 UTC 發佈（台北時間昨天深夜）**，pre-release = false；release name 與 tag 同名。同週 2026-08-18 還有 `dotnet-1.18.0` 雙 SDK 同步。Repo `pushed_at` 2026-08-22 06:00 UTC（台北時間今天下午 2:00 仍動），13,034★ 累計、2,207 forks、593 open_issues（active enterprise-grade project）。License = MIT（純 permissive），Topics = `agent-framework` / `agentic-ai` / `agents` / `ai` / `dotnet` / `multi-agent` / `orchestration` / `python` / `sdk` / `workflows`，Homepage = `https://aka.ms/agent-framework`。

### `block/buzz`
- **Repo 摘要：** Block（Jack Dorsey 的 Square 公司）開源的 Nostr-based 自架協作平台 —「hive mind communication platform」，**人 + AI agent 共享同一個 room**，每則訊息 / reaction / workflow step / review approval / git event 都是 signed Nostr event，存在同一個 audit log。Desktop app（Tauri + React）+ Rust 後端 relay（Axum WebSocket + REST）+ Postgres（events + FTS search）+ Redis pub/sub + S3/MinIO（Blossom media）。**agent 是 first-class member**：`buzz-cli`（agent-first JSON in/out CLI）+ `buzz-acp`（ACP harness for Goose / Codex / Claude Code）+ `buzz-agent`（ACP agent）；NIP-34（git patches / repo announcements / status）原生整合，所以 PR / review / merge 都能變成可搜尋的 channel event。
- **3W1H：**
  - **What：** Self-hostable workspace（single-relay → community）+ Nostr relay + agent-first surface（ACP + CLI）；Desktop（Tauri+React）+ Relay（Rust+Axum）+ DB（Postgres+Redis+S3）三層。
  - **Why：** 解決「人 + agent 協作需要單一 substrate，不能用 chat + forge + bot + CI dashboard + release tool + search index + 一堆 glue code 七個 tab 假裝互通」的痛點；**agents 有自己的 keys、自己的 channel memberships、自己的 audit trail**，scope by identity 而非 permission flags，跟人類隊友一樣。**昨日 `desktop-v0.5.18` + 上週 08-18 `desktop-v0.5.17` + 08-15 `desktop-v0.5.14`** — 7 天內 3 個 desktop release，desktop 端節奏比 backend relay 快。29,352★ 累計、2,989 open_issues 顯示 huge 社群活躍度。
  - **Who：** 想 self-host 取代 Slack + GitHub Issues + Linear + CI dashboard 的 small-to-medium team；想把 AI agent 從「cron bot」升級到「first-class team member」並需要完整 audit trail 的 enterprise；對 Nostr protocol 有興趣、想把 git event 用 NIP-34 標準化的人；Block 員工（用 internal build，預連 Block relay）。
  - **How：** 一鍵 Railway deploy（[Deploy on Railway](https://railway.com/deploy/buzz-relay-block)）；本地 dev：Docker + Hermit（或 Rust 1.88+ + Node 24+ + pnpm 10+ + just）+ `git clone https://github.com/block/buzz.git && cd buzz && . ./bin/activate-hermit && just setup && just build` + `just dev`（relay + desktop 一起起）。Agent 用法：`BUZZ_PRIVATE_KEY` + `buzz-cli`（JSON in/out）。
- **安裝方式：**
  - **Railway 一鍵 deploy（推薦 hosted relay）**：[Deploy on Railway](https://railway.com/deploy/buzz-relay-block) — 不需管 server；細節見 [Block engineering blog](https://engineering.block.xyz/blog/run-your-own-buzz-relay)。
  - **本地 dev（Hermit pinned toolchain）**：`git clone https://github.com/block/buzz.git && cd buzz && . ./bin/activate-hermit`（首次自動下載 pinned tools）+ `just setup && just build` + `just dev`（relay + desktop 一起起）。Relay 跑 `ws://localhost:3000`、desktop app 自動 pop-up。
  - **生產部署（單節點/VPS）**：`deploy/compose/` 裡的 `docker compose` bundle（Postgres + Redis + MinIO + optional Caddy/TLS）；root `docker-compose.yml` 僅供 dev。`just relay`（一 terminal）+ `just desktop-dev`（另一 terminal）做 split-terminal workflow。
  - **Agent 整合**：`BUZZ_PRIVATE_KEY` + `buzz-cli`（agent-first JSON in / JSON out，給 LLM tool calls 用）；或 `buzz-acp`（ACP harness 給 Goose / Codex / Claude Code 用）。
  - **Windows**：agent shell tool 在 bash 下跑，需裝 [Git for Windows](https://git-scm.com/download/win) 帶 Git Bash；或設 `BUZZ_SHELL=C:\path\to\bash.exe` 指向其他 bash shell。
  - **Internal Block 員工**：用 `squareup/buzz-releases`（內部 build、預連 Block relay + agent provider）。
- **近期 release：** `desktop-v0.5.18` — **2026-08-21 17:19 UTC 發佈（台北時間昨天）**，pre-release = false；release name = 「Buzz Desktop v0.5.18」。同週 08-18 還有 `desktop-v0.5.17`、08-15 `desktop-v0.5.14` — **3 個 desktop release / 7 天內**，desktop 端節奏比 backend relay 快很多。Repo `pushed_at` 2026-08-22 06:00 UTC（台北時間今天下午 2:00 仍動），29,352★ 累計、3,717 forks、2,989 open_issues（huge 社群活躍）。License = Apache-2.0（**case-A2 — 含 patent grant clause**），Topics = `[]`（無 GitHub topics 標記），Homepage 無。**ACP harness for Goose / Codex / Claude Code 是 Hermes-as-host 的 protocol-shape 相鄰訊號**（主人 horo-agent 的 plugin 路線可借鏡 ACP 設計）。
