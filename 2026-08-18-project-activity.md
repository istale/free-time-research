# GitHub 專案動態

- 檢查時間：2026-08-18（台北時間 14:00 自由探索巡邏）
- 檢查對象：GitHub Trending Daily Top 3 + 2 個 web 探索挑選的新 AI 開源專案
- 來源組合：Trending（A）3 個、Web 探索（B）2 個，共 5 個 repo

## Repo 摘要與 3W1H

### `harry0703/MoneyPrinterTurbo`
- **Repo 摘要：** 一站式 AI 短影音生成工具。給主題或關鍵詞就能自動產腳本、配素材、生成字幕與背景音樂、合成 1080p 短影音；同時提供 WebUI、REST API 與 SKILL.md 入口讓 AI agent 可直接驅動。社群發行 Windows 一鍵包、macOS/Linux Docker 與 `uv` 本機部署，覆蓋 YouTube Shorts / TikTok / IG Reels 內容工作流。
- **3W1H：**
  - **What：** Python + Streamlit WebUI + FastAPI 服務；底層呼叫 LLM 寫腳本、Pexels/Pixabay 等素材 API、edge-tts/矽基/視覺模型配音，再用 ffmpeg 合成。
  - **Why：** 把「剪短影音」從剪輯師工作壓回可一鍵批次產出；對自媒體 / 短劇 / 行銷批量內容特別受用，因為發布頻率本身就是變現瓶頸。
  - **Who：** 一人短影音創作者、矩陣帳號運營、想做 AI 視訊 PoC 的工程師；對中文素材 + Kimi/豆包這類在地模型有相依加分。
  - **How：** clone → 複製 `config.example.toml` 成 `config.toml` → `uv` 啟動 WebUI（port 8501）與 API（port 8080），或在 `docker-compose.release.yml` 拉預建鏡像跑；亦可把 `docs/skill/SKILL.md` 餵給自家 agent。
- **安裝方式：**
  - **uv（推薦，macOS/Linux 本機）：** `git clone https://github.com/harry0703/MoneyPrinterTurbo.git`，再依 README 走 `uv` 環境；README 也明示「macOS / Linux 用戶優先使用 `uv` 進行本地部署」。
  - **Docker（跨平台、隔離）：** `docker compose -f docker-compose.release.yml up`，預設拉 `ghcr.io/harry0703/moneyprinterturbo:latest`。
  - **Windows 一鍵包：** 從 Releases 下載 zip，雙擊 `update.bat` 後雙擊 `start.bat`。
  - **AI agent 驅動：** 把 `https://raw.githubusercontent.com/harry0703/MoneyPrinterTurbo/main/docs/skill/SKILL.md` 丟給相容 agent。
  - **未找到明確 `pip install` 套件化發佈**（PyPI 上無此名）；以原始碼 / Docker / 一鍵包為主。
- **近期 release：**
  - 最新 release 為 `v1.3.4`，發佈時間 **2026-08-12**，標題 `v1.3.4`（正式版，非 prerelease）。
  - 連結：https://github.com/harry0703/MoneyPrinterTurbo/releases/tag/v1.3.4

### `usestrix/strix`
- **Repo 摘要：** 開源 AI 滲透測試 agent，模擬駭客動態跑應用程式找漏洞並產 PoC。重點是多 agent 編隊、可跑在 Docker sandbox、可被 Claude Code / Cursor / Codex 等 coding agent 透過 `npx skills add` 直接呼叫；同時提供託管的 `app.strix.ai` 平台做連續掃描與 auto-fix PR。
- **3W1H：**
  - **What：** Python CLI + Docker sandbox + 多套 SKILL.md（`penetration-testing-with-strix` 等）；底層串各家 LLM（OpenAI / Anthropic / Google）當滲透測試員的大腦。
  - **Why：** 傳統靜態掃描誤報多、人工滲透成本高；Strix 讓 agent 自己探、攻、驗、產 PoC，並在 CI/CD 每次 PR 阻擋，可縮短從「程式碼進庫」到「漏洞發現」的回饋環。
  - **Who：** AppSec 團隊、紅隊 / bug bounty 獵人、想把安全掃描接進 PR 流程的開發團隊。
  - **How：** `curl -sSL https://strix.ai/install | bash` 安裝 CLI → 設 `STRIX_LLM` + `LLM_API_KEY` → `strix --target ./app-directory`；或讓 coding agent 跑 `npx skills add usestrix/strix` 取得四支 skill。
- **安裝方式：**
  - **官方一鍵安裝（推薦）：** `curl -sSL https://strix.ai/install | bash`（會拉 sandbox Docker image）。
  - **PyPI（pip/uv）：** 套件名為 `strix-agent`（PyPI 最新版 `1.5.3`，上傳時間 2026-08-10）→ `pip install strix-agent` 或 `uv tool install strix-agent`；README badges 也指向 `pypi.org/project/strix-agent/`。
  - **AI agent skill（npx）：** `npx skills add usestrix/strix` 把四支 SKILL.md（penetration-testing / managed / fix / ci）裝進 Claude Code / Cursor / Codex。
  - **CI/CD：** README 內提供 GitHub Actions 範例（`Install Strix` + `Run Strix`）。
- **近期 release：**
  - 最新 release 為 `v1.5.3`，發佈時間 **2026-08-10**，標題 `v1.5.3`（正式版）。
  - 連結：https://github.com/usestrix/strix/releases/tag/v1.5.3

### `nautechsystems/nautilus_trader`
- **Repo 摘要：** 生產級 Rust 原生交易引擎，事件驅動、決定論時間模型，把研究、回測、實盤統一到同一條執行鏈；Python 是控制層（PyO3 binding），可全程用 Python 寫策略，也可整段用 Rust。整合 CEX / DEX / 外匯 / 期權 / Interactive Brokers / Betfair 等多種場域。
- **3W1H：**
  - **What：** Rust 核心（mimalloc + tokio）+ Python 控制面 + 模組化 adapters；現在是 v2 過渡期，README 標 v2 RC wheel、`develop_v1` 只收安全 backport。
  - **Why：** 多數團隊的研究用 Python 向量化、實盤又重寫一遍事件驅動程式碼，NautilusTrader 把這層鴻溝拿掉，research→live 一致語意，可直接拿來訓練 RL/ES trading agent。
  - **Who：** 量化研究員、做市 / 套利 / 跨市策略團隊、零售量化玩家、需要 RL 訓練環境的研究人員。
  - **How：** 建 venv 後 `pip install -U nautilus_trader`（或 `pip install -U nautilus_trader --pre` 試 v2 RC），要圖表加 `visualization` extra；亦可用自家 package index；想要極致效能就走 Rust crate。
- **安裝方式：**
  - **pip（推薦，PyPI）：** `pip install -U nautilus_trader`，想試 v2 RC 用 `pip install -U nautilus_trader --pre`；最新 PyPI 版 `1.231.0`，上傳時間 2026-08-02。
  - **Rust crate：** `crates.io` 上 `nautilus-core` 等 crates（MSRV 等於當下 stable Rust）。
  - **自家 package index：** README 列出 `packages.nautechsystems.io/simple/nautilus-trader/`（master / nightly / develop 三條分支）。
  - **Docker：** README 提到可部署 container（runtime image 內附）。
  - **Cargo 從源碼建：** README 提供 source build 段（Rust toolchain 1.97.1）。
- **近期 release：**
  - 最新 release 為 `v1.231.0`，發佈時間 **2026-08-02**，標題 `NautilusTrader 1.231.0 Beta`（正式版 tag，非 prerelease/draft）。
  - 連結：https://github.com/nautechsystems/nautilus_trader/releases/tag/v1.231.0

### `AMAP-ML/LongHorizon-Harness`
- **Repo 摘要：** 「Loop Engineering」式長時任務編排層：把 Claude Code / Codex / OpenCode / DeepSeek Harness 包成可跑數十小時的 computer-use 系統，提供 Manager / Executor / Auditor 三角色 + 驗證後 checkpoint + 失敗證據回收。已發 Hugging Face Daily Papers 週榜 #1（2026-W32）、arXiv 2608.01964。
- **3W1H：**
  - **What：** Python CLI（`lh-harness`）+ React/FastAPI Web 工作台 + npm 發的 computer-use plugins；定位是「agent 之外的 loop 編排層」，不重訓模型、不換掉既有 agent。
  - **Why：** LLM 單輪 context 容易滿、單一 agent 在 GUI/CLI 跨工具的長任務中會掉 context / 失憶 / 自我安慰完成；LongHorizon-Harness 用「fresh-context 執行 + 獨立 audit + durable verified state」把單輪能力變成長時能力。
  - **Who：** 已在用 Claude Code / Codex / OpenCode 的開發者與研究單位；想量化評估 computer-use（SWE-Bench / OSWorld / Terminal-Bench）的團隊；想跑跨桌面 + CLI 任務的自動化玩家。
  - **How：** 先裝 `uv`（或 pip）→ `uv tool install lh-harness` → 裝至少一個 agent CLI（`codex` / `claude` / `opencode` / `dsh`）→ 視需要 `lh-harness plugin install codex-computer-use` → 在瀏覽器跑 `lh-harness web` 或 CLI 直接 `lh-harness run "..." --agent codex`。
- **安裝方式：**
  - **uv（推薦）：** `uv tool install lh-harness`，升級用 `uv tool upgrade lh-harness`。
  - **pip：** `pip install lh-harness` / `pip install --upgrade lh-harness`。
  - **computer-use plugin（npm）：** `lh-harness plugin install codex-computer-use`（依所選 agent 而定）。
  - **環境體檢：** `lh-harness doctor`。
  - **Web UI：** `lh-harness web`（React + FastAPI）。
- **近期 release：**
  - 最新 release 為 `v0.1.6`，發佈時間 **2026-08-17**，標題 `v0.1.6`（正式版）；PyPI `lh-harness` 最新版 `0.1.6`，上傳時間 2026-08-17。
  - 連結：https://github.com/AMAP-ML/LongHorizon-Harness/releases/tag/v0.1.6

### `yetone/cumora`
- **Repo 摘要：** 「Agent teams 集散地」型跨平台團隊聊天 app：AI agent 與人類共用同一個 roster、DM、群組、看板、行事曆；支援兩種「腦」──Cumora Cloud（per-agent K8s pod + OpenAI Responses API）或 BYOA（用自家 Mac/VPS 跑 `npx cumora agent computer`，把本地 Claude Code / Codex 接到雲端）。強調多 agent 互不踩雷（seen-cursor freshness gate、atomic claim、triage gate）。
- **3W1H：**
  - **What：** Electron / PWA / iOS / Android 共用 React 18 + Vite + TS + Tailwind 前端 + Node + Express + ws 後端 + Postgres / Redis + Cloudflare Workers（email-gate、r2-gate）+ Go FUSE driver + npm `cumora` agent daemon。
  - **Why：** 現在 agent 多半是「1 個 CLI + 偶爾串 Slack」；Cumora 想把人與 agent 拉到同一個通訊層，並解決「兩隻 agent 同時搶同一件工作 / 互相覆蓋訊息」的協作衝突。
  - **Who：** 想把 AI agent 當真正同事編制進團隊流程的小團隊 / 內部 ops；研究 multi-agent coordination 的玩家；BYOA 重度 Claude Code / Codex 使用者。
  - **How：** 本機需 Postgres + Redis → `createdb -h localhost cumora` + `export OPENAI_API_KEY=...` → `npm install` → `npm run dev:all`（Vite :5180 + API :5181）→ 開 `http://localhost:5180`；BYOA 模式另跑 `npx cumora agent computer`。
- **安裝方式：**
  - **npm（後端 + renderer 全棧 dev）：** `npm install`（需本機 Postgres / Redis）+ `npm run dev:all`。
  - **npm（BYOA daemon）：** `npx cumora agent computer`（套件名 `cumora`，npm latest `0.1.127`，發佈時間 2026-07-30）。
  - **桌面殼：** `npm run electron:dev`（auto-update 走 `yetone/cumora-releases`）。
  - **行動殼：** `ios/`、`android/` 用 Capacitor（package id `io.cumora.app`）。
  - **雲端：** Cumora Cloud 由 K8s + Cloudflare Workers 託管，使用者不需安裝 backend。
- **近期 release：**
  - **未找到 GitHub release**（repo 建立於 2026-08-17，仍在「首週」開發期；發佈走另一個 `yetone/cumora-releases` repo 的 desktop auto-update 通道）。
  - 替代活躍度證據（一句話）：`pushed_at` 為 2026-08-18（今天），主分支仍持續推送；npm `cumora@0.1.127` 7 月底上線。

## 重點觀察
- **release 新鮮度**：5 個 repo 都非常「今日」──Strix（08-10）、MoneyPrinterTurbo（08-12）、LongHorizon-Harness（08-17）都在最近一週內；nautilus_trader 雖 08-02，但 Beta 標記持續推進 v2 RC。cumora 尚未開 GitHub release（用獨立 `cumora-releases` repo 做 desktop 發佈），主分支今日仍有 push。
- **套件化成熟度**：4/5 走正式 PyPI／npm──Strix（`strix-agent` on PyPI）、LongHorizon-Harness（`lh-harness` on PyPI）、nautilus_trader（`nautilus_trader` on PyPI + 自家 index）、cumora（`cumora` on npm）；MoneyPrinterTurbo 唯一未套件化，僅以原始碼 / Docker / 一鍵包交付。意味前 4 個都可用 `pip install` 或 `npx` 五分鐘內起步。
- **生態跨度**：3 個 Python 主導（MoneyPrinterTurbo / Strix / LongHorizon-Harness），1 個 Rust + Python 雙綁定（nautilus_trader），1 個 TypeScript / Node 全棧（cumora）。Python 在 agent / 內容自動化仍是最大公約數，TypeScript 開始在「agent 編隊 + 多人協作 UI」場景佔位。
- **上手門檻**：MoneyPrinterTurbo 最低（瀏覽器 + 一鍵包），Strix / cumora 中等（要 Docker / Postgres / Redis），LongHorizon-Harness 最高（要先備好 `codex` / `claude` / `opencode` / `dsh` 其中之一、Node 20+、再裝 computer-use plugin）──但後者也是唯一強調「production-grade 跨日任務」完整管線的。
- **AI agent 互操作是這批新訊號**：Strix 把自己做成「coding agent 可呼叫的 skill」（`npx skills add`）、cumora 讓 BYOA 模式把本地 Claude Code / Codex 直接掛進雲端 app、LongHorizon-Harness 把四種 agent CLI 當 backend 共用同一個 loop──這條「agent 之上的編排層」是這個月的新軸線，與傳統單純做「另一個 LLM 框架」不同。