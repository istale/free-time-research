# GitHub 專案動態
- 檢查時間：2026-08-03
- 檢查對象：microsoft/AI-For-Beginners、usekaneo/kaneo、lyogavin/airllm、esengine/DeepSeek-Reasonix、usestrix/strix
- 來源：Trending Daily Top 3（https://github.com/trending?since=daily）+ 自由探索 Web Search（Tavily：「deepseek AI coding agent terminal open source github 2026 prefix-cache」、「github August 2026 new release local LLM browser inference open source」、「github new repository star 2026 July release AI agent open source breakthrough」）

## Repo 摘要與 3W1H

### microsoft/AI-For-Beginners
- **Repo 摘要：** `microsoft/AI-For-Beginners` 是 Microsoft 維護的 12 週、24 課 AI 入門課程，內容從 symbolic AI、神經網路、CNN、RNN、NLP、GAN 到可執行的 PyTorch / TensorFlow Jupyter Notebook，並有 50 多種語言的自動翻譯 README。它不是用來部署模型的 library，而是給初學者、教師與想用實作建立 AI 基礎的學習者使用的教學 curriculum。今天持續在 Trending Top 1，但 repo 的活躍度應與課程內容維護而非新版本發布分開看。
- **3W1H：**
  - **What：** 一套開源、跨語言翻譯的 AI 教學 curriculum，搭配 Notebook、quiz、labs 與 Microsoft Learn 延伸教材。
  - **Why：** 把 AI 概念拆成 24 課可循序完成的小單元，並用 TensorFlow / PyTorch 雙框架 Notebook 與「Hello AI World」級 beginner-friendly examples 降低第一次接觸的門檻；持續登上 Trending，主要反映教學資源在社群中的長期需求。
  - **Who：** AI / ML 初學者、教師、課程設計者，以及想用 VS Code、Codespaces 或 Binder 跑實作的開發者。
  - **How：** 從 `lessons/0-course-setup` 的環境設定開始，使用 GitHub Codespaces / VS Code 或本機 Jupyter 逐課閱讀與執行；可先看 `examples/README.md` 的四個入門範例，再進入完整課程。
- **安裝方式：**
  - **pip/uv/poetry：** 未找到單一 library 安裝方式，這是課程 repo。要執行 Notebook 可先 `git clone https://github.com/microsoft/AI-For-Beginners.git`，再依 setup lesson 建立 Python 環境並安裝 `requirements.txt`：`python -m pip install -r requirements.txt`（或在 venv 中 `uv pip install -r requirements.txt`）。
  - **npm/pnpm/yarn/npx：** 未找到明確方式；課程另有獨立的 quiz app，須依其子目錄說明另行本機執行或部署。**Sparse clone（建議）：** `git clone --filter=blob:none --sparse https://github.com/microsoft/AI-For-Beginners.git` → `cd AI-For-Beginners` → `git sparse-checkout set --no-cone '/*' '!translations' '!translated_images'`，可避免一次下載全部翻譯。
- **Repo metadata：** Description `12 Weeks, 24 Lessons, AI for All!`；Stars **59,621**；Forks **11,700**；Language **Jupyter Notebook**；Topics `ai`、`artificial-intelligence`、`cnn`、`computer-vision`、`deep-learning`、`gan`、`machine-learning`、`nlp`、`rnn` 等；License **MIT**；Created 2021-03-03；Updated **2026-08-03 06:01:27 UTC**；Pushed **2026-07-21 11:11:48 UTC**；Default branch `main`。
- **近期 release：** **未找到 GitHub release**（`/releases/latest` 回傳 404）。這個 repo 的交付形態是課程內容、翻譯與 Notebook，不能把 Trending 排名直接誤讀為近期版本號。

### usekaneo/kaneo
- **Repo 摘要：** `Kaneo` 是 MIT 授權、可自架的開源專案管理工具，定位在 Jira / Linear 替代品，提供 issue tracking、Kanban、團隊協作與極簡介面。README 給出 drim 一鍵部署、Docker Compose + PostgreSQL、Kubernetes Helm chart 與 pnpm workspace 的本機開發路徑；網站 `https://kaneo.app/` 同時提供 hosted Cloud。適合希望掌握資料與部署環境、又不想承受大型商用 PM 系統複雜度的小型工程團隊。
- **3W1H：**
  - **What：** TypeScript / React / Hono 生態的 self-hosted project-management app，涵蓋 issue tracker、Kanban 與團隊協作。
  - **Why：** 以「less is more」對抗 Jira / Linear 類工具的重量，同時提供 Docker、one-click deployment、獨立 API / web image 與 Helm chart，讓試用與正式部署有不同入口。
  - **Who：** 小型工程團隊、偏好 self-host 的組織、需要 issue / Kanban 但不想把資料交給 SaaS 的使用者，以及想貢獻 TypeScript monorepo 的開發者。
  - **How：** 最短試用路徑是 `curl -fsSL https://assets.kaneo.app/install.sh | sh` → `drim setup`；也可準備 `.env` 後用 Docker Compose 啟動 Kaneo + PostgreSQL；開發者則 clone repo、`pnpm install`、設定環境變數，再 `pnpm dev`。
- **安裝方式：**
  - **npm/pnpm/yarn/npx：** repo 本身不是以公開 npm package 作為終端使用入口；從 source 開發使用 `git clone https://github.com/usekaneo/kaneo.git` → `cd kaneo` → **`pnpm install`** → `pnpm dev`。
  - **pip/uv/poetry：** 未找到明確方式。
  - **Docker Compose：** 依 README 的 `compose.yml` 使用 `ghcr.io/usekaneo/kaneo:latest` 與 `postgres:16-alpine`，準備 `.env`、`POSTGRES_PASSWORD`、`AUTH_SECRET` 後執行 `docker compose up -d`。
  - **一鍵部署：** `curl -fsSL https://assets.kaneo.app/install.sh | sh` → `drim setup`；這是部署工具安裝，不是 pip / npm package。
- **Repo metadata：** Description `🎯 All you need. Nothing you don't. Open source project management that works for you, not against you.`；Stars **6,331**；Forks **526**；Language **TypeScript**；Topics `hacktoberfest`、`hono`、`issue-management`、`issue-tracker`、`jira-alternative`、`kanban`、`linear-alternative`、`project-management`、`react`、`self-hosted`、`typescript`；License **MIT**；Created 2024-12-31；Updated **2026-08-03 05:53:31 UTC**；Pushed **2026-08-02 07:13:12 UTC**；Default branch `main`；Homepage `https://kaneo.app/`。
- **近期 release：** **2026-07-30 20:03:50 UTC**；`v2.12.1`；**Release v2.12.1**。正式 stable release，release note 主要包含 comparison / alternative pages 的功能更新；今日 Trending 第二名與近期正式版本同時存在。

### lyogavin/airllm
- **Repo 摘要：** `AirLLM` 是用「分層 / per-expert streaming」把大語言模型推理記憶體需求壓到極低的 Python inference library：在不量化、不蒸餾、不剪枝的前提下宣稱可在單卡 4GB GPU 上跑 70B、在 8GB 上跑 405B Llama 3.1、在約 12GB 上跑 671B DeepSeek-V3，並在 2026-07 加了 **Kimi K3（2.8T）** 支援。MoE 模型因為每次只載入被 route 到的 expert，所以 Kimi K3 可在單卡 **3.72GB VRAM** 端到端跑（README 報告在 RTX 6000 Ada 上）。今日登上 Trending 第三與 v3.1.0 release（2026-07-29）都圍繞這個「在單卡跑超大模型」賣點。
- **3W1H：**
  - **What：** Python inference library，命名空間 `airllm`，提供 `AutoModel.from_pretrained(...)` 的 HF-style API；底層會把模型切層、預取、並對 MoE 做 per-expert streaming。
  - **Why：** 把「消費級 GPU 無法跑大模型」轉成「只要 RAM / VRAM 足以容納一層 / 一個 expert、且時間允許 streaming 載入，就能在本機執行」；適合研究、demo、PoC 與資料不能上雲的情境。
  - **Who：** 研究 / demo LLM 的工程師、需要在單卡跑 70B–2.8T 模型的人、CIFAR / personal GPU user、PoC 設計者；Kimi K3 路徑還要相容版本的 `flash-attn`、CUDA 12 torch 與 `transformers` 4.56.x。
  - **How：** `pip install airllm` → `from airllm import AutoModel` → `model = AutoModel.from_pretrained("Qwen/Qwen3-32B")` → tokenize → `model.generate(...)`。對 K3 需要額外 `pip install compressed-tensors flash-attn`，並使用 CUDA 12 的 torch + `transformers` 4.56.x；詳細 notebook 與 README 在 repo 中。
- **安裝方式：**
  - **pip/uv/poetry：** **`pip install airllm`**（README 標示推薦）；PyPI 也提供 `pip install airllm`，可用 `uv pip install airllm` 或在 poetry 環境中 `poetry add airllm`。Kimi K3 額外需要 **`pip install compressed-tensors flash-attn`**。
  - **npm/pnpm/yarn/npx：** 未找到明確方式；這是 Python-only library。
  - **From source：** `git clone https://github.com/lyogavin/airllm.git` → `cd airllm` → `pip install -e .`。MacOS / CPU inference、模型分層 / shard 機制與 example notebooks 另見 README。
- **Repo metadata：** Description `AirLLM 70B inference with single 4GB GPU`；Stars **25,917**；Forks **2,900**；Language **Jupyter Notebook**（核心 lib 在 `air_llm/` 目錄，是 Python package；repo 內含大量 example notebooks）；Topics `chinese-llm`、`chinese-nlp`、`finetune`、`generative-ai`、`instruct-gpt`、`llama`、`llm`、`lora`、`open-models`、`open-source`、`qlora` 等；License **Apache-2.0**；Created 2023-06-12；Updated **2026-08-03 05:59:39 UTC**；Pushed **2026-07-29 01:08:32 UTC**；Default branch `main`。
- **近期 release：** **2026-07-29 01:08:32 UTC**；`v3.1.0`；**AirLLM v3.1.0 — Kimi K3 (2.8T) on a single card**。這是 2026-07 加 K3 支援後的正式 release，也是今日 Trending 第三名的關鍵籌碼；K3 路徑的依賴限制（flash-attn、CUDA 12 torch、`transformers` 4.56.x）README 也明確寫出。

### esengine/DeepSeek-Reasonix
- **Repo 摘要：** `DeepSeek-Reasonix`（npm package name `reasonix`）是一個專為 DeepSeek 設計、圍繞「prefix-cache 穩定性」打造的 terminal AI coding agent / harness：單一靜態 Go binary（`CGO_ENABLED=0`），以 `reasonix.toml` 宣告 providers / agents / tools / plugins，所有外部工具以 stdio JSON-RPC 啟動（相容 MCP），預設 DeepSeek-V4-Flash 並可 per-turn 或 per-session 升級到 V4-Pro。它同時提供 CLI / TUI、Desktop app 與 VS Code extension，VS Code extension 並不內嵌 CLI，而是呼叫本機 `reasonix acp` 後端。今日 release `desktop-v1.19.3`（2026-08-03，published_at 2026-08-03 00:17:30 UTC）正好在採樣當天，是本日最「新鮮」的一筆正式 release。
- **3W1H：**
  - **What：** Go-based terminal coding agent + 多平台 desktop app + VS Code extension；公開 npm package `reasonix`，對應 CLI binary 也是 `reasonix`；可作為 MCP host / `acp` server。
  - **Why：** 把「DeepSeek prefix cache」當成一等公民來設計 — 啟動時注入穩定環境摘要、對過時工具輸出做 prune / snip，並在 summary compaction 前做 cache-aware 的整理；目標是 long-running coding session 不會因為 context 變動而把 cache 命中率打掉，讓 token 成本維持在低位（Threads 上自述實測 ~$12 vs ~$61）。
  - **Who：** DeepSeek API 的重度用戶、想在 terminal 跑長時 coding session 又在意成本的開發者、需要 MCP / plan-mode / 多 provider 的人，以及想用 Reasonix 當 `acp` backend 串到 VS Code 的使用者。
  - **How：** 先 `npm i -g reasonix` 取得 CLI（macOS 也可 `brew install esengine/reasonix/reasonix`），再以 `reasonix.toml` 設定 providers / agents / plugins；要接 VS Code 可裝 marketplace extension `SivanLiu.reasonix-agent`（先裝 Path A 的 CLI）；desktop app 直接從官方下載頁安裝。
- **安裝方式：**
  - **npm/pnpm/yarn/npx：** **`npm i -g reasonix`**（README 標示推薦，會拉預建 native binary）；升級用 `npm i -g reasonix@latest`。macOS 也提供 `brew install esengine/reasonix/reasonix`。
  - **pip/uv/poetry：** 未找到方式；runtime 是 Go，沒有 Python package。
  - **Prebuilt binary：** 從 GitHub releases 下載對應平台（darwin / linux / windows × amd64 / arm64）的 tarball + `SHA256SUMS`。
  - **Desktop：** macOS Universal `.dmg` / `.zip`（Apple Silicon / Intel）、Windows Installer `.exe` 或 portable `.zip`（x64 / ARM64）、Linux `.deb` 或 `.tar.gz`（x64）；Windows installer 走 SignPath.io 簽章。
  - **VS Code extension：** 安裝 marketplace extension `SivanLiu.reasonix-agent`；VSCodium / Eclipse Theia 用 Open VSX Registry 同名擴充；extension 不內嵌 CLI，依賴本機 `reasonix acp`。
- **Repo metadata：** Description `DeepSeek-native AI coding agent for your terminal. Engineered around prefix-cache stability — leave it running.`；Stars **29,313**；Forks（API metadata 未另列）；Language **Go**；Topics `agent`、`agent-framework`、`ai-agent`、`ai-coding`、`cli`、`coding-agent`、`deepseek`、`developer-tools`、`ink`、`llm`、`prompt-caching`、`r1`、`terminal`、`tool-use`、`tui`、`typescript`；License **MIT**；Created 2026-04-21；Updated **2026-08-03 06:01:12 UTC**；Pushed **2026-08-03 04:52:43 UTC**；Default branch `main-v2`；Homepage `http://reasonix.io/`。
- **近期 release：** **2026-08-03 00:17:30 UTC**（created_at 2026-08-02 18:05:59 UTC）；`desktop-v1.19.3`；**Reasonix Desktop v1.19.3**。Release note 引入帶永久 launcher 的 atomic desktop update、移除 Guard 和安全模式、為受阻的 Delivery 工作區加入 recovery action、把發佈路徑收斂成單一穩定 channel，並在網站加上 GitHub 連結；這是本日五個 repo 中**唯一一筆 release 日期就在採樣當天**的版本。

### usestrix/strix
- **Repo 摘要：** `Strix` 是把 LLM agent 變成可實際跑 PoC 的 autonomous AI pentesting tool：team of AI hackers 動態執行你的程式碼、找出漏洞，並以可重現的 proof-of-concept 驗證，不是只丟靜態掃描的 false positive。它提供 Docker sandbox、CLI、`strix --target <dir>` 與 GitHub Actions / CI/CD 整合，也額外提供 hosted platform `app.strix.ai`。發布在 PyPI（`strix-agent`）、Apache-2.0、Stars ~46k 並登上 Trendshift weekly，反映 security tooling 在 2026 年的關注度。
- **3W1H：**
  - **What：** 開源 AI 滲透測試 agent；Python 套件 `strix-agent`（PyPI），CLI 為 `strix`，背後跑 Docker sandbox。
  - **Why：** 傳統 SAST / DAST 工具誤報太多、手動 pentest 又貴又慢；Strix 把 multi-agent orchestration 帶進 recon / exploit / validation，並輸出可貼上 bug bounty 報告的 PoC 與可套用於 PR 的 patch 草稿，順手解決「掃出來但不能 exploit」的痛點。
  - **Who：** 應用安全工程師、滲透測試員、bug bounty 獵人、red team、把 security check 拉進 CI 的 DevSecOps、以及需要快速出具合規報告的小型資安團隊。
  - **How：** 安裝 Docker + 任一受支援 LLM API key → `curl -sSL https://strix.ai/install | bash` → 設定 `STRIX_LLM` / `LLM_API_KEY` → `strix --target ./app-directory`；第一次跑會自動 pull sandbox image，結果寫到 `strix_runs/<run-name>`。GitHub Actions 用 hosted `app.strix.ai` 接 repo / domain 即可。
- **安裝方式：**
  - **pip/uv/poetry：** **`pip install strix-agent`**（PyPI 公開）；也可 `uv pip install strix-agent` 或在 poetry 環境 `poetry add strix-agent`。這是 Python package，CLI 透過 entry point 暴露 `strix`。**官方推薦的安裝路徑其實是一鍵 shell script：** `curl -sSL https://strix.ai/install | bash`，會做 Docker + CLI 整體 setup。
  - **npm/pnpm/yarn/npx：** 未找到明確方式。
  - **Docker：** Strix 本身跑在 Docker sandbox；first run 會自動 pull sandbox image；不需要單獨下載 container。
  - **From source：** `git clone https://github.com/usestrix/strix.git` → 依 `pyproject.toml` / `requirements` 安裝 Python 依賴，再以 Docker 啟動 sandbox。
- **Repo metadata：** Description `Open-source AI penetration testing tool to find and fix your app’s vulnerabilities.`；Stars **46,718**；Forks（API metadata 未另列）；Language **Python**；Topics `agents`、`ai-hacking`、`ai-penetration-testing`、`ai-pentesting`、`ai-security`、`bug-bounty`、`cybersecurity`、`ethical-hacking`、`hacking`、`llm-security`、`offensive-security`、`penetration-testing`、`red-teaming`、`security`、`security-automation`；License **Apache-2.0**；Created 2025-08-05；Updated **2026-08-03 05:55:12 UTC**；Pushed **2026-08-03 02:46:20 UTC**；Default branch `main`；Homepage `https://strix.ai/`。
- **近期 release：** **2026-07-27 20:02:01 UTC**；`v1.4.1`；**v1.4.1**。正式 stable release，配合 GitHub Actions / CI/CD 整合（PR 時自動掃、阻擋不安全 code）與 hosted platform `app.strix.ai`；這是本週稍早（約 7 天前）的版本，今天仍有 push 與 `pushed_at 2026-08-03 02:46:20 UTC`，顯示 branch 活動未中斷。

## 重點觀察
- **Trending Top 3 對應到三種完全不同的「新鮮度」訊號：** `microsoft/AI-For-Beginners` 沒有任何 GitHub release，last push 是 2026-07-21；`usekaneo/kaneo` 的 `v2.12.1` 在 2026-07-30，正式版穩定但 pushed_at 也是前一日；`lyogavin/airllm` 的 `v3.1.0` 在 2026-07-29，刻意搭著 Kimi K3（2.8T）一起打榜。把 Trending 名次當作 release 新鮮度容易誤判 — 必須看 `/releases/latest` 才知道這天實際上新了什麼。
- **兩個自由探索 repo 的 release 都比 Trending Top 3 更貼近採樣當天：** `esengine/DeepSeek-Reasonix` 的 `desktop-v1.19.3` published_at 正是 2026-08-03 00:17:30 UTC，是本份輸出五個 repo 中唯一一筆 release 在採樣當天；`usestrix/strix` 的 `v1.4.1` 在 2026-07-27，並且 2026-08-03 仍有 push。Web-search 帶來的兩個 repo 都明確命中「近期有正式 release + README / 文件 / 安裝完整」這條 spec，質量與 Trending 上看見的東西相當、甚至更貼近主人對 release 資訊的關注。
- **安裝生態走向雙軌：** Python 這一軌有 `pip install airllm` 與 `pip install strix-agent`（後者還附一鍵 shell + Docker sandbox 整合）；Node / npm 這一軌有 `npm i -g reasonix`；TypeScript 自架 app 走 `pnpm install` 開發 + Docker Compose / drim 部署；課程 repo 不靠 `pip install`，靠 `requirements.txt` + sparse clone。**今日沒有任何一個 repo 是「沒有任何正式安裝命令」的全說明型**，與上月觀察的「教學 / 清單 repo 偏多」形成對比。
- **單卡 / 本機推理仍然是 AI 開源的主旋律：** AirLLM 把「單卡 4GB 跑 70B」推到「單卡 ~3.72GB 跑 Kimi K3 2.8T」，Reasonix 則把 DeepSeek prefix cache 做成 long-session coding agent 的 cost reduction，Strix 把多 agent 放進 Docker sandbox 做 AI pentesting。三者都不是雲端 API 的另一層 wrapper，而是把「運算 / 執行 / 驗證」拉回本機、Docker 或 terminal，剛好對齊主人近期偏好的 self-host / local-first 路徑。
- **MoE / 前沿模型 × 工程化 package 開始出貨：** AirLLM 的 K3 支援 (`v3.1.0`) 把 2.8T 參數的 MoE 變成可在本機 streaming 的物件，Strix 把 70k+ 級別的多 agent 框架做成可 PR-block 的 GitHub Actions；Reasonix 把 DeepSeek prefix cache 設計做成 `reasonix.toml` 的 config-driven harness。共同的 pattern 是「前沿能力不再停在 paper / demo，而是走 PyPI / npm / GitHub release 的常規交付管道」，對應到主人工作中「release 日期清楚、安裝方式完整」的核心檢查項，今天這五個 repo 五個都過關。