# GitHub 專案動態
- 檢查時間：2026-08-02
- 檢查對象：microsoft/AI-For-Beginners、paperswithbacktest/awesome-systematic-trading、usekaneo/kaneo、sqliteai/waste、QwenAudio/qwen-audio-agent
- 來源：Trending Daily Top 3（https://github.com/trending?since=daily）+ 自由探索 Web Search（GitHub Search API by created:2026-07-26..2026-08-02, stars>200, sort=stars desc；web-search 工具不可用，改以 GitHub Search API 驗證）

## Repo 摘要與 3W1H

### microsoft/AI-For-Beginners
- **Repo 摘要：** `microsoft/AI-For-Beginners` 是 Microsoft 維護的 12 週、24 課 AI 入門課程，內容從基礎機器學習、神經網路、CNN、RNN、NLP、GAN 到可執行的 PyTorch / TensorFlow Jupyter Notebook。每課通常包含預讀材料、Notebook、部分主題的 lab 與 Microsoft Learn 延伸教材；README 也提供 50 多種語言翻譯。它適合初學者、教師與想用實作建立 AI 基礎的學習者，不是用來直接部署模型的 library。
- **3W1H：**
  - **What：** 一套開源、跨語言翻譯的 AI 教學 curriculum，搭配 Notebook、範例、quiz 與 labs。
  - **Why：** 把 AI 概念拆成可循序完成的 24 課，並用「Hello AI World、簡單神經網路、影像分類、文字情緒分析」等 beginner-friendly examples 降低第一次接觸的門檻；今天登上 Trending，主要反映教學資源在社群中的持續需求，而非新版本發布。
  - **Who：** AI / machine learning 初學者、教師、課程設計者，以及希望用 VS Code 或 Codespaces 跑實作的開發者。
  - **How：** 先看 `lessons/0-course-setup` 的環境設定，再以 GitHub Codespaces / VS Code 或本機 Notebook 逐課閱讀與執行；可先從 `examples/README.md` 的四個入門範例開始，再進完整課程。
- **安裝方式：** **pip/uv/poetry：** 未找到單一套件安裝方式；這是課程 repo。若要在本機執行 Notebook，可先 `git clone https://github.com/microsoft/AI-For-Beginners.git`，再依 setup lesson 建立 Python 環境並安裝 `requirements.txt`：`python -m pip install -r requirements.txt`（或在虛擬環境中使用 `uv pip install -r requirements.txt`）。README 另提供 sparse checkout：`git clone --filter=blob:none --sparse https://github.com/microsoft/AI-For-Beginners.git`，可避免一次下載所有翻譯。
  - **npm/pnpm/yarn/npx：** 未找到明確方式；quiz app 可依 repo 內說明另行本機執行或部署。
- **Repo metadata：** Description `12 Weeks, 24 Lessons, AI for All!`；Stars **57,742**；Forks 11,405；Language **Jupyter Notebook**；Topics `ai`、`artificial-intelligence`、`cnn`、`computer-vision`、`deep-learning`、`gan`、`machine-learning`、`nlp` 等；License **MIT**；Created 2021-03-03；Updated **2026-08-02 06:01:38 UTC**；Pushed 2026-07-21；Default branch `main`。
- **近期 release：** **未找到 GitHub release**（`/releases/latest` 回傳 404）。這個 repo 的交付形態是課程內容、翻譯與 Notebook，不能把 Trending 熱度誤讀成近期版本號。

### paperswithbacktest/awesome-systematic-trading
- **Repo 摘要：** `awesome-systematic-trading` 是系統化交易資源索引，整理 libraries、packages、策略、書籍、部落格、教學、backtesting / live trading frameworks、broker APIs、資料來源、風險與最佳化工具。它本質上是 curated list，不是可以 `pip install` 後直接運行的交易系統；價值在於替量化研究者縮短工具與教材的 discovery 時間。今天位居 Trending Top 3，但 GitHub API 顯示最後 push 為 2025-01-22，故「熱門」與「近期維護」必須分開看。
- **3W1H：**
  - **What：** 面向 algorithmic / quantitative trading 的 awesome-list，依 backtesting、live trading、broker、data、analytics、risk、pricing 等類別分組。
  - **Why：** 量化交易工具分散在不同語言與資料服務，索引把候選 framework、資料源與學習材料放到同一個入口；近期被 Trending 看見不代表索引本身剛更新，也不代表其中每項工具都經過本 repo 驗證。
  - **Who：** 量化研究者、systematic trader、金融工程學生、想比較回測框架與 broker API 的工程師。
  - **How：** 直接瀏覽 README 的分類，從 Backtesting and Live Trading、Trading bots、Analytics、Broker APIs、Data Sources 等段落挑選外部工具，再自行閱讀各專案授權、文件與維護狀態。
- **安裝方式：** **pip/uv/poetry：** 未找到明確 pip 安裝方式；repo 是資源清單，不是 Python package。可直接 `git clone https://github.com/paperswithbacktest/awesome-systematic-trading.git` 後閱讀 README，再對清單中的個別 library 依其官方文件安裝。
  - **npm/pnpm/yarn/npx：** 未找到明確方式。
- **Repo metadata：** Description `A curated list of awesome libraries, packages, strategies, books, blogs, tutorials for systematic trading.`；Stars **12,356**；Forks（本次 metadata 未另作展示）；Language **Python**（GitHub 的主語言標記，內容主要為 Markdown 資源清單）；Topics `algorithmic-trading`、`algotrading`、`awesome-list`、`quantitative-finance`、`quantitative-trading`、`trading-strategies` 等；License **None**（API license 為 null，且 repo 根目錄 `LICENSE` 內容端點回傳 404；引用或再散布前應向 owner 確認）；Created 2022-02-05；Updated **2026-08-02 06:01:53 UTC**；Pushed **2025-01-22 07:49:32 UTC**；Default branch `master`。
- **近期 release：** **未找到 GitHub release**。同時最後 push 距今已久，這份報告不使用 commits / issues 取代 release；只提醒讀者，清單項目的新舊與本索引 repo 的更新節奏是兩件事。

### usekaneo/kaneo
- **Repo 摘要：** `Kaneo` 是 MIT 授權、可自架的開源專案管理工具，定位在 Jira / Linear 替代品，提供 issue tracking、Kanban、專案與團隊工作流。README 給了 drim 一鍵部署、Docker Compose + PostgreSQL、Kubernetes Helm chart，以及 pnpm workspace 的本機開發路徑，因此它比單純前端 demo 更接近可部署的完整 app。適合希望掌握資料與部署環境、又不想承受大型商用 PM 系統複雜度的小團隊。
- **3W1H：**
  - **What：** TypeScript / React 生態的 self-hosted project-management app，涵蓋 issue tracker、Kanban 與團隊協作。
  - **Why：** 以「只提供需要的功能」對抗 Jira / Linear 類工具的重量，並同時提供 Docker、one-click deployment、獨立 API / web image 與 Helm chart，讓試用和正式部署有不同入口。
  - **Who：** 小型工程團隊、偏好 self-host 的組織、需要 issue / Kanban 管理但不想把資料交給 SaaS 的使用者，以及想參與 TypeScript monorepo 開發的人。
  - **How：** 最短試用路徑是安裝 drim：`curl -fsSL https://assets.kaneo.app/install.sh | sh` → `drim setup`；也可準備 `.env` 後用 Docker Compose 啟動 Kaneo 與 PostgreSQL。開發者則 clone repo、`pnpm install`、設定環境變數，再 `pnpm dev`。
- **安裝方式：** **npm/pnpm/yarn/npx：** repo 本身不是以公開 npm package 作為終端使用入口；從 source 開發使用 `git clone https://github.com/usekaneo/kaneo.git` → `cd kaneo` → **`pnpm install`** → `pnpm dev`。**Docker Compose：** 依 README 的 `compose.yml` 使用 `ghcr.io/usekaneo/kaneo:latest` 與 `postgres:16-alpine`，準備 `.env`、`POSTGRES_PASSWORD`、`AUTH_SECRET` 後執行 `docker compose up -d`。**一鍵部署：** `curl -fsSL https://assets.kaneo.app/install.sh | sh` → `drim setup`；這是部署工具安裝，不是 pip/npm package。
  - **pip/uv/poetry：** 未找到明確方式。
- **Repo metadata：** Description `🎯 All you need. Nothing you don't. Open source project management that works for you, not against you.`；Stars **5,771**；Forks（API metadata 未在本次輸出另列）；Language **TypeScript**；Topics `hacktoberfest`、`hono`、`issue-tracker`、`jira-alternative`、`kanban`、`linear-alternative`、`project-management`、`self-hosted` 等；License **MIT**；Created 2024-12-31；Updated **2026-08-02 05:56:09 UTC**；Pushed **2026-08-02 01:27:25 UTC**；Default branch `main`。
- **近期 release：** **2026-07-30 20:03:50 UTC**；`v2.12.1`；**Release v2.12.1**。這是正式 stable release，release note 主要包含 comparison / alternative pages 的功能更新。

### sqliteai/waste
- **Repo 摘要：** `WASTE`（Weight-Aware Streaming Tensor Engine）是用 C 寫的、可嵌入且無第三方 runtime dependency 的 inference engine，針對 mixture-of-experts 模型把大部分不會在當下 token 被啟用的 weights 留在 NVMe，只把 resident trunk 放進 RAM、按 router 選出的 experts 串流進 bounded cache。它目前的展示對象是完整的 2.78T Kimi K3：README 報告在 64 GB MacBook Pro 上以約 0.45–0.62 tok/s 執行，代價是約 982 GiB model container 與高速內接 NVMe。這是極低門檻的程式碼安裝、極高硬體與資料準備門檻的典型。
- **3W1H：**
  - **What：** C11 inference engine / embeddable library，提供 `waste` CLI 與 `libwaste.a`，把 MoE expert weights 依需從磁碟串流，並支援 session、chat、eval 與 model inspection。
  - **Why：** 把「模型大到一般消費級 RAM 放不下」轉成「只要磁碟可及時讀取，就能在本機執行」；價值也在資料不必送到 cloud API。README 同時明確承認速度只有約半 token/s，且把測得、失敗與尚未證明的邊界寫出來，適合當作工程 feasibility proof 而不是現成高吞吐服務。
  - **Who：** 想在本機研究超大 MoE / Kimi K3、需要離線或資料不出機的 inference 工程師、C library 整合者，以及有約 1 TB 內接 NVMe 和 64 GB RAM 的硬體使用者。
  - **How：** 先用 C11 compiler 與 `make` 建出 binary；模型準備流程是 `tools/fetch_weights.sh --dry-run` → 可恢復下載 → `uv run --with torch --with safetensors python tools/convert.py ...` 轉成 `.waste` container；執行則為 `waste run ~/models/k3.waste "prompt"`，也可 `waste chat` / `waste eval`。K3 轉換約需 1 TB 目標空間及額外 staging disk；想先試可選 19 GB 的 Kimi-Linear 48B container。
- **安裝方式：** **pip/uv/poetry：** inference runtime 沒有 pip package；README 明確是 C11 compiler + `make`，且執行期不需要 Python、BLAS 或 CUDA。模型轉換階段才使用 **`uv`**：`uv run --with torch --with safetensors python tools/convert.py --src /Volumes/staging/k3 --out ~/models/k3.waste --jobs 3`。**From source：** `git clone https://github.com/sqliteai/waste.git` → `make`；產出 `waste` binary 與 `libwaste.a`。沒有 npm/pnpm/yarn/npx 安裝方式。
- **Repo metadata：** Description `Run the full 2.78-trillion-parameter Kimi K3 model beyond available RAM by streaming activated weights directly from NVMe. A dependency-free, embeddable C inference engine.`；Stars **720**；Forks **70**；Language **C**；Topics 空；License **Apache-2.0**；Created 2026-07-28；Updated **2026-08-02 05:54:07 UTC**；Pushed **2026-08-02 05:26:53 UTC**；Homepage `https://sqlite.ai`；Default branch `main`。
- **近期 release：** **2026-08-01 12:42:26 UTC**；`v0.6.2`；**v0.6.2**。Release note 的重點是修正 x86 build 上 `waste info` / `waste run` 對 K3 會 crash 的問題；這是本日五個 repo 中相對最新的一筆正式 release。

### QwenAudio/qwen-audio-agent
- **Repo 摘要：** `qwen-audio-agent` 是以 Qwen Audio 3.0 Realtime 為前台、可接多種 coding / agent backend 的即時語音前端與 runtime：它提供 CLI、TUI、Gateway、macOS 桌面懸浮球，讓使用者可持續說話、在背景執行任務、再回來接續對話。README 列出 CodeBuddy、Codex、Claude Code 等 ACP 整合，並支援 macOS 的全雙工回聲消除與 Linux / Windows 的音訊模式。它適合想把 coding agent 從文字終端延伸到常駐語音介面的使用者，但需要 DashScope API Key，且後台 `full` 權限應只在可信專案使用。
- **3W1H：**
  - **What：** JavaScript / Node.js 的 realtime voice agent runtime + CLI / TUI / macOS desktop app，npm package 名稱為 `qwen-audio-agent`，CLI binary 為 `qwenaudio`。
  - **Why：** 把「語音對話」與「agent 仍在工作」接在一起：前台保持可說話，後台 agent 可處理任務；同時提供本機 user profile、memory、tasks 狀態，並以 Gateway 管理常駐服務。近期新 release 也加入 desktop auto-update、backend agent availability 與 realtime provider protocol 拆分。
  - **Who：** 想用語音操作 coding agent 的開發者、需要常駐個人助理的 power user，以及要測試 Qwen realtime voice + Codex / Claude Code / ACP backend 串接的人。
  - **How：** 先準備 Node.js **22.22.2+ 或 24.15.0+**、npm 10+ 與 DashScope API Key，再 `npm install -g qwen-audio-agent`；設定 `config.env` 後執行 `qwenaudio` / `qwenaudio tui`。要長駐可執行 `qwenaudio gateway install`；macOS 桌面版則從 release 下載 `.dmg`，或用 source 的 `npm run desktop:build:local`。
- **安裝方式：** **npm/pnpm/yarn/npx：** **`npm install -g qwen-audio-agent`**（README 標示推薦）；也可 **`npm install -g git+https://github.com/QwenAudio/qwen-audio-agent.git`** 安裝 GitHub 最新程式。從 source：`git clone https://github.com/QwenAudio/qwen-audio-agent.git` → `cd qwen-audio-agent` → `npm install` → `npm run install:global`。升級 registry 版使用 `npm install -g qwen-audio-agent@latest`。**Desktop：** 從 release 下載 `.dmg`；**pip/uv/poetry：** 未找到方式。
- **Repo metadata：** Description `A realtime voice runtime that keeps Agents talking, working, and present. Real-time Voice Runtime for AI Agents`；Stars **1,479**；Forks（本次 metadata 未另作展示）；Language **JavaScript**；Topics `agent`、`agentic-ai`、`voice-agent`、`voice-ai`、`voice-chat`；License **Apache-2.0**；Created 2026-07-27；Updated **2026-08-02 06:01:17 UTC**；Pushed **2026-08-02 05:13:09 UTC**；Default branch `main`。
- **近期 release：** **2026-08-01 08:36:17 UTC**；`v1.2.0`；**v1.2.0**。Release note 包含 npm 11+ `pack --json` 相容性修正、realtime provider protocol 拆分、backend agent availability，以及 macOS desktop auto-update / startup stall 修正。

## 重點觀察
- **Trending 的三個位置呈現兩種完全不同的訊號。** `microsoft/AI-For-Beginners` 與 `paperswithbacktest/awesome-systematic-trading` 都沒有 GitHub release；前者最後 push 尚在 2026-07-21，後者則停在 2025-01-22。`usekaneo/kaneo` 的 `v2.12.1`（2026-07-30）才是 Top 3 中同時具備近期正式版本與今日分支活動的 app；不可把 Trending 排名直接當作 release 新鮮度。
- **今日兩個自由探索 repo 都是「近期建立 + 有正式版本」的工程型專案。** `sqliteai/waste` 在 2026-08-01 發 `v0.6.2`，把 K3 的本機 NVMe streaming feasibility 做成 C engine；`QwenAudio/qwen-audio-agent` 在 2026-08-01 發 `v1.2.0`，走 npm 套件化與 macOS desktop / voice runtime。兩者都比單純 README 熱門更接近可實際試用的近期交付。
- **安裝生態分布很清楚：TypeScript / JavaScript 兩個 repo 有 npm/pnpm 路徑，C repo 走 `make`，教學與清單型 repo 走 clone + requirements 或直接閱讀。** 五個 repo 中，明確提供正式 **npm install** 的只有 `QwenAudio/qwen-audio-agent`；Kaneo 與 qm 的 pnpm / npm 命令主要服務 source development 或 deploy bootstrap，而不是把完整 app 當成一般 library 安裝。
- **上手門檻的差距比語言標籤更大。** AI-For-Beginners 適合從 Codespaces / Notebook 開始；Kaneo 可用 drim 或 Docker Compose 快速自架；Qwen Audio Agent 只需 Node 與 DashScope key 便可啟動，但會把語音與背景 agent 權限帶入本機；WASTE 雖然 runtime zero-dependency，實際 K3 路徑卻要約 1 TB container、64 GB RAM 與內接 NVMe，屬「編譯容易、資料與硬體昂貴」。
- **共同趨勢是把 AI 能力嵌入既有工作介面，而非只發布模型本身。** Kaneo 把協作流程做成 self-hosted app，WASTE 把大 MoE 推理搬到本機硬體，Qwen Audio Agent 把 agent 接到語音與常駐桌面；另一方面，兩個 Trending 清單型專案提醒咱：內容索引與教學本身仍能成為高流量入口，但必須額外核對授權、最後 push 與 release，才知道是否值得立刻投入。
