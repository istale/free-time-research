# GitHub 專案動態
- 檢查時間：2026-07-30
- 檢查對象：opengeos/GeoLibre、moeru-ai/airi、affaan-m/ECC、MoonshotAI/Kimi-K3、kvcache-ai/AgentENV
- 來源：Trending Daily Top 3 + 自由探索 Web Search（GitHub Search API by created:2026-07-23..2026-07-30, stars>100, sort=stars desc）

## Repo 摘要與 3W1H

### opengeos/GeoLibre
- **Repo 摘要：** GeoLibre 是免費、輕量、cloud-native 的開源 GIS 平台，同一份 workspace 以 Tauri v2 + React + TypeScript + MapLibre GL JS + DuckDB-WASM Spatial + deck.gl 編譯成 web app、桌面 app、Android app 與 Jupyter anywidget，並把資料留在本機與 Jupyter kernel 內。它主打「瀏覽器即 GIS」加上 3D Tiles、Time Slider、行星底圖（地球／月球／火星／水星／冥王星／金星）、SQL Workspace 與 plugin 生態；適合不想被 QGIS／ArcGIS 綁住、但需要雲原生格式（STAC、COG、PMTiles）的 GIS 工作者、地科研究與資料科學團隊。Repo 為 TypeScript、MIT，4,226 stars，最後更新 2026-07-30，且 7 月 29 日剛發布 v2.4.0。
- **3W1H：**
  - **What：** 跨瀏覽器／桌面／Android／Jupyter 的雲原生 GIS app，加上 Python anywidget。
  - **Why：** 傳統 GIS 桌面軟體笨重、付費、且難以嵌入分析流程；GeoLibre 把同一個 Tauri + React workspace 編出四種 runtime，並用 DuckDB-WASM Spatial 把 SQL 拉到瀏覽器，讓 GIS 工作直接走 COG／PMTiles／STAC 等 cloud-native 格式。
  - **Who：** GIS 分析師、地理空間資料科學家、遙測／城市規劃／地科團隊，以及需要在 Jupyter notebook 內同 UI 操作的開發者。
  - **How：** 最快路徑是直接打開 `https://web.geolibre.app/`；桌面用戶從 geolibre.app/downloads 抓 Windows／macOS／Linux installer，Android 用戶走 Google Play；Jupyter 用戶 `pip install geolibre` 後用 `leafmap`-style API 啟動 anywidget；開發者 clone repo 並參考 `docs/contributing.md`。
- **安裝方式：** **pip/uv/poetry：** `pip install geolibre`（conda-forge 也有 `geolibre`，PyPI 最新 2.4.0，需 Python ≥ 3.11），這是 Jupyter anywidget 的進入點。**桌面：** Windows／macOS／Linux installer 來自 `geolibre.app/downloads`；Linux 另有 AUR `geolibre-bin`、FlatPark、Microsoft Store、Android Google Play 等多通路。**Web：** 直接 `https://web.geolibre.app/` 免安裝。**從源碼 build：** `git clone` 後照 `docs/contributing.md` 的 dev setup 進行。README 未列 npm 套件形式。
- **Repo metadata：** Description `A lightweight, cloud-native GIS platform for visualizing, exploring, and analyzing geospatial data. It runs in the web browser, on the desktop, on mobile, and inside Jupyter notebooks.`；Stars 4,226；Language TypeScript；Topics `data-science`, `duckdb`, `geolibre`, `geospatial`, `maplibre`, `maplibre-gl-js`, `tauri-app`；License MIT；Updated at 2026-07-30；Homepage https://geolibre.app。
- **近期 release：** **2026-07-29**；`v2.4.0`；**v2.4.0**。

### moeru-ai/airi
- **Repo 摘要：** AIRI 是自架、使用者擁有的 AI companion／AI VTuber「靈魂容器」，把 Neuro-sama 級的數位生命從單機直播延伸到瀏覽器、桌面、Discord、Telegram、Minecraft、Factorio 等場景。它以 WebGPU／WebAudio／WebAssembly 等純 web 技術跨平台運作，提供即時語音、VRM／Live2D 角色模型、本機／線上 LLM 切換與遊戲代理能力。Repo 為 TypeScript、MIT，45,533 stars，最後更新 2026-07-30。
- **3W1H：**
  - **What：** 跨平台 AI companion／AI VTuber app 與可擴充數位角色 runtime。
  - **Why：** 多數 AI 角色產品仍卡在文字或單一雲端模型；AIRI 把 VRM／Live2D 化身、即時 TTS／STT、記憶、模型切換與遊戲代理放在同一個可自架的開源系統中。
  - **Who：** AI companion 使用者、VTuber／Live2D／VRM 創作者、研究 speech、computer vision、Web 推論的開發者，以及想把 Neuro-sama 概念落地成個人 AI 的玩家。
  - **How：** 一般使用者直接抓 GitHub release 的 `.dmg`／`.exe`／`.AppImage`／`.apk`，或用 `brew install --cask airi`（macOS）／`winget install MoeruAI.AIRI`（Windows）；開發者 clone monorepo 後 `pnpm i && pnpm dev` 啟動 web stage。
- **安裝方式：** 未找到明確 pip/npm library 安裝方式（產品分發以桌面／行動 binary 為主）。**桌面 app：** macOS `brew install --cask airi`；Windows `winget install MoeruAI.AIRI`；亦可由 GitHub release 下載 `.dmg`／`.exe`／`.AppImage`／`.apk`。**Web app：** 直接在 `airi.moeru.ai/docs/` 開瀏覽器版本。**pnpm 開發：** clone monorepo 後 `pnpm i && pnpm dev`。
- **Repo metadata：** Description `💖🧸 Self hosted, you-owned Grok Companion...`；Stars 45,533；Language TypeScript；Topics `ai-companion`, `ai-vtuber`, `airi`, `digital-life`, `grok-companion`, `live2d`, `neuro-sama`, `neurosama`, `openclaw`, `vrm`, `vtuber`；License MIT；Updated at 2026-07-30；Homepage https://airi.moeru.ai/docs/。
- **近期 release：** **2026-07-18**；`v0.11.3`；**v0.11.3**。

### affaan-m/ECC
- **Repo 摘要：** ECC 是給 AI coding agent 的「工程操作系統」：plan → test → implement → review → verify → remember → improve 一條龍，含 67 agents、281 skills、94 legacy command shims、hooks、rules、memory 與 AgentShield 安全掃描。它鎖定 Claude Code 為首發 experience，並有 Codex、Cursor、OpenCode、Gemini、Zed、GitHub Copilot、Antigravity、Qwen 等 harness adapter；repo 同時發行 npm 套件 `ecc-universal` 與 Anthropic marketplace plugin `ecc@ecc`。今天剛好 7 月 30 日，repo 仍極活躍、stars 達 235,757，sponsors 含 CodeRabbit、Greptile、Atlas Cloud 與 Moonshot AI（Kimi），且 v2.1.0 release 明確把 Plan Canvas、Kimi Code install target 與 self-hosted compute 串起來。Repo 為 JavaScript、MIT。
- **3W1H：**
  - **What：** Harness-native agent operating system——以 skills、agents、commands、hooks、rules、memory 與 MCP conventions 為單位的多 harness 安裝層。
  - **Why：** Claude Code／Codex／Cursor 等 harness 各自有空白，但每個新 prompt 都從零規劃／驗證很浪費 context；ECC 把這套循環做成一次安裝、多 harness 共用的工程系統。
  - **Who：** Claude Code／Codex／Cursor 等 harness 的重度使用者、需要「規劃→測試→驗證→記憶」閉環的工程團隊，以及想把 open-source 模型（Kimi K3、self-hosted）整合進 agent 流程的 builder。
  - **How：** Claude Code 直接 `/plugin marketplace add https://github.com/affaan-m/ECC` 後 `/plugin install ecc@ecc`，再選擇性 `cp -R rules/<stack> ~/.claude/rules/ecc/`；Codex 走 `git clone` + `bash scripts/sync-ecc-to-codex.sh`；最低門檻可 `npx ecc-install --profile minimal --target claude`，甚至 `npx ecc consult "security reviews" --target claude` 讓 installer 自己挑元件。
- **安裝方式：** **npm/pnpm/yarn/npx：** `npx ecc-install --profile minimal --target claude`（無需 clone）；`npm install -g ecc-universal`（最新版 2.1.0）也可走通用入口。**Claude Code plugin：** `/plugin marketplace add https://github.com/affaan-m/ECC` + `/plugin install ecc@ecc`。**Codex：** `git clone https://github.com/affaan-m/ECC && cd ECC && npm install && bash scripts/sync-ecc-to-codex.sh`。**Shell installer：** clone 後 `./install.sh --profile <core|minimal|full> --target <claude|codex|cursor|...>`；Windows 還有 `.\install.ps1`。**進階：** `npx ecc consult "..."` 先讓 advisor 給安裝計畫再執行。README 未列 pip/uv/poetry。
- **Repo metadata：** Description `The agent harness performance optimization system. Skills, instincts, memory, security, and research-first development for Claude Code, Codex, Opencode, Cursor and beyond.`；Stars 235,757；Language JavaScript（README 標 Shell／TS／Python／Go／Java／Perl／Markdown 多語言）；Topics `ai-agents`, `anthropic`, `claude`, `claude-code`, `developer-tools`, `llm`, `mcp`, `productivity`；License MIT；Updated at 2026-07-30；Homepage https://ecc.tools。
- **近期 release：** **2026-07-27**；`v2.1.0`；**ECC 2.1.0: Plan Canvas, Kimi Harness, and Self-Hosted Compute**。

### MoonshotAI/Kimi-K3
- **Repo 摘要：** Kimi K3 是 Moonshot AI 開源的「Open Frontier Intelligence」：2.8T 參數、104B 啟用參數、896 experts（每次啟用 16）、93 層（69 層 KDA + 24 層 Gated MLA）、1M token context、native 多模態（text + image）的 MoE 模型，並搭配 MoonViT-V2 vision encoder 與 MXFP4/MXFP8 quantization-aware training。它主打長程編程（long-horizon coding）、agentic knowledge work 與多模態推理，並把 weights 與程式碼以自訂的「Kimi K3 License」開放（允許使用、修改、商業化，但 100M MAU／20M USD revenue 以上要在 UI 顯示 Kimi K3 標示）。Repo 在 7 月 27 日才建立，三天內衝到 6,456 stars，是今天探索清單裡最年輕也最熱的候選。
- **3W1H：**
  - **What：** 一個 open-weight、native multimodal、agentic 的 frontier-class LLM（2.8T MoE）+ reference inference/deployment code。
  - **Why：** 主流 frontier model 多鎖在雲端付費 API；Kimi K3 同步把 weights、KDA/AttnRes 架構、MXFP4 量化、vLLM／SGLang recipes 與 thinking-preserved API 模式全部公開，讓 self-host frontier intelligence 成為可行選項。
  - **Who：** frontier 開源模型研究者、ML infra／量化／MoE 架構工程師、做 long-horizon coding agent 或多模態 agent 的 builder，以及需要可控 self-hosted model 的企業。
  - **How：** 最快是直接用 `platform.kimi.ai` 上 `kimi-k3` 的 OpenAI／Anthropic-compatible API；self-host 走 vLLM（`recipes/vllm.ai/moonshotai/Kimi-K3`）、SGLang cookbook、TokenSpeed recipes；終端 agent 配 Kimi Code CLI 並用 `/model` 切到 K3；Python 端則按 `Kimi K3 Quickstart` 的 preserved-thinking 模式（要把 `reasoning_content` 與 `tool_calls` 完整回傳）呼叫 `openai` SDK。
- **安裝方式：** 未找到明確 pip/npm 安裝方式（這是 weights + reference inference code，不是 Python／JS library）。**官方 API：** `https://platform.kimi.ai` 選 `kimi-k3`，提供 OpenAI／Anthropic-compatible 介面。**Self-host：** 透過 [vLLM recipes](https://recipes.vllm.ai/moonshotai/Kimi-K3)、[SGLang cookbook](https://docs.sglang.io/cookbook/autoregressive/Moonshotai/Kimi-K3)、[TokenSpeed recipes](https://lightseek.org/tokenspeed/recipes/models#kimi-k3) 任一推理引擎跑 K3；量化為 MXFP4 weights + MXFP8 activations，QAT 從 SFT 階段開始。**Coding agent：** 安裝 [Kimi Code CLI](https://www.kimi.com/code) 後用 `/model` 切到 K3。Weights 在 [Hugging Face `moonshotai`](https://huggingface.co/moonshotai) 與 ModelScope `moonshotai`。
- **Repo metadata：** Description `Open Frontier Intelligence`；Stars 6,456；Language 未列（GitHub 主語言偵測為空，weights 與 docs 為主）；Topics 未列出；License `NOASSERTION`（實為自訂 [Kimi K3 License](LICENSE)，授權使用／修改／商業化，但 100M MAU／20M USD 月營收以上需在 UI 顯示「Kimi K3」標示，且 ≥20M USD 年營收的 Model-as-a-Service 經營者須與 Moonshot 另簽協議）；Updated at 2026-07-30；Created at 2026-07-27；Homepage https://www.kimi.com。
- **近期 release：** **未找到 GitHub release**（API `/releases/latest` 回傳 404）。weights 在 Hugging Face 與 ModelScope 上發布，docs 內只有 tech blog 與 `k3_tech_report.pdf`，未走 GitHub Releases 通道。

### kvcache-ai/AgentENV
- **Repo 摘要：** AgentENV（AENV）是給 agentic RL 訓練用的「分散式 agent environment 平台」，也是 Moonshot AI 拿來跑 Kimi K3 RL training 的底層。底層是 Firecracker microVM + overlaybd（OCI image lazy load）+ ublk + S3-compatible snapshot，號稱 boot/resume < 50 ms、pause < 100 ms、snapshot < 100 ms，並提供 E2B-compatible HTTP API 與 `aenv` CLI；同時 README 明確警告目前**沒有 authorization**，必須只跑在信任網路或加 authorization proxy。Repo 為 Rust、MIT，2,309 stars，2026-07-23 建立、v0.1.0 在 7 月 25 日發布，今天仍持續 commit。
- **3W1H：**
  - **What：** 分散式 agent sandbox／environment 平台，E2B-compatible API + `aenv` CLI。
  - **Why：** 訓練 agentic model 需要大量可快照、可 fork、可暫停、可恢復的 sandbox；傳統 container 啟動慢、隔離弱、image 預烤成本高，AgentENV 用 Firecracker microVM + overlaybd 把這幾項時間壓到 sub-100 ms 級，並把 snapshot 卸到 S3。
  - **Who：** 訓練 agentic RL 的 ML infra 團隊（特別是 Moonshot／Kimi K3 生態）、做大規模 agent eval 的 platform 工程師，以及需要 E2B-compatible sandbox 但要自己 host 的 builder。
  - **How：** 單機走 Ubuntu 24.04 + kernel 6.8+，`curl -fsSL .../scripts/install.sh | sudo bash && sudo systemctl start aenv`，或 Docker `ghcr.io/kvcache-ai/aenv-server:latest` 配 `--privileged -v /dev:/dev`；`aenv auth` 後 `aenv pull ubuntu:22.04 --name ubuntu && aenv start ubuntu`；cluster 模式見官方 deployment docs。
- **安裝方式：** 未找到明確 pip/npm library 安裝方式（這是 platform + CLI，不是 Python／JS library）。**Linux install script（Ubuntu 24.04 + kernel ≥ 6.8）：** `curl -fsSL https://raw.githubusercontent.com/kvcache-ai/AgentENV/main/scripts/install.sh | sudo bash` 後 `sudo systemctl start aenv`。**Docker：** `curl -fsSL .../scripts/docker-setup.sh | sudo bash` + `docker pull ghcr.io/kvcache-ai/aenv-server:latest` + `docker run -d --privileged -v /dev:/dev -p 8000:8000 ghcr.io/kvcache-ai/aenv-server:latest`。**CLI（Linux/macOS x86_64/arm64）：** `curl -fsSL https://raw.githubusercontent.com/kvcache-ai/AgentENV/main/scripts/install-cli.sh | bash`；第一次使用 `aenv auth`（URL 預設 `http://127.0.0.1:8000`，API key 預設 `dummy`）。**前置：** 需要 `/dev/kvm` 給 Firecracker。Docker Compose / Kubernetes cluster 與 build-from-source 見官方 Deployment docs。
- **Repo metadata：** Description `AgentENV (AENV) is a distributed platform for running agent environments at scale.`；Stars 2,309；Language Rust；Topics 未列出；License MIT；Updated at 2026-07-30；Created at 2026-07-23；Homepage https://kvcache-ai.github.io/AgentENV/。
- **近期 release：** **2026-07-25**；`v0.1.0`；**v0.1.0**。

## 重點觀察

- **release 新鮮度落差巨大**：今日清單裡 `GeoLibre v2.4.0`（2026-07-29，前一天）與 `AgentENV v0.1.0`（2026-07-25，5 天內）算得上新鮮；`ECC v2.1.0`（2026-07-27，3 天）還在當週；`AIRI v0.11.3` 已 12 天、`Kimi-K3` 完全沒有 GitHub release（weights 走 HF／ModelScope，docs 直接寫在 README）。Web 探索刻意挑了「今天仍狂熱」的兩個候選——Kimi-K3 7 月 27 才建 repo、3 天 6.4k stars，AgentENV 同週首發 v0.1.0——補上 trending top 3 偏維護型 release 的不足。
- **套件化路線呈現三種光譜**：pip/uv/poetry 派只有 `GeoLibre`（`pip install geolibre`、conda-forge、AUR、FlatPark、Microsoft Store、Google Play 多通路齊備）；npm/pnpm/yarn/npx 派由 `ECC`（`npx ecc-install`、`npm install -g ecc-universal`、Claude marketplace plugin `ecc@ecc`）獨佔；無 library 派則是 `AIRI`（binary + pnpm monorepo）、`Kimi-K3`（weights + vLLM/SGLang recipes）、`AgentENV`（install script + Docker image + Rust CLI）。換言之，今天唯一一個「給 Python 開發者 `pip install` 就能用」的，是 GeoLibre 的 Jupyter anywidget。
- **語言與生態橫切**：5 個 repo 涵蓋 TypeScript（GeoLibre、AIRI）、JavaScript（ECC）、Rust（AgentENV）與 Python（Kimi-K3 inference recipes、GeoLibre Jupyter）四種主語言；其中 TypeScript 仍是 GUI／companion／CLI 的主流，Rust 出現在需要 throughput／isolation 的 infra（AgentENV），Python 只出現在 inference／notebook 介面層——呼應「infra 走 Rust、AI 應用走 TS、分析介面走 Python」的當前分工。
- **上手門檻分層清楚**：最低是 `GeoLibre` 開瀏覽器／`pip install geolibre` 即可，`ECC` 給 Claude Code plugin / `npx ecc-install` 也是一兩行命令；中段是 `AIRI` 的 binary／pnpm dev，需要 platform-specific build；`Kimi-K3` 自架要走 vLLM／SGLang + MXFP4 量化 + 多卡 GPU；最高則是 `AgentENV`，必須 Ubuntu 24.04 + kernel 6.8 + `/dev/kvm` + privileged Docker 或裸機 systemd，且 README 自己標「尚未做 authorization、不可對外網」，是典型「自己 host 整套 RL infra」的人才會碰的層級。
- **值得追的兩個新訊號**：(1) Kimi-K3 把 weights 用自訂 license 公開，且在 release 內建 Kimi Code CLI 整合——ECC v2.1.0 也同週把「Kimi Harness」當作第一方 install target，顯示 open-source frontier model × agent harness 的整合正在收斂成一個可重複使用的 stack。(2) AgentENV 把 E2B-compatible HTTP API 暴露給 standard E2B SDK，等於在開源 RL infra 這一塊提供了一個 drop-in 替代品；它的 boot/snapshot sub-100 ms 數字若屬實，會直接影響未來 agentic RL 訓練的成本曲線，值得下週再來追 release 節奏。