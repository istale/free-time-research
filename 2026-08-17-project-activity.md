# GitHub 專案動態
- 檢查時間：2026-08-17
- 檢查對象：GitHub Daily Trending top 3 + 自由探索 2 個（共 5 個 repo）

## Repo 摘要與 3W1H

### `cordiverse/cordis`
- **Repo 摘要：** 與昨日（08-16）同一檔案重複（cordis 連續兩天坐穩 Trending #1），TypeScript meta-framework「Of Spatiotemporal Composability」—— Service Injection / plugin 架構 / context lifecycle 抽象，整個 monorepo（`packages/core` + `packages/loader` + `packages/include`）透過 Effect + Node + Browser / 多 island 共用同一套 context。今日 `stars` 從昨日 ~5,076 跳到 → 仍約 **5,400** 級距、`pushed_at` 維持 2026-08-13、未新增任何 GitHub release（**npm 才是主要出貨管道**）。值得關注的是，08-14 `deepseek-ai/deepseek-harness` 將 cordis 列入底層 plugin 組合器後，這個 framework 在 agent / harness 後端被「引用而非 fork」的路徑已被 DeepSeek 走過一遍。
- **3W1H：**
  - **What：** TypeScript monorepo（`packages/core` / `packages/loader` / `packages/include`），runtime + context + plugin 三件式抽象，搭配 Effect-friendly 的 dependency injection；peer deps `@cordisjs/plugin-include` + `@cordisjs/plugin-loader` 為 optional。
  - **Why：** React / Vue 解決了 UI 渲染，但「跨 runtime / 跨 process 共享同一份 service / context」始終沒標準；cordis 把 plugin 抽象、service 依賴、isolation 邊界寫成可組合 framework，是 mono-agent / multi-agent / island architecture 的共同底層（DeepSeek 已驗證）。
  - **Who：** 寫多 island / multi-tenant SaaS / multi-agent backend / 同時要跑 Node + browser + Edge 的工程師；以及在 Koishi / Satori / Mastodon-like bot 框架上做 plugin 開發的人。
  - **How：** `npm install cordis`（peer deps 為 optional plugin、未 stable API）；CLI 入口 `node_modules/.bin/cordis`（package.json `bin` 指向 `bin.js`）；自家 build 走 `yarn install` → `yarn build`（`yarn yakumo esbuild && yarn yakumo tsc`，鎖 `yarn@4.14.1`）。
- **安裝方式：**
  - **npm：`npm install cordis`**（peer deps 為 optional，`dist-tags.latest = 4.0.0-rc.8`，npm 為主要出貨管道）。
  - **yarn（monorepo-contributors）：** `yarn install`（`.yarnrc.yml` 鎖 `yarn@4.14.1`，`packageManager: yarn@4.14.1`）。
  - **CLI bootstrap（自家開發）：** `git clone https://github.com/cordiverse/cordis.git` → `yarn install` → `yarn build`（`yarn yakumo esbuild && yarn yakumo tsc`）。
  - **CLI 入口：** `node_modules/.bin/cordis`（package.json `bin` 指向 `bin.js`）。
  - **文件：** 主站 `https://deepseek-harness.github.io/deepseek-harness/reference/cordis-primer`（README 唯一列出），論文 `cordiverse/paper` 為 spec source。
- **近期 release：** 未找到 GitHub release（npm 為主要出貨管道；當前 npm `dist-tags.latest = 4.0.0-rc.8`，發佈於 **2026-08-10**；`pushed_at` 為 2026-08-13，README 明確自標「API is not yet stable and may change without notice」，目前仍是 4.0 RC 階段）。**08-17 = 08-16 的 follow-up**：連續兩日 Top 1 顯示 Trending 算法的 owner reputation / fresh impact 加權明顯（cordiverse 8/13 才 pushed，這種 rank-durability 很罕見）。

### `basecamp/omarchy`
- **Repo 摘要：** DHH（37signals / Ruby on Rails 作者）親自發起的「Beautiful, Modern & Opinionated Linux」發行版 —— Arch Linux 為基底 + Hyprland tiling Wayland compositor + 全套 opinionated 工具鏈（Neovim、AI、shell plugins、Omarchy CLI）。Repository 本身就是系統本體 + `manual/` 完整手冊（51 章節，從安裝到 dual-boot 到 unattended install），授權 **MIT**、default branch `quattro`（**注意：不是 main**）、`stars` 達 **25,585**、`pushed_at` 2026-08-16 = 仍在持續整合。**08-17 Trending #2 顯示 DHH 在社群內的號召力**：發布後隨即衝到第二，README 一句話直奔 omarchy.org，所有細節都在 `manual/` 而非 README。
- **3W1H：**
  - **What：** Arch-based Linux distribution（185MB repo size = 包含完整 image / config / manual）+ Hyprland + Wayland + 自家 `omarchy` CLI，主打「意見統一 + 美學優先」。
  - **Why：** 一般 Linux distro 要使用者裝完後花數週自己拼 tiling compositor / dotfiles / shell prompt；omarchy 把整套 DHH 心法（Hyprland + Tokyo Night + JetBrains Mono + Waybar + walker launcher + Ghostty terminal）打包成「開機即用」的一次性體驗，呼應他在 HEY / Basecamp 推行的「opinionated software」哲學。
  - **Who：** 想立刻有「可工作的 Linux 工作站」的開發者、Ruby / Rails / 設計師社群（37signals 自家用戶基本盤）、喜歡 Hyprland 但不想自己寫 rice 的人。
  - **How：** **沒有 pip / npm 安裝方式**（整包是 OS image）：從 [omarchy.org](https://omarchy.org) 下載 ISO 或在現有 Arch 跑官方 installer script；後續透過 `omarchy` CLI 更新（見 `manual/14-omarchy-cli.md`、`manual/30-updates.md`）；manual 提供 dotfiles / theme / monitor 設定檔的存放位置（`~/.config/omarchy/`、`~/.local/share/omarchy/`）。
- **安裝方式：**
  - **官方 ISO（推薦）：** 從 [omarchy.org](https://omarchy.org) 下載最新 ISO，直接 boot 進 live installer。
  - **Arch 既有系統轉換：** 跑官方 install script（README 指向 omarchy.org，細節在 `manual/02-getting-started.md`）。
  - **Omarchy CLI（已安裝後）：** `omarchy update` / `omarchy theme` / `omarchy config`（見 `manual/14-omarchy-cli.md`）。
  - **手動 fork build：** `git clone https://github.com/basecamp/omarchy` → 讀 `manual/` 對應章節；無 build.sh / Makefile，整包 release 是 image，不是 source build。
  - **沒有 `pip install` / `npm install` / `brew install`** —— 這是 OS distribution，不能裝在 macOS / Windows 上（除非走 VM / dual-boot，見 `manual/28-windows-vm.md` + `manual/51-dual-boot-install.md`）。
  - **手冊來源：** `manual/` 51 章節為 canonical source，網頁鏡像 `https://learn.omacom.io/2/the-omarchy-manual`（含 screenshot）。
- **近期 release：** 最新 release 為 **`v4.0.0`**，發佈於 **2026-08-14**（published_at 2026-08-14T16:35:40Z），非 prerelease；前一個為 v3.8.4 (2026-07-21) → v3.8.3 (2026-07-13) → v3.8.2 (2026-05-24) → v3.8.1 (2026-05-14)。**節奏為 1.5-3 週 / 一版**，v4.0.0 為重大版本（從 `default_branch = quattro` 推測為新版 compositor / desktop stack 切換）。注意：release 名稱都叫 `v4.0.0`（name 跟 tag 一致），這是 OS distro 風格；前綴 `v` 一致，命名空間清楚。

### `unslothai/unsloth`
- **Repo 摘要：** 不只是 Python 套件，已進化成「**第一個真正的 desktop app** to run and train models」（README 開頭寫明）—— 跨 Windows / macOS / Linux + Ubuntu deb / AppImage / ARM64 全部有 native binary + 新出的 Unsloth Desktop（v0.1.701+）+ Unsloth Start（`unsloth start hermes` / `claude` / `codex` / `openclaw` 一行把本地 model 接到 agent）。支援 Qwen3.8 / Kimi K3 / MiniMax-H3 / Gemma 4 / DeepSeek-V4 / FLUX / Muse Glimmer，**`stars` 達 72,793**、size 202MB、1,266 open issues、`pushed_at` 2026-08-17 = 仍在 daily 迭代。今日 Trending #3 表示 Unsloth 已從「LoRA 微調神器」轉型為「local-first AI desktop OS」。
- **3W1H：**
  - **What：** Python library + native desktop app（Windows .exe / macOS .dmg / Ubuntu .deb / Linux AppImage / ARM64 .app.tar.gz），核心是 2× faster LoRA/QLoRA 訓練 + 70% VRAM 節省 + Unsloth Desktop（GUI）+ Unsloth Start（agent ↔ local model 橋接）。
  - **Why：** LM Studio / Ollama 解決「跑模型」，Unsloth 同時解決「跑 + 訓練 + 接到 agent」三件事；用戶不用自己接 vLLM + OpenAI API + Claude Code 設定 base_url，單一 `unsloth start hermes` 把 desktop 開成 OpenAI 相容 endpoint。
  - **Who：** 想在本地跑 frontier model 又想接 Claude Code / Codex / Hermes Agent 的開發者；做 LoRA / QLoRA / RL（DPO/GRPO）微調的 researcher；注重隱私的 enterprise（in-house 私有模型 self-host）。
  - **How：** 下載 native installer 一行裝；或 `curl -fsSL https://unsloth.ai/install.sh | sh`（macOS/Linux/WSL）；或 Windows `irm https://unsloth.ai/install.ps1 | iex`；裝完 `unsloth start hermes` 把本地模型接到 Hermes Agent。
- **安裝方式：**
  - **Native desktop（推薦）：** GitHub Releases 直接抓平台 binary（`Unsloth-Desktop-0_1_800_beta-Windows.exe` / `-MacOS.dmg` / `-Ubuntu.deb` / `-Linux.AppImage` / `-ARM64.app.tar.gz`），每個平台都是單檔安裝。
  - **macOS / Linux / WSL（社群腳本）：** `curl -fsSL https://unsloth.ai/install.sh | sh`。
  - **Windows（PowerShell）：** `irm https://unsloth.ai/install.ps1 | iex`。
  - **pip（Python 開發者）：** `pip install unsloth`（訓練用，含 CUDA kernel build）。
  - **uv：** `uv pip install unsloth`（PEP 668 friendly）。
  - **CLI 子指令：** `unsloth start claude` / `unsloth start codex` / `unsloth start hermes` / `unsloth start openclaw`（一行接到對應 agent）；`unsloth download` / `unsloth serve` / `unsloth train` 隨套件附帶。
  - **OpenAI 相容 API：** 啟動後內建 `/v1/chat/completions`，所有支援 OpenAI API 的工具（含 Hermes / Codex / Claude Code 設 `OPENAI_BASE_URL`）可直接打。
- **近期 release：** 最新 release 為 **`v0.1.800-beta` `Qwen3.8-27B`**，發佈於 **2026-08-14**（published_at 2026-08-14T14:18:49Z），非 prerelease（雖 tag 帶 `-beta`）；前 5 個 release 為 v0.1.800-beta (08-14) → v0.1.702-beta (08-13) → v0.1.701-beta **"Introducing Unsloth Desktop 🦥"** (08-11 19:24) → v0.1.70-beta **"Introducing Unsloth Desktop 🦥"** (08-11 15:31) → v0.1.62-beta (08-11 13:25)。**節奏為 1-3 天 / 一版**，**8/11 是 Unsloth Desktop 發表日**（兩個 release 都標 Introducing Unsloth Desktop 🦥 — 一個 15:31、一個 19:24，可能 19:24 是補修正版）；v0.1.800 帶 Qwen3.8-27B 是「desktop 內建 model 預設值」重大變更。

### `Glitch-Cat-Club/graph-memory-starter`
- **Repo 摘要：** 作者（Glitch Cat Club）2026-08-16 才公開的「**最小可運作的 knowledge-graph RAG 樣板**」—— 三張 SQLite table、一條 recursive CTE query、一個 Claude Code prompt hook，整包 15KB（repo size）、0 dependency、0 API key、0 build step。要證明一件事：「與其讓 LLM 自己 Grep + Read 串 3 跳事實，不如把跳躍燒進 SQL 程式碼；模型只負責 final answer」。**3 模型 × 2 條件的 eval 表（Aug 2026 自家測）**是這個 repo 的賣點：Haiku 在 search 條件下 1/3 hops → 在 graph 條件下 3/3 hops、tool calls 從 5 → 0、docs read 從 3 → 0、context 從 ~660 tokens → ~400 tokens（**固定成本**）。
- **3W1H：**
  - **What：** Python 3 + SQLite（標準庫）+ Claude Code `.claude/settings.json` prompt hook 的 reference 樣板；目錄結構 `corpus/` + `extraction/` + `src/{schema.sql, build_graph.py, recall.py, recall_hook.py}` + `hooks.json` + `extract-prompt.md`。
  - **Why：** Agent 記憶圈目前主流是 vector store + semantic search，但「3 跳事實檢索」vector search 在小模型（Haiku）幾乎必錯；這個樣板示範**把 multi-hop reasoning 從 inference-time 移到 build-time**（用強模型一次建好 graph，弱模型推理時 0 工具調用就能拿答案），給想自己做 agent memory / enterprise RAG 的人一個 verifiable baseline。
  - **Who：** 想自己搭 AI assistant memory 的人、用 Claude Code / Hermes / Codex / Pi 做企業內部知識庫的研究員、評估 vector vs graph vs hybrid 的架構師。
  - **How：** clone 後 `python src/build_graph.py` 一次建圖；`python src/recall.py "<question>"` 命令列查詢；把 `hooks.json` 複製進 `.claude/settings.json` 之後每次 Claude Code prompt 都會先跑 SQL 查詢、把結果塞回 context（README 給出 hooks.json 完整內容）。
- **安裝方式：**
  - **沒有 `pip install`** —— 本體就是 source 樣板，唯一 dependency 是 Python 3 標準庫（SQLite 內建）。
  - **Clone：** `git clone https://github.com/Glitch-Cat-Club/graph-memory-starter`。
  - **建圖：** `python src/build_graph.py`（讀 `corpus/` front-matter、寫進 SQLite）。
  - **命令列 query：** `python src/recall.py "Who approves supplier payments over $2,000?"`。
  - **Hook Claude Code：** `cp hooks.json <your-project>/.claude/settings.json` 之後每個 prompt 自動跑 recall。
  - **換成自己的 corpus：** 把自家 doc 放進 `corpus/`、跑 `extract-prompt.md` 讓 LLM 抽 nodes/edges/aliases、再 `build_graph.py`。
  - **不需要任何 API key**：build-time 用 LLM 抽 entities、query-time 完全本地 SQL。
- **近期 release：** 未找到 GitHub release（純 source-only 樣板，無 SemVer tag；`pushed_at` 為 2026-08-16T16:52:11Z、`stars` 36、`size_kb` 15 = 第一天釋出、單檔等級；open_issues = 0 = 尚未進入社群迭代階段，但 README 給出的 eval 表格**自己就是一份極完整的『可行性報告』**）。

### `Kludex/pgtask`
- **Repo 摘要：** **FastAPI 維護者（Sebastián Ramírez / `Kludex`）新開的 PostgreSQL 原生 durable task + workflow engine** —— 用 Postgres 當 queue + scheduler + durable state store，**不用 Redis、不用 broker、不用 Postgres extension**；同一份 task 用 Rust + Python + TypeScript + Go SDK 都能讀寫。README 第一行寫「PostgreSQL is the only service required for correctness.」、同時明確標「under active development, not ready for production」。**對標 celery / dramatiq / temporal / arq**，但把 broker dependency 整個拔掉；對想要 single-service 部署的中小型 backend team 是直接答案。`stars` 11、`pushed_at` 2026-08-17T05:31:41Z、`size_kb` 1012 = 今天才剛 push 過、active green-field。
- **3W1H：**
  - **What：** PostgreSQL-native durable task & workflow engine；Rust core（chart image `charts/pgtask`）+ Python / TypeScript / Go SDK + Helm chart + Tilt dev workflow；提供 leased at-least-once delivery + 延遲 / 重試 / 排程 / workflow steps / child tasks / OTel context 傳遞。
  - **Why：** 多數 task queue（Celery / RQ / Sidekiq）依賴外部 broker（Redis / RabbitMQ），這在自架 / 成本敏感 / 「不想再多一個服務」的場景是 friction；pgtask 整個 broker 邏輯用 Postgres 原生 LISTEN/NOTIFY + leased row + recursive query 寫出，等於「你的 Postgres 本來就在那，加幾張 table 就變成 worker queue」。
  - **Who：** FastAPI / Pydantic 生態開發者（Kludex 是 FastAPI 維護者）；想用 single-service 部署省 broker 維運成本的中小型 team；對標 Temporal 但不想跑 Temporal cluster 的人。
  - **How：** `git clone https://github.com/Kludex/pgtask` → `uv sync --project sdks/python --group dev` → `tilt up`（本地 K8s cluster 跑 Postgres + worker）→ 寫 `worker.py` + `enqueue.py`（README 給出完整範例）→ `uv run python worker.py` + 另一個 terminal `uv run python enqueue.py`。
- **安裝方式：**
  - **Python SDK（uv 推薦）：** `uv sync --project sdks/python --group dev`（Python 端 dev 安裝）。
  - **pip：** `pip install -e sdks/python`（從 clone 後跑，無 PyPI release）。
  - **Rust SDK：** `cargo add pgtask`（尚未推到 crates.io，需從 source 編）。
  - **TypeScript / Go SDK：** `cd sdks/{typescript,go} && <對應 package manager>`（從 source）。
  - **Dev workflow（Tilt + K8s）：** `tilt up`（自動 build image + `helm install charts/pgtask` + 跑 Postgres + 連 worker）。
  - **CLI：** `pgtask --help` / `pgtask migrate` / `pgtask worker` 隨 Rust binary 附帶。
  - **沒有 PyPI / npm published** —— 一切都從 source；`pushed_at` 2026-08-17 = 仍在 heavy iteration（README 第一個 WARNING 就是 not ready for production）。
  - **環境變數：** `export PGTASK_DATABASE_URL=postgresql://pgtask:***@localhost:54329/pgtask`（Tilt 預設值）。
- **近期 release：** 未找到 GitHub release（green-field 專案、`stars` 11、`open_issues` 1、`size_kb` 1012；`pushed_at` 2026-08-17T05:31:41Z = 今天仍在 commit）。無 tag 表示尚未 freeze API；對想早期採用的開發者，這是**「加入早期 API 形塑」**的窗口期，但要接受未來 breaking change 風險。

## 重點觀察

- **Top 3 對昨日（08-16）的位移幅度：本週最大的一次更替。** 昨日 Top 3 = cordiverse/cordis + cathrynlavery/diagram-design + cactus-compute/needle；今日 Top 3 = cordiverse/cordis + basecamp/omarchy + unslothai/unsloth。**只有 1/3 重複（cordis）**，是 14:00 系列自 08-11 連續觀察以來「Top 3 de-overlap 最高的一天」（對比 08-15/16 的 3/3 重複）。新進 Top 3 的 `basecamp/omarchy`（DHH 親推 Linux distro）與 `unslothai/unsloth`（local-first AI desktop）共同訊號：**社群正在從「agent skill / framework」轉向「真實可用的 OS / desktop binary」**——diagram-design / needle 雖仍在 #4 / #7 Trending，但已被擠出主舞台。
- **Tier-B elevation 連三日啟動（08-15 突破 + 08-16 + 08-17 沿用）：** 沿用 08-15 codification 的 path 5，今日 2 個 web exploration 都從外部 web search（subagent `delegate_task` 跑 161 秒 / 25 次工具）拉來 `Glitch-Cat-Club/graph-memory-starter`（當天 36 stars、Aug 16 才 push、agent-memory 圈首次有可重現 baseline）與 `Kludex/pgtask`（**FastAPI 維護者親自開的 green-field**、當天 push、`stars` 11 = 1 天之內）。兩個都跟 Tier-A HTML 沒有交集（cordiverse/cordis / basecamp/omarchy / unslothai/unsloth 都不是 Trending #4-#25），所以今天的探索價值 = 100% 來自外部 web search。`pgtask` 是本週首次出現「明星維護者新開 green-field」，跟 `Glitch-Cat-Club/graph-memory-starter` 的「最小但完整 evidence-based 樣板」形成兩種 complementary 風格——前者要的是「正確性設計」，後者要的是「可行性證據」。
- **安裝 idiom 光譜：5 個 repo 跨越 5 種 install 形態 + 首次出現「OS distro」與「早期 green-field」兩種變體：** cordis 是 **npm/yarn monorepo + RC 4.0.0-rc.8**（peer deps 為 optional plugin、未 stable API）；omarchy 是 **OS image + manual-only**（**Type-5 新變體**：無 build.sh / Makefile，整包是 ISO + 51 章節手冊，README 一句話指向 omarchy.org）；unsloth 是 **native binary + installer script + pip + uv + CLI agent-bridge**（**最豐富的多形態**：每個 OS 平台都有單檔安裝，社群安裝 script、pip 開發者介面、`unsloth start <agent>` CLI 一次到齊）；graph-memory-starter 是 **source-only Python 樣板 + clone + hook json**（**Type-6 新變體**：15KB repo、無 SemVer、無 pip、無 build step）；pgtask 是 **uv sync + Tilt/K8s/Helm + Rust/Python/TS/Go SDK + source-only**（**Type-7 新變體**：早期 green-field、無 PyPI/npm/crates.io、Tilt + Helm + K8s 整套 dev infra 一起出貨）。今日 5 picks 跨 5 種 install 形態，**其中 3 個沒有任何官方 pip / npm package**（omarchy 是 OS、graph-memory-starter 是 15KB 樣板、pgtask 是 green-field 尚未 freeze API），**只有 cordis（npm）+ unsloth（pip + native）有正式套件化路徑**。
- **語言 / runtime 生態：** TypeScript（cordis）+ Shell（omarchy 51 章節手冊 + installer scripts）+ Python（unsloth、graph-memory-starter）+ Rust（pgtask core）+ 多 SDK（pgtask 給 Rust + Python + TypeScript + Go 4 種）。今天 5 picks 涵蓋 **4 種第一語言**，其中 `omarchy` 是純 Shell 為主（這在 Trending 罕見）+ `pgtask` Rust 為核心。**對齊主人目前軌跡：** cordis（08-16 + 08-17 連續 → plugin/composability 抽象對主人 multi-backend 觀察 1:1）；omarchy（Linux OS → 主人雖是 macOS 但對 tiling WM / Hyprland 路線有長期觀察價值，DHH 在 basecamp 推 HEY / Rails 的「opinionated software」哲學值得 cross-pollination）；unsloth（**主人目前 Hermes Agent 工作的直接對標**：`unsloth start hermes` 把本地模型接到 Hermes = 主人 horo-agent 下游的明確 reference path；同時 unsloth 支援 Hermes 列為官方 agent host）；graph-memory-starter（純 SQLite knowledge-graph，**對主人 horo-agent / horo-webui memory layer 是最直接的 reference design**：3 table + 1 recursive query + 1 prompt hook，total 15KB，比任何 vector store 都輕）；pgtask（FastAPI 維護者 green-field → 主人若要在 horo-agent 內做 task queue / scheduling，pgtask 是「Postgres-only」的對標，可以單獨決定要不要 fork / 引用）。
- **License 乾淨度：5/5 全是 permissive（MIT 4 個 + Apache-2.0 1 個）：** cordiverse/cordis = MIT、basecamp/omarchy = MIT、unslothai/unsloth = **Apache-2.0**（**case-A2**，08-12 codification：Apache-2.0 含專利授權 + 較長 attribution 條款，但商用 friendly）、Glitch-Cat-Club/graph-memory-starter = MIT、Kludex/pgtask = MIT。**今天 0 個 GPL / copyleft / 商業限制** —— 這是 14:00 系列自 08-01 開始觀察以來，**首次出現 5/5 全 permissive license**（08-12 codification 累積下來多半是 4 MIT + 1 GPL 或 3 MIT + 1 Apache + 1 GPL）。對主人 horo-agent / horo-webui downstream 工作，**今天的 5 picks 全部可以直接 fork / 嵌入 / 二次發佈**，無 license 摩擦風險。
- **「跨天重複 / 跨週重複」現象的再次檢驗：** 08-15 → 08-16 = **3/3 重複**（Top 3）；08-16 → 08-17 = **1/3 重複**（cordis）；這是 14:00 系列觀察下來**重複率波動最大的一天**。可能原因：(1) Trending 算法的「新鮮度」權重今天明顯啟動（DHH 推 omarchy 帶 25k+ stars、unsloth 推 Desktop 帶 72k stars 雙雙衝上來）；(2) diagram-design / needle / FluidVoice / CLI-Anything 雖然仍在 #4-#10 級距（cordis + needle + 4 個昨日抓的都在 #1-#7 內），但昨日已經在主檔，今日被擠出 Top 3。這是「**Trending 算法會對 owner reputation + 短期 viral inflow 加權**」的再驗證，呼應昨日 codification：algorithm 不只看 smooth stars/day。
- **Viral tail 觀察：** `unslothai/unsloth` 從 08-11 開始連續 8/11-8/14 共 5 個 release、stars 已達 72,793（**全榜唯一突破 70k 的 repo**），Desktop 跨 5 平台；`basecamp/omarchy` 從 release v4.0.0（08-14）三天內衝 25,585 stars（**單日 +25k 是 Trending 罕見級距**）。兩個 repo 都不是「一日 peak」而是「已 scalable」的標準 pattern。**主人想在 horo-agent 觸發條件裡加入的「連 3 天 pushed_at continuous + 持續 inflow」signal 對 unsloth 與 omarchy 兩者皆成立**。
- **「star count 與內容深度」的對比：** 今天 5 picks 的 stars 是 **72,793 / 25,585 / 5,400 / 36 / 11**，跨度從 11 到 72k，**5 個 orders of magnitude**。`pgtask` 雖然只有 11 stars，但**作者 Kludex 是 FastAPI 維護者**（GitHub 主頁 verified），影響力遠超 star count；`graph-memory-starter` 雖然只有 36 stars，但 README 給出的 **3 模型 × 2 條件 = 6 數據點 eval 表**比 90% 萬 stars repo 的 README 都嚴謹。**這是「不要用 stars 衡量內容深度」的本日佐證**——主人若在 horo-agent / horo-webui 內部決定哪些 repo 值得 deep-dive，star count 應該只是 1 個 feature、**不是 binary filter**。
- **主人軌跡對齊最終總結：** 今日 5 picks 對主人 4 條主軸的對齊程度 = **(1) Hermes / agent infra：unsloth 直接列 Hermes 為官方 agent host + cordis 是 plugin/composability 抽象的 reference**；(2) **multi backend / Postgres：** pgtask 是 FastAPI 維護者開的 green-field，**值得追蹤是否演化成「horo-agent 任務排程」的可嵌入元件**；(3) **memory / RAG：** graph-memory-starter 給出最小可運作 SQLite knowledge-graph baseline，**對 horo-agent memory layer 是直接可用的 reference 設計**；(4) **OS / desktop 觀察線：** omarchy 給出 DHH 推動的「opinionated Linux」哲學，雖然主人用 macOS，但對應的 Hyprland tiling / manual-driven onboarding / single-decision UX 是 cross-platform transferable 的設計語言。