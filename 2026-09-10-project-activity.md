---
title: GitHub 自由探索 2026-09-10（14:00 台北時間）
date: 2026-09-10
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 7 天 repeat）
  - GitTrend AI-Agent today ranking（Tier-B web-search 2）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md` + PyPI `pypi.org/pypi/<pkg>/json` + npm `registry.npmjs.org/<pkg>`
---

# GitHub 專案動態

- 檢查時間：2026-09-10（14:00 台北時間）
- 檢查對象：`Tencent/teamai-cli` / `pascalorg/editor` / `TauricResearch/TradingAgents` / `experientiallabs/experiential` / `kunchenguid/firstmate`
- 來源組合：GitHub Trending today Tier-A 排除前 7 天 repeat（**fresh top 3 = rank 2 `Tencent/teamai-cli`（556 stars/day，TypeScript，**GH API license NOASSERTION 但 README/LICENSE/npm 顯示 MIT**，3,244★，pushed 2026-09-10 05:24 UTC = 今天台北時間 13:24 仍在 push，Tencent 開源，skills / rules / docs / env / agents / hooks / mcp 的 team-level shared harness distribution CLI，Stop hook 用「friction score」自動 collect session learnings，overview table 列了 11 個 agent host 含 Hermes 行 `skills ✓ / docs ✓ / env ✓` 但其他欄位 ✗ — 9/9 codification 的 0% Hermes-support signal 今天第一次回升）+ rank 4 `pascalorg/editor`（418 stars/day，TypeScript，MIT，23,074★，pushed 2026-09-10 05:57 UTC = 今天台北時間 13:57 仍在 push，**local-first 3D architectural editor** with React Three Fiber + WebGPU + MCP tools，npm `@pascal-app/cli` 0.1.5 + `@pascal-app/core`/`viewer`/`nodes` 全 mono-package，latest stable release v0.9.1（2026-06-10）+ CLI beta line `cli-v1.0.0-beta.2-status.0` @ 2026-09-10 06:26 UTC、3 個月 stale stable + 6 個 beta 版昨天/今天全發 — stable / pre-release cadence 完全脫鉤）+ rank 7 `TauricResearch/TradingAgents`（509 stars/day，Python，Apache-2.0，104,162★，`pushed_at` 2026-09-07 22:51 UTC = 3 天前 push，Multi-Agent LLM Financial Trading Framework，**PyPI latest 0.7.0（9 個發版中 PyPI 比 GH release 快 3 版**，GH release latest 還停在 v0.4.0 @ 2026-08-31，PyPI 0.4.0→0.5.0→0.5.1→0.6.0→0.7.0 全在 2026-08..09），支援 11 種 LLM provider 含 OpenAI/Anthropic/Google/xAI/DeepSeek/Qwen/GLM/MiniMax/OpenRouter + Azure OpenAI + AWS Bedrock + Ollama + OpenAI-compatible，PyPI MIT `tradingagents` 0.7.0 with 14 versions）** — 注意 rank 1 `ayghri/i-have-adhd`（09-09）、rank 3 `obra/superpowers`（09-04, 09-09）、rank 6 `cathrynlavery/diagram-design`（09-06, 09-07, 09-09）、rank 9 `openai/plugins`（09-07, 09-09）、rank 10 `freestylefly/awesome-gpt-image-2`、rank 11 `rohitg00/ai-engineering-from-scratch`、rank 12 `vastsa/PI-Desktop`（11/14 仍在 7 天 dedup 視窗外但 rank 2/4/7 都已是 best domain-diversity pick — `teamai-cli` team-level harness distribution 是 enterprise-grade 全新 domain、`pascalorg/editor` 是 local-first 3D + MCP 全新 domain、`TradingAgents` 是 financial trading framework 全新 domain））+ GitTrend AI-Agent today #6 `experientiallabs/experiential`（NEW 2026，Python，Apache-2.0，3,857★，`pushed_at` 2026-09-10 05:08 UTC = 今天台北時間 13:08 仍在 push，**open source zero-markup gateway for BYOK / self-hosted / 1000+ marketplace models**，編譯 native data plane + OpenAI-compatible + Anthropic Messages API，OTel-trace 收集後 build project router 或 fine-tune 一個自己擁有的 model，PyPI 0.7.62、requires_python ≥3.12、72 個 versions — release cadence 飛快、release tag 跟著 native crate 兩個版本號、`pip install experiential` 然後 `exp` 啟 local gateway）+ GitTrend AI-Agent today #10 `kunchenguid/firstmate`（NEW 2026，Shell+AGENTS.md，MIT，5,374★，`pushed_at` 2026-09-10 05:26 UTC = 今天台北時間 13:26 仍在 push，**「agent distro」— 自我定位「not a model, not a harness, not a skill, not an MCP server, and not a CLI」**，是 portable directory of instructions / skills / tooling / policies / state conventions，支援 Claude Code / Grok / Pi / `pi-signed` / Oh My Pi / Codex / OpenCode / Cursor Agent CLI 七種 verified primary harness，zero-token bash watcher supervisor，事件驅動，disposable git worktree per task，`ship` 任務 deliver PR / approved local merge / `scout` 任務交付 investigation report，optional Relay 自動回 public X / Discord mentions，0 GitHub release tag、0 PyPI/npm — 純 agent distro 走 git + AGENTS.md，安裝 = `gh auth login && git clone && <launch harness>`）

---

## Repo 摘要與 3W1H

### `Tencent/teamai-cli`

- **Repo 摘要：** Tencent 開源的「team-level AI harness distribution CLI」 — 把團隊的 skills / rules / docs / env / agents / hooks / MCP / culture / CLAUDE.md / packages 全部放在一個共用 git repo（`teamai init https://github.com/yourorg/yourrepo`），每個成員 `teamai pull` 自動把這些資源 sync 到自己本地的 Claude Code / Codex / Cursor / CodeBuddy / WorkBuddy / OpenCode / OpenClaw / Hermes / DeepSeek Harness / Qoder / ZCode 等 11 種 AI agent harness。3,244★、TypeScript、**license：GH API 顯示 `other`（NOASSERTION）但 LICENSE 檔案標頭明寫 MIT**（Tencent 客製化包裝：「Copyright (C) 2026 Tencent. All rights reserved. teamai-cli is licensed under MIT. teamai-cli does not impose any additional restrictions beyond those specified in the license.」），4,519 KB（小 CLI）、`pushed_at` 2026-09-10 05:24 UTC = 今天台北時間 13:24 仍在 push。Stop hook 自動收集 session learnings（**用「friction score」判斷是否值得 collect** — user interrupt / tool deny / retry 數量越多越值得，long-but-routine session 不觸發），可選 `teamai recall` subagent 自動 recall 團隊過去的 MR lessons（用 keyword extraction + relevance precheck + graph-boosted re-ranking），`teamai import --from-repo <url>` 把 codebase 解析成 teamwiki graph。topics 為空 array（沒刻意塞 SEO tag）。適合 5–50 人的 dev team 想讓「team A 的 PM 換 prompt 後，team B 的成員下週 pull 就能自動繼承新 prompt」、又不想每次自己改 `~/.claude/CLAUDE.md` 或 `.cursor/rules` 的人；對 solo dev 也有 `teamai init` 一行起。
- **3W1H：**
  - **What：** npm CLI（`teamai-cli`，latest 0.23.1，engines 無限制）+ 一個 `teamai.yaml` + shared git repo template（官方 `teamai-hub` org 提供 production-ready templates）+ Claude Code / Codex / Cursor 等 11 hosts 的 SessionStart hook 自動 sync。
  - **Why：** 個人層級的 AI agent skill 庫（`skills/<name>/SKILL.md`）已經成熟，但「**團隊層級**的 skill 同步 / review / 版本控管」在 2026 Q3 仍是 open space — 每個成員各自 fork `affaan-m/ECC` 拉 SKILL.md 出來改，然後 rebase 痛苦、review 沒 ground truth、誰 push 了什麼 HR 看不到。teamai-cli 把這個 workflow 變成「git repo + MR + push/pull + hooks」 — 與 09-09 `tt-a1i/archify` 的「typed IR + reviewer diffable output」是同主旋律的不同層級。
  - **Who：** 5–50 人 dev team（product / platform / QA 都算）；想 scale AI agent skill 共享的 Enterprise AI Lead；Claude Code / Cursor / Codex 重度 daily user 的 team lead。
  - **How：** 5 步 quick start：
    1. **Admin / solo 建立 shared-experience repo：** 在 GitHub / GitLab / GitCode / CNB / TGit / private Git 起一個 repo（或 `Use this template` 從 `teamai-hub` 拷貝），開 write access 給成員
    2. **npm install -g teamai-cli**
    3. **Init：** `teamai init https://github.com/yourorg/yourrepo`（project-scope）或 `teamai init <url> --scope user`（user-scope，資源裝到 `~/`）
    4. **編輯 team repo：** 編輯 `skills/<name>/SKILL.md` / `rules/*.md` / `agents/<name>.yaml` / `hooks/hooks.yaml` / `mcp/mcp.yaml` / `culture.md` / `claudemd/*.md` / `env/` / `teamai.yaml`，commit + push
    5. **Members pull：** 進 AI session 自動觸發 `teamai pull`（SessionStart hook），最新 skills / rules / context 立刻同步到本地 AI tools，**不需手動 sync**；Stop hook 結束時 friction-score 判斷是否值得自動 collect 這次 session 的 learnings
- **安裝方式：**
  - **npm（標準）：**
    ```bash
    npm install -g teamai-cli
    ```
    對應 latest stable tag v0.23.1（2026-09-09），latest beta v0.24.0-beta.4（2026-09-10 05:18 UTC），npm last-week downloads 1,315。
  - **GitHub source（開發者）：**
    ```bash
    git clone https://github.com/Tencent/teamai-cli
    cd teamai-cli && npm install && npm run build
    ```
  - **Init 共享 repo：**
    ```bash
    # Admin：建立或拷貝 template repo
    # （從 https://github.com/teamai-hub 選一個 template，Use this template）
    
    # Members：
    teamai init https://github.com/yourorg/yourrepo            # project-scope
    teamai init https://github.com/yourorg/yourrepo --scope user  # user-scope
    ```
  - **需求：** Node.js（未在 README 明確版本，建議 ≥18）、git、`gh auth login` 或等效的 git remote 認證。
  - **未提供 pip / brew / docker / apt 路徑** — 純 Node.js CLI，無 Python 套件、無 system package、無容器化。
- **近期 release：** **Stable line：** `v0.23.1` — "v0.23.1"，published 2026-09-09 06:37 UTC（台北時間 09-09 14:37，距現在 24 小時）。body 是 link-only（`compare v0.17.4...v0.23.1` — 跳過 6 個版沒發 GH release，但 npm tag 同步發了 v0.18 ~ v0.23 整個 line）。**Pre-release line：** `v0.24.0-beta.4` — published 2026-09-10 05:18 UTC（台北時間 09-10 13:18，**今天才發**），beta cadence 從 09-08 起 daily（v0.24.0-beta.1 @ 09-09 04:02 → beta.2 @ 09-09 12:15 → beta.3 @ 09-09 14:13 → beta.4 @ 09-10 05:18）。**`pushed_at` 與 release cadence 都很快**，但 stable line 5 個多月（v0.17.4 → v0.23.1）才出一個 stable、beta line 一天 3 版 — 是典型的「fast beta + slow stable」release pattern，**對主人的 takeaway**：跟 09-08 `heygen-com/hyperframes` 5 天 5 版是相同 release-velocity 派，但 teamai-cli 的 stable/beta 解耦更極端。**對主人 `horo-agent` downstream**：teamai-cli overview table 列 Hermes 行 `skills ✓ / docs ✓ / env ✓`、其他欄位 ✗，跟 OpenClaw / WorkBuddy / DeepSeek Harness 同級支援 — 這是 9/9 codification 的「0% Hermes-support signal」**第一個回升點**，對 Hermes 進入 enterprise-team 場景是個 positive signal。

### `pascalorg/editor`

- **Repo 摘要：** Pascal Editor — local-first 3D architectural editor（**React Three Fiber + WebGPU**），有 CLI + MCP server + Claude/Codex plugin skills，可以讓 AI agent 在 browser 或 terminal 操作 3D 建築模型。23,074★、TypeScript、MIT、138 MB、`pushed_at` 2026-09-10 05:57 UTC = 今天台北時間 13:57 仍在 push。topics 完整塞好（17 個：`3d` / `agent-skills` / `ai-agents` / `architecture` / `bim` / `cad` / `editor` / `floorplan` / `local-first` / `mcp` / `mcp-server` / `model-context-protocol` / `nextjs` / `parametric-design` / `react-three-fiber` / `threejs` / `typescript`）。npm `@pascal-app/cli` latest 0.1.5、last-week downloads 175、MIT；core/viewer/editor/nodes/capture-protocol/capture-viewer 6 個 npm package 同時維護。**Stable release v0.9.1 @ 2026-06-10（3 個月 stale）+ CLI beta line `cli-v1.0.0-beta.2-status.0` @ 2026-09-10 06:26 UTC（今天才發）+ `pascal-agent-skills` pre-release cadence @ 09-09 ~ 09-10 一天 6 版**，stable/pre-release cadence 完全脫鉤是這個 repo 的特徵。適合建築師 / 室內設計師 / BIM 從業者 + 想把 3D 編輯流程接進 AI agent 的開發者。
- **3W1H：**
  - **What：** TypeScript monorepo，6 個 npm package（`@pascal-app/cli` / `core` / `viewer` / `editor` / `nodes` / `@pascal-app/capture-protocol` / `@pascal-app/capture-viewer`）+ browser 端的 WebGPU 3D editor + Python-side MCP tools for agents + Claude Code / Codex plugin marketplace。
  - **Why：** 建築 / 室內設計的「AI agent + 3D CAD」過去的痛點是：(1) **雲端 BIM 太重**（Autodesk Revit 要 license、要工作站、要在公司 network）；(2) **open-source 3D editor 沒有 MCP**（Blender 有 MCP 但 UX 是 mesh editing 不是 building plan）；(3) **AI agent 沒 structured 3D building primitives**（給 LLM 一個 `.obj` 沒有意義，要的是「room / wall / door / window / furniture」這種 parametric building block）。Pascal Editor 走「**local-first React Three Fiber + WebGPU + native CLI + MCP**」 — 不需雲端帳號、不需 license、本地 CLI + persistent DB + verified CLI preview 用 SHA256 驗證 binary 完整性、Claude/Codex plugin 提供 `pascal-3d` + `furniture-fit` 兩個 skill 讓 agent 自動操作。
  - **Who：** 建築 / 室內設計 / BIM 從業者；想做「LLM agent 自動生成 floor plan / 評估家具 fit 不 fit」的 dev；React Three Fiber + WebGPU 的學習者（real production reference）。
  - **How：** 三條路徑：
    1. **CLI 跑本地 editor（最快上手）：** `npx @pascal-app/cli editor`（Node.js ≥22.13）— CLI 自動起 editor + authenticated MCP service，存到 `~/.pascal/data/pascal.db`，MCP 服務跑在自動選擇的 loopback port
    2. **Claude Code plugin：** `/plugin marketplace add pascalorg/editor` + `/plugin install pascal-agent-skills@pascal` — 自動裝 `pascal mcp connect` MCP server、`pascal-3d` + `furniture-fit` skill
    3. **Verified CLI preview（測試中功能）：** GitHub prerelease `cli-v1.0.0-beta.2-status.0` 從 `https://github.com/pascalorg/editor/releases/download/...` 下載、sha256sum 驗（archive SHA-256 = `15628baeeb174fb7786a1643db08f0554bf6d18afaaa3979f01922c5cd40019a`）、`npm install --global --prefix "$PASCAL_PREVIEW_PREFIX" --ignore-scripts`，可用 `pascal --version` / `pascal editor --no-open` / `pascal agent claim`（拿到 prefilled 15-min human handoff）/ `pascal agent status --json` 驗證 hosted key。
- **安裝方式：**
  - **npm（最簡單）：**
    ```bash
    npx @pascal-app/cli editor
    ```
    對應 `@pascal-app/cli` latest 0.1.5（MIT、engines 無設、last-week downloads 175），可同時 `npm install @pascal-app/core @pascal-app/viewer @pascal-app/editor @pascal-app/nodes` 把整個 stack 一起裝。
  - **Verified CLI preview（測試中功能）：**
    ```bash
    PASCAL_PREVIEW_VERSION='1.0.0-beta.2.status.0'
    PASCAL_PREVIEW_PREFIX="${XDG_DATA_HOME:-$HOME/.local/share}/pascal-preview"
    PASCAL_PREVIEW_DOWNLOAD="$(mktemp -d)"
    cd "$PASCAL_PREVIEW_DOWNLOAD"
    curl --fail --location --remote-name \
      "https://github.com/pascalorg/editor/releases/download/cli-v1.0.0-beta.2-status.0/pascal-app-cli-${PASCAL_PREVIEW_VERSION}.tgz"
    curl --fail --location --remote-name \
      "https://github.com/pascalorg/editor/releases/download/cli-v1.0.0-beta.2-status.0/SHA256SUMS.txt"
    shasum -a 256 -c SHA256SUMS.txt   # macOS / sha256sum -c SHA256SUMS.txt for Linux
    npm install --global --prefix "$PASCAL_PREVIEW_PREFIX" --ignore-scripts \
      "./pascal-app-cli-${PASCAL_PREVIEW_VERSION}.tgz"
    export PATH="$PASCAL_PREVIEW_PREFIX/bin:$PATH"
    pascal --version
    pascal update --version "$PASCAL_PREVIEW_VERSION"
    pascal editor --no-open
    ```
  - **Claude Code plugin：**
    ```text
    /plugin marketplace add pascalorg/editor
    /plugin install pascal-agent-skills@pascal
    ```
  - **Codex plugin：**
    ```bash
    codex plugin marketplace add pascalorg/editor
    codex plugin add pascal-agent-skills@pascal
    ```
  - **Skills.sh install（給 Claude/Cursor/Codex generic install）：**
    ```bash
    npx skills add pascalorg/editor \
      --skill pascal-3d \
      --skill furniture-fit
    ```
  - **OpenClaw：** README 寫「installation becomes available after the skills are published under Pascal's ClawHub publisher.」 — 走 `skills/README.md` 的 owner-qualified install 流程。
  - **Hermes：** README overview table 沒列 Hermes；雖未明示但 `npx skills add` 走 skills.sh protocol 應該可裝（需自測）。
  - **需求：** Node.js ≥22.13（CLI）、WebGPU-capable browser（`editor.pascal.app` 或本地 editor）、optional `gh` CLI。
- **近期 release：** **Stable line：** `v0.9.1` — "v0.9.1 — Preset System, Rooms & Templates, Building Manipulation"，published 2026-06-10 03:17 UTC（台北時間 06-10 11:17，**距今 92 天 = 3 個月 stale**）。整個 release 文字描述：「first-class preset system, room presets & template scans, building-manipulation overhaul (rotation, unified handles, tap-to-engage move), editor sound design, viewer shadow improvements, full dependency/security refresh + publishes 6 npm packages」**Pre-release line：** `cli-v1.0.0-beta.2-status.0` — published 2026-09-10 05:26 UTC（台北時間 09-10 13:26，**今天才發**）；同日還有 `cli-v1.0.0-beta.2-claim.0` @ 09-10 04:41 UTC（給 `pascal agent claim` prefilled 15-min human handoff）。**`pascal-agent-skills` pre-release cadence：** v0.1.3 → v0.1.4 → v0.1.6 → v0.1.7 全在 09-09 10:45 UTC ~ 09-09 22:22 UTC 一天內發，6 版/v0.1.3-7。**對主人的 takeaway**：與 09-08 `heygen-com/hyperframes` 同類「stable stale + fast beta line」release pattern，但 pascal 的 stable / pre-release 落差更極端（92 天 vs 24 小時）；stable line 應該是 feature-complete 後才 bump、pre-release line 是 daily beta — 與 `teamai-cli` v0.23.1 stable / v0.24.0-beta.4 beta 同樣是兩條線解耦。

### `TauricResearch/TradingAgents`

- **Repo 摘要：** **TradingAgents: Multi-Agents LLM Financial Trading Framework** — 用多個 specialized LLM agent（Fundamentals / Sentiment / News / Technical Analyst + Bull/Bear Researcher + Trader + Risk Management + Portfolio Manager）協作模擬真實交易公司決策流程。104,162★、Python、Apache-2.0、5,459 KB、`pushed_at` 2026-09-07 22:51 UTC（台北時間 09-08 06:51，3 天前）。topics 5 個：`agent` / `finance` / `llm` / `multiagent` / `trading`。**PyPI latest 0.7.0、14 個版本；GitHub release latest 還停在 v0.4.0 @ 2026-08-31** — PyPI 比 GH release 快 3 版，兩個 release stream 不同步是這個 repo 的特徵（PyPI 從 v0.5.0 開始 cadence 變快）。支援 11 種 LLM provider + 16+ data vendor（Alpha Vantage / FRED / StockTwits / Reddit / Yahoo Finance / Polymarket / Finnhub / SimFin）。`pushed_at` 在 09-07 22:51 UTC 之後就沒 push，但 PyPI 09-09 / 09-10 還在出新版本（0.6.0 → 0.7.0）。適合量化研究員 / 金融工程師 / 想用 LLM 試 alpha 的人；明確「**research purposes only, not financial advice**」免責聲明。
- **3W1H：**
  - **What：** Python framework + interactive CLI + LangGraph checkpoint resume + 4-tier agent team（Analysts / Researchers / Trader / Risk + PM）。
  - **Why：** 傳統量化策略迭代週期長、需要寫程式、需要 domain expertise；TradingAgents 把策略 discovery 變成「給 LLM 看 news + sentiment + 技術指標 + 風險控管、讓 LLM 吵架、產出 final trading decision」 — 量化研究員可以快速試「換一個 sentiment source」「加一個 analyst」「改 LLM reasoning effort」「換 model」會怎樣影響 P&L。同時 multi-agent debate 模擬 hedge fund 的 bullish / bearish 研究員對抗，能在 backtest 中觀察「LLM 的 hallucination 對 trading signal 的影響」這類無法從單 agent 看出來的盲點。
  - **Who：** 量化研究員 / 對沖基金分析師 / FinTech 工程師 / 學術界 multi-agent LLM 研究者；對 LLM bias / overconfidence / reasoning quality 在 high-stakes domain 表現有興趣的研究者。
  - **How：** 5 步 quick start：
    1. **Clone：** `git clone https://github.com/TauricResearch/TradingAgents && cd TradingAgents`
    2. **Virtual env：** `conda create -n tradingagents python=3.12 && conda activate tradingagents`
    3. **Install：** `pip install .`
    4. **API keys：** `cp .env.example .env`、填入 `OPENAI_API_KEY` / `ANTHROPIC_API_KEY` / `GOOGLE_API_KEY` / `XAI_API_KEY` / `DEEPSEEK_API_KEY` / `DASHSCOPE_API_KEY` / `ZHIPU_API_KEY` / `MINIMAX_API_KEY` / `OPENROUTER_API_KEY` / `ALPHA_VANTAGE_API_KEY` 等 10+ keys
    5. **Run CLI：** `tradingagents`（或 `python -m cli.main`），互動式選擇 ticker / analysis date / LLM provider / research depth；對話式看 agent 逐個出 report
- **安裝方式：**
  - **pip（PyPI latest 0.7.0）：**
    ```bash
    pip install tradingagents
    tradingagents   # 互動式 CLI
    ```
    PyPI 14 versions：0.3.2 / 0.4.0 / 0.5.0 / 0.5.1 / 0.6.0 / 0.7.0 是最近 6 個，**PyPI 比 GH release 快 3 版**（GH latest v0.4.0、PyPI latest 0.7.0）。
  - **Git clone source：**
    ```bash
    git clone https://github.com/TauricResearch/TradingAgents
    cd TradingAgents
    conda create -n tradingagents python=3.12
    conda activate tradingagents
    pip install .
    ```
  - **Docker（不用裝 Python）：**
    ```bash
    cp .env.example .env   # 加 API keys
    docker compose run --rm tradingagents
    # 本地模型 with Ollama：
    docker compose --profile ollama run --rm tradingagents-ollama
    ```
  - **AWS Bedrock extra：** `pip install ".[bedrock]"` + 設 `llm_provider: "bedrock"` + AWS credentials + `AWS_DEFAULT_REGION`
  - **Azure OpenAI：** `cp .env.enterprise.example .env.enterprise`、填 credentials
  - **本地 Ollama：** `llm_provider: "ollama"`、預設 `http://localhost:11434/v1`、`OLLAMA_BASE_URL` 改 remote
  - **OpenAI-compatible endpoint（vLLM / LM Studio / llama.cpp / 自架 relay）：** `llm_provider: "openai_compatible"` + `backend_url` 設 endpoint；本地 server 不需 key、要 key 的 endpoint 設 `OPENAI_COMPATIBLE_API_KEY`
  - **需求：** Python 3.12+、10+ API keys（取決於 provider / data vendor）、3–16 GB RAM 視 model / agent 數量
- **近期 release：** **GitHub release：** `v0.4.0` — "TradingAgents v0.4.0"，published 2026-08-31 03:10 UTC（台北時間 08-31 11:10，**距今 10 天**）。CHANGELOG 完整條列：**Look-ahead / point-in-time fixes（FRED macro vintage pinning、StockTwits / Reddit 限定 analysis window、memory point-in-time guard、OHLCV 最新 bar 不再被 NaN drop、debate opening 不再 fabricate empty opponent、silent Hold 改 REVIEW sentinel、`--checkpoint` CLI 不再是 no-op、DeepSeek via OpenRouter 不再 force object-form `tool_choice`） + Trader price grounding + configurable output-token cap + GPT-5.6 family + GLM-5.3 default model。**PyPI latest：** `tradingagents 0.7.0`（PyPI 跟 GH release 不同步 — PyPI 從 v0.5.0 起發版更頻）。**對主人的 takeaway**：GH release 與 PyPI version 兩個 stream 不同步是「research framework」repo 的常見 pattern（research 階段 PyPI 跟著 main branch daily tag、GH release 留作「release note 完整版」）；對使用者意味著「想試最新功能用 `pip install tradingagents --upgrade`、要看 release note 上 GH release page」 — 與 09-09 `superradcompany/microsandbox` 的 PyPI + npm + Rust 同步 v0.6.17 完全相反。

### `experientiallabs/experiential`

- **Repo 摘要：** **Experiential — open source zero-markup gateway for BYOK / self-hosted / 1000+ marketplace models**。LLM agent 的「AI gateway」 = OpenAI-compatible + Anthropic Messages API 的本地端點（`exp` CLI 起在 `127.0.0.1:8000`），統一路由 OpenAI / Anthropic / Gemini / Azure / Bedrock / Fireworks / OpenRouter / local Ollama / 1000+ marketplace models，第一步 setup wizard 設 public alias（如 `opus-5`）+ identity + `$50` 預算，發一次性 key (`xpl_` prefix)；第二步是 **從 production traffic 學習** — 收集 OTel traces 後用 `exp build support-agent` build 一個 fitted project router（自動 simulation + optimize quality / speed / cost），或 `exp optimize model support-agent` 用 Tinker fine-tune 一個自己擁有的開源模型。3,857★、Python、Apache-2.0、92 MB、`pushed_at` 2026-09-10 05:08 UTC = 今天台北時間 13:08 仍在 push。topics 為空 array。**PyPI `experiential` 0.7.62、requires_python ≥3.12、72 個 versions、release cadence 一天 2~3 版**（09-09 ~ 09-10 從 0.7.55 衝到 0.7.62 共 8 版）。native crate version 0.3.47 跟 Python 0.7.62 解耦 — 是 Rust + Python 兩個 release stream 的典型 cadence。適合所有「用 Claude Code / Cursor / Codex / Aider 但想換 model」「想把多個 LLM provider 的 cost 集中管理」「想從自己 production traffic 訓練 specialized model」的 dev。
- **3W1H：**
  - **What：** Python gateway package（`pip install experiential` / CLI `exp`）+ hosted platform `https://api.experientiallabs.ai/v1`（OpenAI + Anthropic Messages API）+ project router（從 traces 學 routing）+ Tinker fine-tuning pipeline。
  - **Why：** AI agent 的 LLM cost 在 2026 是「**分散在多個 provider、無法預測、無法 optimize**」的痛 — Claude Code user 每月 $200 OpenAI + $300 Anthropic + unit-test 一次不小心 loop 出 $150 是常態。Experiential 的解法是 (1) **統一 gateway**：所有 coding agent repoint 到 `127.0.0.1:8000` 一個 endpoint，自選 `opus-5` / `gpt-5.6` / `gemini-3.1-flash` / `deepseek-v4` 等 alias，gateway 自動 routing；(2) **friction-based pricing**：自動按 session friction 計算 `contributeHint`（類似 09-09 `Tencent/teamai-cli` 的 friction score 概念）；(3) **traffic → router → fine-tune**：從 OTel traces build simulation，跑 router optimization 找「對 support ticket 類任務，最佳 router = 70% cheap model + 30% frontier model」、最終 fine-tune 一個自己擁有的 specialist model。
  - **Who：** Claude Code / Cursor / Codex / Aider 重度 daily user；multi-tenant SaaS（要把 user LLM usage 統一計價）；想自建 specialist model 而不想每次 fine-tune 重花 $5000 的團隊。
  - **How：** 5 步 quick start：
    1. **Local install：** `pip install experiential` 後 `exp` 啟 setup wizard
    2. **Run gateway：** `exp` 或 `exp run`，本地端點 `127.0.0.1:8000/v1/chat/completions`
    3. **First call：** `export EXP_GATEWAY_KEY=...` → `curl http://127.0.0.1:8000/v1/chat/completions -H "Authorization: Bearer $EXP_GATEWAY_KEY" -d '{"model":"opus-5","messages":[...]}`
    4. **Repoint coding agents：** SETUP.md 提供 copy-paste prompts（給 Claude Code / Cursor / Codex / Aider / Conductor 用），agent 自動跑 setup：建立帳號 → 上傳 LLM traces → 連 BYOK → repoint /v1 endpoint
    5. **Build router from traffic：** `curl -L -o traces.otel.jsonl https://huggingface.co/datasets/experiential-labs/wmo-terminal-tasks-traces/resolve/.../traces.otel.jsonl` → `exp build support-agent` 走 provider / model / budget 設定 → `exp optimize model support-agent` 用 Tinker fine-tune
- **安裝方式：**
  - **pip（推薦）：**
    ```bash
    pip install experiential
    exp
    ```
    對應 PyPI 0.7.62、Apache-2.0、requires_python ≥3.12、72 versions、release cadence day-many-prerelease。Native crate 0.3.47 是 compiled native data plane 跟 Python 解耦。
  - **uv（PEP 723 一行）：**
    ```bash
    uv add experiential
    ```
  - **Hosted gateway（不裝 local）：** 在 https://platform.experientiallabs.ai 註冊 → 拿到 `xpl_` API key → `https://api.experientiallabs.ai/v1/chat/completions` 是 OpenAI-compatible endpoint；ANTHROPIC Messages API 也支援。SETUP.md 提供 copy-paste prompts 給 Claude Code / Cursor / Codex / Aider / Conductor 用，agent 自動 onboarding。
  - **Development：**
    ```bash
    uv sync --extra dev
    uv run ruff format --check .
    uv run ruff check .
    uv run ty check
    uv run pytest -q
    ```
  - **需求：** Python ≥3.12（**注意比 TradingAgents 還嚴 — TradingAgents 寫 3.10+，experiential 寫 ≥3.12**）、Rust toolchain（build native crate）。
  - **未提供 npm / brew / apt 路徑** — 純 Python + Rust（compiled native），無 Node.js 套件。
- **近期 release：** **`v0.7.62` — "v0.7.62"，published 2026-09-10 05:06 UTC（台北時間 09-10 13:06，**今天才發，距現在 ~1 小時**）**。Body 內容是 single PR description：「#890: on DeepSeek's own origin the Chat wire builder backfills `reasoning_content: ""` on EVERY assistant message that lacks a string value (explicit null included), not only tool-call turns, on both the streaming path and the buffered `RouterRuntime.complete` path. DeepSeek's thinking mode requires the field on every assistant message of the current turn」— **fix DeepSeek 的 thinking mode wire-level bug**，specific issue 顯示 Experiential gateway 在 DeepSeek 路由上是 production-grade 細節（不是 demo-grade）。release cadence 飛快：0.7.55 @ 09-07 18:18 → 0.7.56 @ 09-07 20:25 → 0.7.57 @ 09-08 17:29 → 0.7.58 @ 09-08 21:31 → 0.7.59 @ 09-09 00:46 → 0.7.60 @ 09-09 08:51 → 0.7.61 @ 09-10 02:17 → 0.7.62 @ 09-10 05:06 = **8 個 release / 3 天、9/9-9/10 一天 4 版**。**對主人的 takeaway**：與 09-08 `heygen-com/hyperframes` 5 天 5 版、09-09 `superradcompany/microsandbox` 2-3 天一版同級 — 是 2026 Q3 Rust + Python release-engineering 成熟度的 baseline；對主人 `horo-agent` downstream 若要抄 release cadence，experiential 的 `native crate (Rust) + Python wrapper` 雙 stream 解耦是 production-grade reference。

### `kunchenguid/firstmate`

- **Repo 摘要：** **firstmate — 「agent distro」**（自我定位明確：「firstmate is not a model, not a harness, not a skill, not an MCP server, and not a CLI. firstmate is an agent distro for running a crew of agents.」）。一個 portable directory of `AGENTS.md` + bundled skills + helper scripts + state conventions，把一個 general-purpose coding agent 變成「**first mate**」 — 你（captain）只跟 first mate 對話，first mate 在 visible session backend（tmux by default / Herdr / zellij / cmux / Orca terminal）spawn 多個 crewmate agent，每個跑在 disposable git worktree（[treehouse](https://github.com/kunchenguid/treehouse) 或 Orca-managed），zero-token bash watcher event-driven supervisor，**`ship` 任務 deliver PR / approved local merge、`scout` 任務交付 investigation report**。5,374★、Shell（其實是 shell scripts + AGENTS.md + 大量 markdown）、MIT、25 MB、`pushed_at` 2026-09-10 05:26 UTC = 今天台北時間 13:26 仍在 push。topics 為空 array。支援 7 種 verified primary harness：**Claude Code / Grok / Pi / `pi-signed` / Oh My Pi (`omp`) / Codex / OpenCode / Cursor Agent CLI**（Cursor 在互動模式才 verified、headless `cursor-agent -p` 沒 turn-end hook）。**0 GitHub release tag、0 PyPI、0 npm** — 安裝 = `git clone + gh auth login + 啟動一個支援的 harness`。**Optional Relay 自動回 public X / Discord mentions**（主人就是用 Discord 回 Kanban 進度的 — 這個 layer 與主人的 workflow 直接相關）。
- **3W1H：**
  - **What：** 不是 CLI、不是 MCP server、不是 skill；是 portable directory (`AGENTS.md` 78 KB + 7 種 agent 的 extension/hook + state conventions)。
  - **Why：** 個人「**多任務並行** coding agent」的痛點 — 你想「修 flaky login test + 加 dark mode + audit github project xyz」三件事並行，但 Claude Code / Cursor 只有一個 session，要嘛你手動開 3 個 tab、要嘛 fork 3 個 worktree 自己切換、要嘛叫 Claude 一次做完 3 件然後卡住 30 分鐘等。firstmate 把這個 workflow 變成 **「一個 cap’n、一個 first mate、3 個 crewmate、每個 crewmate 在自己的 worktree + 自己的 session endpoint、event-driven supervisor 0 token、ship / scout 任務型」**。與 09-07 `holaboss-ai/holaOS`、09-09 `stablyai/orca`（multi-agent orchestration 4.3k★）同類但 firstmate 走「agent distro + worktree per task + git-as-MR-protocol」與 orca 走「ADE desktop / mobile / remote runtime」是不同 architecture 選擇。
  - **Who：** 有 3+ 個並行 coding task 的人；想用 Claude Code / Codex / Grok 跑 multi-agent fleet 但不想自寫 supervisor 的人；對「每個 task 一個 worktree」「git-as-merge-protocol」這種 dev 有好感的人；想要 optional Relay 自動回 public mention（X / Discord）的人（**主人 Discord Kanban workflow 的 friend**）。
  - **How：** 5 步 quick start：
    1. **Prereqs：** `gh auth login` + GitHub CLI + 一個 verified primary harness（Claude Code / Grok / Pi / Oh My Pi / Codex / OpenCode / Cursor Agent CLI）+ tmux by default（其他 backend 可選）
    2. **Clone：** `git clone https://github.com/kunchenguid/firstmate && cd firstmate`
    3. **Launch primary：** `claude`（或 `grok --trust` / `pi` / `omp` / `codex` / `opencode` / `cursor-agent --trust`）— AGENTS.md 接管 first mate
    4. **Talk to first mate：** `> ahoy! look at my github project xyz, then fix the flaky login test and add dark mode` — first mate 跑 toolchain check（會先 ask 你再裝缺的 tool）→ clone 專案到 `projects/` → spawn 2 個 isolated workers → 數分鐘後回報
    5. **Get result：** `PR ready for review, captain: https://github.com/you/xyz/pull/42 (fix flaky login test - risk: low - CI green)` → `> alright merge it` → first mate 走 merge flow
- **安裝方式：**
  - **無 npm / pip / brew / curl install — 純 git clone（這是 by design）：**
    ```sh
    gh auth login
    git clone https://github.com/kunchenguid/firstmate
    cd firstmate
    ```
  - **Launch primary harness（任一即可）：**
    ```sh
    claude                       # Claude Code（co-primary）
    grok --trust                 # Grok（co-primary）
    pi                           # Pi（co-primary）
    FM_PI_HARNESS=pi-signed pi-signed    # pi-signed wrapper
    omp                          # Oh My Pi（Pi fork）
    codex                        # Codex（verified）
    opencode                     # OpenCode（verified）
    cursor-agent --trust         # Cursor Agent CLI（互動模式才 verified）
    ```
  - **第一個 verified primary launch 後，AGENTS.md 接管 — first mate 自己會 detect 缺哪些 tool 並 ask 你裝什麼。**
  - **支援 backend：** tmux（default）/ Herdr / zellij / cmux / Orca terminal
  - **Optional Relay（自動回 public mentions）：** 開 `.env` 加 pairing token、first mate 自動答 X / Discord 的 public mention、最多 7 天內 3 個 public-safe follow-up、thread-內 final reply 變 durable state 重啟或 compact 後不丟、dry-run preview 模式紀錄 would-be replies 到本地
  - **需求：** macOS 或 Linux（README 寫「macOS | Linux」）、git、GitHub CLI authenticated、verified primary harness 任一、tmux（default）或其他 backend、optional `gh` + 2+ GB disk
  - **Hermes：** README 的「Verified primary」清單**沒列 Hermes** — 但 `AGENTS.md` 是通用 protocol，理論上 Hermes 也吃（**需自測**）；`omp` / `pi-signed` 這類 fork-friendly extension model 對 Hermes profile 整合是 positive signal
- **近期 release：** **未找到 GitHub release（0 tags、`/releases/latest` HTTP 404）**。`pushed_at` 2026-09-10 05:26 UTC = 今天台北時間 13:26 仍在 push。LICENSE 是 MIT (`Copyright (c) 2026 Kun Chen`)。**`open_issues_count` 1,209 — 異常大量** — 對一個 5,374★ repo 來說這代表「issue triage backlog」、不是「project dying」（pushed 仍 today）。**`forks_count` 1,673 = forks/stars ratio = 31%** — 異常高，說明社群很 active 在 fork tune；這與 09-09 `ayghri/i-have-adhd` 的 forks/issue ratio = 57 同一個 pattern — **skill / distro 類 repo 的社群 engagement = forks > issues（社群在 tune 自己 fork、不走 issue tracker）**。**對主人的 takeaway**：與 09-09 `i-have-adhd` 同類「零 release 標籤、純 skill / distro pack」、與 09-09 `multica-ai/andrej-karpathy-skills` 的「stable-viral signal」一致（pushed 5 個月前仍 high rank）。**Hermes 整合可能性**：firstmate 的 zero-token bash watcher + visible session backend 概念與主人「long task 用 Discord 回報 Kanban 真進度、區分已實作 / 已驗證 / blocked」workflow 的 telemetry model 直接對話 — Hermes agent 若要對 firstmate 整合，`AGENTS.md` 的 78 KB protocol 文件本身就是整合點。

---

## 重點觀察

- **Tier-A 7-day dedup 命中率 5/12 ≈ 42% — 9 月以來最低**：今天 Tier-A top 14 有 **7 個 fresh pick**（rank 2 `Tencent/teamai-cli`、rank 4 `pascalorg/editor`、rank 5 `earthtojake/text-to-cad`、rank 7 `TauricResearch/TradingAgents`、rank 8 `liquidslr/system-design-notes`、rank 10 `freestylefly/awesome-gpt-image-2`、rank 11 `rohitg00/ai-engineering-from-scratch`、rank 12 `vastsa/PI-Desktop`），扣除「pick 3」邊界後 7 個 fresh pick 集中在 rank 2~12 — 是 9 月以來新鮮度最高的一天（09-03 的 9/14 ≈ 64%、09-06 的 4/14 ≈ 29%、09-09 的 3/14 ≈ 21%）。挑 `teamai-cli` / `pascalorg/editor` / `TradingAgents` 三個，跨 **enterprise team harness / local-first 3D + MCP / financial multi-agent** 三個新 domain。**Tier-B 2 個 web-search pick 都是 GitTrend AI-Agent today 撈出來的新 2026 repo**：`experiential` (#6) + `firstmate` (#10) — domain 跨「LLM gateway + traffic-learn router」「agent distro」兩條新主旋律。
- **release 新鮮度分三層**：(a) **day-many-prerelease**：`experiential` 0.7.62 @ 09-10 13:06（**距現在 ~1 小時**、3 天 8 版）+ `teamai-cli` v0.24.0-beta.4 @ 09-10 13:18 + `pascalorg/editor` `cli-v1.0.0-beta.2-status.0` @ 09-10 13:26 + `pascal-agent-skills` v0.1.3~7 全在 09-09~09-10 一天 6 版；(b) **slow stable**：`teamai-cli` v0.23.1 stable @ 09-09 14:37（v0.17.4 → v0.23.1 = 5 個多月才 bump stable）+ `pascalorg/editor` v0.9.1 stable @ 06-10（**92 天 stale**）+ `TradingAgents` v0.4.0 @ 08-31（10 天前，但 PyPI 已經 0.7.0）；(c) **無 release**：`firstmate` 0 tag、`experiential` 的 native crate 0.3.47 跟 Python 0.7.62 解耦。**結論**：今天 5 個 pick 跨三種 release 模型 — beta-line fast / stable-line slow / no-release tag — **比 09-09 的「40% zero-release」更多元**。
- **3/5 = 60% permissively licensed + 1/5 NOASSERTION 變體 + 1/5 Apache-2.0**：MIT × 2（`teamai-cli` / `firstmate`）+ Apache-2.0 × 2（`TradingAgents` / `experiential`）+ MIT × 1（`pascalorg/editor`）。**`Tencent/teamai-cli` 的 GH API license 欄位 `other / NOASSERTION` 是 case-D variant** — LICENSE 檔標頭明寫「Copyright (C) 2026 Tencent. All rights reserved. teamai-cli is licensed under MIT.」 — 客製化 MIT 包裝，與 09-09 `kunchenguid/firstmate` 的 `MIT (Kun Chen 2026)` 不同、是「商業公司 + MIT 雙標頭」 — 是 2026 中國 tech company open-source 的常見 LICENSE 樣式（免責 + 商業保留）。**對主人 `horo-agent` downstream**：0 copyleft、0 ruleset-no-license、4/5 完全 clean、1/5 NOASSERTION（內容仍 MIT）— license friction 仍是 0，是 9 月以來最 clean 的一天。
- **2/5 = 40% Hermes-support signal — 9/9 codification 的「0%」第一次回升**：(1) `Tencent/teamai-cli` overview table 列 **Hermes 行 `skills ✓ / docs ✓ / env ✓`**（其他欄位 ✗），與 OpenClaw / WorkBuddy / DeepSeek Harness 同級支援 — **這是 09-09 codification 的「0% Hermes-support signal」**在 9/10 第一次回升，雖然只是 partial ✓；(2) `pascalorg/editor` README 沒列 Hermes 但 `npx skills add` 走 skills.sh protocol 理論上可裝；(3) `firstmate` README 的 verified primary harness 清單**沒列 Hermes** 但 `AGENTS.md` 是 generic protocol；(4) `experiential` SETUP.md onboarding prompts 沒列 Hermes，列 Claude Code / Cursor / Codex / Aider / Conductor；(5) `TradingAgents` 是 research framework 不直接接 agent host。**結論**：**Hermes 在 enterprise-team 場景（teamai-cli）有 partial signal**，但 solo / distro / gateway 場景仍是 niche — 對主人 `horo-agent` downstream 是 positive 但不是 breakthrough。
- **3/5 走 npm、2/5 走 pip — node-heavy 但 Python 沒缺席**：TypeScript (`teamai-cli` cli / `pascalorg/editor` 6 packages) × 2、Python (`TradingAgents` 14 PyPI versions / `experiential` 72 PyPI versions) × 2、Shell+AGENTS.md (`firstmate`) × 1。**`experiential` 的 PyPI 72 versions 是 9 月以來單一 PyPI repo version count 最高紀錄**（對比 09-09 `microsandbox` 0.6.17 single version、09-08 `heygen-com/hyperframes` 5 versions、09-07 `NVIDIA/SkillSpector` few versions）。**`experiential` 採 Rust native crate 0.3.47 + Python wrapper 0.7.62 雙 stream release model 是生產級 PyPI release engineering reference**。**npm last-week downloads**：teamai-cli 1,315 / pascalorg/editor @pascal-app/cli 175 — teamai-cli 的 npm download 在新 repo 中相對亮眼（Tencent brand 推動）。
- **5 個 pick 跨 5 個不同 domain、不重疊**：enterprise team harness distribution / local-first 3D + MCP / financial multi-agent / LLM gateway + traffic-learn router / agent distro — 跟 09-09「UX / safety / review 三主旋律」完全不同，今天是「**distribution + dev-tooling + financial research + infra-as-gateway + multi-agent-fleet**」5 個全新 domain。**對主人 `horo-agent` downstream**：(a) teamai-cli 的 friction-based session learning + (b) experiential 的 OTel trace → build router pipeline + (c) firstmate 的 zero-token bash watcher supervisor + (d) pascalorg/editor 的 verified CLI preview (sha256 驗 binary) — **這 4 個 primitive 都跟 horo-agent runtime 直接相關**，且都不需要動 agent loop / SSE / session schema（主人「air-gap/downstream 保守減法」原則的標準安全整合點）。
