---
title: GitHub 自由探索 2026-08-30（14:00 台北時間）
date: 2026-08-30
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3，全部都是 08-27/28/29 的 repeat）
  - Web search（兩輪：fresh AI agent + supply-chain security / agent infra）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md`
---

# GitHub 專案動態

- 檢查時間：2026-08-30（14:00 台北時間）
- 檢查對象：`tt-a1i/archify` / `bilawalsidhu/gods-eye-view` / `K-Dense-AI/scientific-agent-skills` / `agentscope-ai/AgentTeams` / `perplexityai/bumblebee`
- 來源組合：GitHub Trending today Tier-A top 3（**全部都是前幾天 repeat**：`tt-a1i/archify` 3902★/day = 08-28 pick #1 從 #4 升上來 + `bilawalsidhu/gods-eye-view` 1855★/day = 08-28 pick #2 + `K-Dense-AI/scientific-agent-skills` 1587★/day = 08-28 pick #3）+ web exploration 2 個 (`agentscope-ai/AgentTeams` Apache-2.0, 5509★, multi-agent OS via Matrix rooms, **Master 「Qwen + Hermes Workers」訊號命中**：README 明列 `OpenClaw, QwenPaw, and Hermes Workers coexist` ＋ `Higress AI Gateway` + matrix 整合，v1.2.3 release 8/22 = 8 天內活躍 + `perplexityai/bumblebee` Apache-2.0, 4979★, perplexityai 維護的 supply-chain 安全 scanner, 3 種 scan profile (`baseline` / `project` / `deep`), Go 1.25+ 單一 static binary 0 非 stdlib dep, v0.1.2 release 6/18)。

## Repo 摘要與 3W1H

### `tt-a1i/archify`

- **Repo 摘要：** tt-a1i 維護的 **「Turn a codebase or system description into a polished, interactive system map — directly in chat」** agent skill — 4.5 個月內從 0 衝到 31.7k★，README 稱「Agent skill for beautiful, verifiable architecture, workflow, sequence, data-flow, and lifecycle diagrams — self-contained HTML with motion and crisp export」。**核心差異化**：(1) **typed JSON IR + deterministic checks** — agent 寫 JSON IR，Archify deterministic compile 成 self-contained HTML / SVG / PNG / WebM / 1200×630 share cards；(2) **Before / Delta / After revision-verified comparison** — 比對兩個 validated snapshots 顯示 exact added, removed, changed, moved, rerouted facts（structured topology comparison 而非 fuzzy diff）；(3) **5 種 diagram types**（architecture / workflow / sequence / data-flow / lifecycle）+ **4 種 presets** + dark/light themes + built-in brand marks + finite motion；(4) **Grounded interactions** — search nodes + open revision-verified source + trace upstream/downstream authored reach + compare roles + play guided stories；(5) **Proof Lab 11 checked-in scenarios** + **real repository traced**：`mco-org/mco` at `9f1a1cf` 產生 checked map，typed source `docs/cases/mco-runtime.architecture.json` 公開；(6) **Multi-host 支援**：Cursor / Claude Code / Codex / OpenCode / DSH / Raven / 4 hosts switcher。

- **3W1H：**
  - **What：** Node.js rendering + validation system for AI coding agents。Agent 寫 typed JSON IR，Archify deterministic compile 成 self-contained HTML / SVG。Skill form factor — 不是 CLI、不是 npm library，是 agent skill plugin。
  - **Why：** 解決「架構文件 5 個月後過期 / 架構 review 沒有視覺參考 / diagram-as-code 工具的產出不是 verified grounded facts / 沒有 share card for README / 沒有 before-after delta view」的痛點。Archify 把 codebase + system description 變成 revision-verified, shareable, self-contained HTML。
  - **Who：** 想把 codebase 變成 interactive architecture diagram 的 software architect / system designer / tech writer / developer-advocate；對「diagram-as-code」現有工具 (Mermaid / PlantUML / Structurizr) 不滿意、想要 grounded + verified + interactive 輸出的 team；對「architecture comparison across two snapshots」有需求的 reviewer / release manager。
  - **How：** (a) **一行裝全部（主推）**：`npx skills add tt-a1i/archify -g` 一次性裝好，然後 prompt「Use archify to map this repository's runtime architecture.」；(b) **Per-host install**：Cursor 走 `npx -y skills add tt-a1i/archify --skill archify --agent cursor --global --copy --yes`；Codex 走 `npx skills use tt-a1i/archify@archify --agent codex`；Claude Code / OpenCode 走 `npx skills add tt-a1i/archify -g` + agent switcher (`tt-a1i.github.io/archify/start.html?agent=...`)；(c) **No agent path**：clone 後開 `examples/web-app.html` 直接 try 完整 viewer + 看 Proof Lab 11 scenarios + mco runtime case；(d) **DSH community opt-in**：`dsh plugin --profile web add @tt-a1i/archify-dsh@0.1.0`；(e) **Raven manual ZIP install**：extract `archify.zip` 到 `~/.raven/workspace/skills`。

- **安裝方式：**
  - **`npx skills add` 一行裝所有**（**主推**）：`npx skills add tt-a1i/archify -g`。
  - **Explicit per-host install**：
    - **Cursor**：`npx -y skills add tt-a1i/archify --skill archify --agent cursor --global --copy --yes`。
    - **Codex**：`npx skills use tt-a1i/archify@archify --agent codex`。
    - **Claude Code / OpenCode**：透過 `npx skills add tt-a1i/archify -g` 一次裝，agent switcher (`tt-a1i.github.io/archify/start.html?agent=...`) 提供 exact commands。
  - **DSH community opt-in**：`dsh plugin --profile web add @tt-a1i/archify-dsh@0.1.0`。
  - **Raven manual ZIP install**：下載 `archify.zip` → 解到 `~/.raven/workspace/skills`，產出 `~/.raven/workspace/skills/archify`。
  - **As interactive web (no agent)**：clone 後開 `examples/web-app.html`。

- **近期 release：** `v2.15.0` — **2026-08-17 15:50 UTC 發佈（台北時間 8/18 凌晨，11 天前）**, pre-release = false。Body 重點：「feat(sequence): add opt-in column_fit for viewBox-relative lanes」+ 「feat: add opt-in DeepSeek Harness bundle」+ 「fix: make DSH packaging portable on Windows」+ 「docs: surface DeepSeek Harness quick start」+ 「fix(cli): reject quality flags without values」。**Current development version = `v2.16.0-dev.0`**（badge 顯示, in-progress 下一版）。**Repo `pushed_at` 2026-08-30 05:24 UTC（台北時間今天 13:24, 今天 main 剛 commit）** + `created_at` 2026-04-15（**4.5 個月內從 0 衝到 31,743★**）+ 1,976 forks + 65 open issues + 99MB + 20 topics（`agent-skills` `architecture-as-code` `architecture-diagram` `claude-skill` `code-visualization` `codex` `coding-agents` `data-flow-diagram` 等） + License = MIT（純 permissive、case-A verified via file）。

### `bilawalsidhu/gods-eye-view`

- **Repo 摘要：** bilawalsidhu（前 WorldView、YouTube 5M+ 觀看數的同系列創作者）維護的 **「A spy-satellite simulator in your browser — then you realize the sources are public and the data is real」** — 2 個月內從 0 衝到 12.8k★，瀏覽器內的 photorealistic 3D globe + real-time AI agent voice control。**核心差異化**：(1) **真實 vs 模擬嚴格區分** — 客戶端 deliberate 把 flights 渲染成 one polling interval 落後 real time（可平滑插值），每個 layer 都標明 freshness state 包含 partial / delayed / simulated / unavailable；(2) **public signals 整合** — flight transponders / ship beacons / orbital elements / seismographs / public cameras 一個 globe 內全部 live；(3) **cockpit view** — 進入一架被追蹤的飛機從起飛到降落，camera 永遠 hold 住下方的 terrain，可 mid-flight 切換 NVG/FLIR/CRT/Noir/Snow 等 7 種 sensor mode；(4) **250 km contacts roster** — 在目標附近所有 live signal，click 任意一架直接 jump 進 cockpit；(5) **voice whiteboard + 28 tools** — 講「Outline the state of Texas」就畫出 actual enclosing boundary 不是 circle，講「How far is the Eiffel Tower from the Louvre」就 spawn connector arrow；(6) **8 個 Layered signal sources**：Live Flights / Satellites (ISS + orbital) / Ships / Earthquakes / Traffic / Public Cameras (projected into 3D city) / Space Missions / Environmental。

- **3W1H：**
  - **What：** Node.js + Vite + CesiumJS 構建的 browser-side 3D globe，整合多個 public live signals（ADS-B / AIS / USGS / 公開 webcams / satellite TLE）+ OpenAI 提供的 voice agent + 7 種 sensor shader。Single-page web app，不是 CLI，不是 library。
  - **Why：** 解決「open-source intelligence 是 a pile of browser tabs — signals 多但 interface 是 bottleneck」的痛點。God's Eye View 把 abundant signals 變成 **一個 place**：3D Earth 上的 cockpit 視角，帶 military HUD、AI briefing strip、live telemetry。
  - **Who：** 想把 OSINT / 公開信號變成 spatial / geographic insight 的 researcher / journalist / aviation 愛好者 / space fan；對「real-time live data + 3D visualization」有興趣的 developer；想用 voice 探索 3D 地圖的 demo / 教育 / 旅遊場景使用者。
  - **How：** (a) **quick start (主推)**：`git clone` → `.env.example` 拷貝成 `.env` 設 `GOOGLE_MAPS_API_KEY` → `npm install` → `npm run dev -- --host localhost --port 4173` → 開 `http://localhost:4173`；(b) **macOS shortcut**：`./scripts/dev-fresh.sh` 清 Vite cache + 從 Keychain 拉 keys；(c) **無程式背景用戶**：裝 Claude Code / Codex / Cursor / Antigravity 任一 agent，貼上 README 的 one-shot prompt（"clone + 設定 API key + step-by-step walk me through + billing alert"）；(d) **Keys**：唯一 metered = Google Maps（$0 on most layers，1000 free 3D-tile sessions/月），其餘 flight / ship / seismic 全 🟢 公開、TomTom traffic 是 🟡 free key、OpenAI voice 是 🟡 free key with $5 session cap。

- **安裝方式：**
  - **Node.js 24.14.x or 26.x** required（enforced by `package.json`）。
  - **`npm` 一行裝所有**：
    ```bash
    git clone https://github.com/bilawalsidhu/gods-eye-view
    cd gods-eye-view
    cp .env.example .env
    # 設定 GOOGLE_MAPS_API_KEY
    npm install
    npm run dev -- --host localhost --port 4173
    ```
  - **macOS 快速啟動**：`./scripts/dev-fresh.sh`（清 Vite cache + 從 macOS Keychain 拉 keys）。
  - **瀏覽器訪問**：`http://localhost:4173`。
  - **鍵盤快捷鍵**：`1`-`7` 切視覺樣式 / `H` HUD / `D` detection / `C` cockpit / `Esc` out。
  - **未找到明確 `pip install` / `npm install -g` 安裝方式**（整包是 source-only web app，需要 `npm install` + Vite dev server）；clone + run 是正路。

- **近期 release：** **未找到 GitHub release** — `/releases/latest` endpoint 回 `Not Found`。但 `pushed_at` 2026-08-28 00:46 UTC（台北時間昨天 08:46，2 天前 main 剛 commit）+ repo 在持續 active 維護中（12,879★、2,548 forks、115 open issues、80MB size、2 個月內從 0 衝到 12.8k★ 是 viral-tail 中段的速度）。Topics = `3d-globe` `cesium` `flight-tracking` `geospatial` `geospatial-intelligence` `gis` `osint` `photogrammetry` — 全部 geospatial/OSINT 軸。License = **`NOASSERTION`**（README 有 LICENSE 但 GitHub auto-detect 沒解析成標準 SPDX identifier — **case-G**：custom 條款，商用前需逐一驗證 LICENSE 檔內容；該 repo 主推 consumer-facing entertainment / research 用，非商用 SaaS 嵌入）。

### `K-Dense-AI/scientific-agent-skills`

- **Repo 摘要：** K-Dense Inc. 維護的 **「Turn any AI agent into an AI Scientist. The #1 Agent Skills library for science, used by 190,000+ scientists worldwide. 165 ready-to-use validated skills plus 100+ scientific databases」** — 165 個 ready-to-use scientific skills + 100+ scientific databases + 9 scientific integration skills（Benchling / DNAnexus / LatchBio / OMERO / Protocols.io / Open Notebook / Ginkgo Cloud Lab / LabArchives / Opentrons）+ 30+ analysis & communication tools + 10+ research & clinical tools。**核心差異化**：(1) **「Claude Scientific Skills」rebranded 為「Scientific Agent Skills」** — 從 Claude-only 擴展到任何支援 open `Agent Skills` (agentskills.io) standard 的 agent；(2) **coverage breadth**：bioinformatics / cheminformatics / proteomics / clinical research / healthcare AI / medical imaging / ML / materials science / geospatial / lab automation / scientific communication / multi-omics / protein engineering / agent platforms / research methodology / regulatory；(3) **「165 skills = too much context」warning** — README 明確建議「consider installing a topical subset rather than the whole collection」避免 standing context overflow；(4) **6 install paths**：`npx skills add` 標準化 installer / `gh skill` v2.90+ CLI 整合（自動目錄管理 + provenance metadata）/ `hermes skills tap add` Hermes-specific tap registry / Agent Plugins 1.0.0 (`plugin.json` + `skills/`) / 手動 `git clone` 到 `~/.agents/skills/` 或 `.agents/skills/` / Cursor symlink `~/.cursor/plugins/local/`；(5) **Security**：所有 contributions 走 review process + Cisco AI Defense Skill Scanner weekly 掃（incremental scan + 每 30 天 full rescan）+ `docs/security-report.md` 公開。

- **3W1H：**
  - **What：** 165 scientific skills + 100+ scientific databases + 9 scientific integration skills + 30+ analysis & communication tools + 10+ research & clinical tools，每個 skill 有 `SKILL.md` + code examples + reference materials + test suite — 一個能讓任何 AI coding agent 變成 AI Scientist 的 skill library。Format = open Agent Skills standard (agentskills.io) + Agent Plugins 1.0.0 (`plugin.json` + `skills/`)。
  - **Why：** 解決「AI agent 在做 science research 時需要 165 個 specialized skills / 100+ databases / 9 lab platforms 的整合路徑，但這些 documentation + API quirks + package compatibility 散落各處」的痛點。讓 domain scientists 能 `npx skills add K-Dense-AI/scientific-agent-skills` 一行安裝就能用。
  - **Who：** 在 AI agent 上跑 scientific workflow 的 life science researcher / cheminformatician / clinical researcher / 醫療 AI 工程師 / ML engineer；想給 AI coding agent 加 bioinformatics / drug discovery / medical imaging skill 的 developer；用 Pi / OpenClaw / NemoClaw / Hermes 等 agent host 做 science 的使用者。
  - **How：** (a) **主推（standards-based）**：`npx skills add K-Dense-AI/scientific-agent-skills`（Claude Code / Cowork / Codex / Gemini CLI / Antigravity / Cursor 都支援）；(b) **GitHub CLI v2.90+**：`gh skill install K-Dense-AI/scientific-agent-skills` 含 `--agent <host>` 與 `--pin v2.65.0` 版本鎖定；(c) **Hermes**：`hermes skills tap add K-Dense-AI/scientific-agent-skills`；(d) **Cursor 手動 symlink**：`mkdir -p ~/.cursor/plugins/local && ln -s "$(pwd)" ~/.cursor/plugins/local/scientific-agent-skills` 後 Reload Window；(e) **Codex**：`codex plugins install .`；(f) **手動 git clone 到 `~/.agents/skills/`** 或 `.agents/skills/`。

- **安裝方式：**
  - **`npx skills add` 一行裝所有**（**主推**，standards-based installer for 6 hosts）：
    ```bash
    npx skills add K-Dense-AI/scientific-agent-skills
    ```
  - **`gh skill` GitHub CLI v2.90+**（含 provenance metadata 供 supply-chain integrity）：
    ```bash
    gh skill install K-Dense-AI/scientific-agent-skills
    gh skill install K-Dense-AI/scientific-agent-skills scanpy   # install specific skill
    gh skill install K-Dense-AI/scientific-agent-skills --agent cursor
    gh skill install K-Dense-AI/scientific-agent-skills --pin v2.65.0   # version pin
    gh skill update --all
    ```
  - **`hermes skills tap add` Hermes-specific tap registry**：
    ```bash
    hermes skills tap add K-Dense-AI/scientific-agent-skills
    ```
  - **Agent Plugins 1.0.0（Cursor / Codex / Copilot / VS Code / Kiro）**：
    - Cursor：`mkdir -p ~/.cursor/plugins/local && ln -s "$(pwd)" ~/.cursor/plugins/local/scientific-agent-skills`，restart Cursor 或 Developer: Reload Window。
    - Codex：`codex plugins install .`。
  - **手動 `git clone` 到 agent host skill 目錄**：
    ```bash
    git clone https://github.com/K-Dense-AI/scientific-agent-skills.git ~/.agents/skills/scientific-agent-skills   # user-level
    git clone https://github.com/K-Dense-AI/scientific-agent-skills.git .agents/skills/scientific-agent-skills      # project-level
    ```
  - **Optional 依賴**：`uv pip install cisco-ai-skill-scanner`（weekly security scan 用）。

- **近期 release：** `v2.65.0` — **2026-08-29 22:55 UTC 發佈（台北時間今天 06:55，**15 小時內最最最 fresh**）**, pre-release = false, draft = false。Body：「Changes since v2.64.0: Update version to 2.65.0 and reflect skill count increase in README」+ 「fix(stable-baselines3): repair broken API reference links (#233)」+ 「Fix Rowan examples for SDK 3.1.13 (#237)」+ 「Update README to replace live webinar announcement」。**Daily-cadence SemVer bumps** — 從 08-29 到 08-30 之間已 +1 minor，顯示 active feature work in progress。**Repo `pushed_at` 2026-08-29 22:55 UTC（台北時間今天 06:55, 同步 release commit）** + `created_at` 2025-10-19（**10 個月內從 0 衝到 38,098★**）+ 3,590 forks + 15 open issues + 243MB + 19 topics（`agent-skills` `ai-scientist` `bioinformatics` `chemoinformatics` `claude` `claude-skills` `claudecode` `clinical-research` 等）+ License = MIT（純 permissive、case-A verified via file）。

### `agentscope-ai/AgentTeams`

- **Repo 摘要：** Alibaba 旗下 agentscope-ai 維護的 **「An open-source Collaborative Multi-Agent OS for transparent, human-in-the-loop task coordination via Matrix rooms」** — 7 個月內到 5.5k★，Manager-Workers 架構 + Matrix protocol 整合 + Kubernetes-native control plane + agent runtime 多廠牌混搭（OpenClaw / QwenPaw / Hermes Workers 共存）。**核心差異化**：(1) **「Agent runtime 不是 Agent logic」** — AgentTeams 不實作 agent 邏輯本身，是「orchestrate 多個 agent container」（Manager + 多個 Workers）；(2) **多 runtime 共存同一 Matrix room** — OpenClaw / QwenPaw 當 deterministic Leaders 編排任務，Hermes Workers 跑 autonomous code execution，每個 runtime 做它擅長的；(3) **MinIO shared filesystem** — 跨 agent 共享檔案系統，「significantly reducing token consumption in multi-Agent collaboration scenarios」；(4) **Higress AI Gateway** 集中 traffic + credential 隔離 — Workers 拿到的是 consumer token，真實 API key 留在 gateway，Workers 看不到 attackers 也看不到；(5) **Element IM + Tuwunel IM server（Matrix protocol-based）** — 不需要 DingTalk/Lark bot application approval，直接開 Element Web 即可 onboard；(6) **Helm chart** + Kubernetes declarative resource（Worker / Team / Human 都是 YAML CRDs）。

- **3W1H：**
  - **What：** Go + Kubernetes + Matrix protocol 構建的 multi-agent orchestration 平台 — bundled 安裝包包含 Manager + Workers + AI Gateway + Matrix server + file storage + web client。Helm chart 可在任意 K8s 部署。
  - **Why：** 解決「企業要部署 multi-agent 但要 enterprise-grade security（API key 不外洩給 Workers）、full human visibility、零配置 IM onboarding、avoid vendor lock-in」的痛點。AgentTeams 把「agent 之間的 coordination」從「每個 vendor 自己實作」標準化到 Matrix 開放 protocol。
  - **Who：** 想用 multi-agent 在企業內部做 task coordination 的 enterprise IT / DevOps team / platform engineer；對「agent 之間 deterministic + autonomous runtime 混搭」有需求的研究者；想跑 Hermes / OpenClaw / QwenPaw 多 runtime 互操作的使用者。
  - **How：** (a) **quick start (Docker)**：`bash <(curl -sSL https://raw.githubusercontent.com/agentscope-ai/AgentTeams/main/install/agentteams-install.sh)`（macOS/Linux）；(b) **installer 流程**：選 LLM provider → 貼 API key → 選 network mode（local-only / external）→ 自動拉 docker containers；(c) **訪問**：開 `http://127.0.0.1:18088` Element Web log in；(d) **Mobile**：用任意 Matrix client (Element / FluffyChat) 連自家 server；(e) **Kubernetes**：用官方 Helm chart，default profile 包含 Higress AI gateway + Tuwunel (Matrix) + MinIO + AgentTeams controller。

- **安裝方式：**
  - **`curl | bash` 一行裝所有**（**主推**，Docker Desktop / Engine 自動偵測）：
    ```bash
    # macOS / Linux
    bash <(curl -sSL https://raw.githubusercontent.com/agentscope-ai/AgentTeams/main/install/agentteams-install.sh)

    # Windows (PowerShell 7+)
    $wc=New-Object Net.WebClient
    $wc.Encoding=[Text.Encoding]::UTF8
    iex $wc.DownloadString('https://raw.githubusercontent.com/agentscope-ai/AgentTeams/main/install/agentteams-install.ps1')
    ```
  - **資源需求**：2 CPU + 4 GB RAM 最低，多 Workers 4 cores + 8 GB 推薦。
  - **升級**：
    ```bash
    bash <(curl -sSL https://raw.githubusercontent.com/agentscope-ai/AgentTeams/main/install/agentteams-install.sh)   # 最新版
    AGENTTEAMS_VERSION=v1.2.2 bash <(curl -sSL ...)   # 指定版本
    ```
  - **卸載**：`bash <(curl -fsSL https://raw.githubusercontent.com/agentscope-ai/AgentTeams/main/install/agentteams-install.sh) uninstall`
  - **Kubernetes Helm**：官方 Helm chart，default profile 含 Higress AI gateway + Tuwunel (Matrix) + MinIO + AgentTeams controller，no external deps。
  - **未找到明確 `pip install` / `go install` / `npm install` 安裝方式**（整包是 bundled platform installer）。

- **近期 release：** `v1.2.3` — **2026-08-22 00:13 UTC 發佈（台北時間 8/22 08:13, **8 天前**）**, pre-release = false, draft = false。Body：「AgentTeams v1.2.3 makes long-running Project workflows visible and controllable from the Dashboard, promotes QwenPaw as the default local runtime, and improves installation and runtime reliability. / Human-visible Project workflows: New Controller APIs and `agt` CLI / QwenPaw 2.0.1 default runtime / Manager-to-Worker custom Skill delivery with validation」。**v1.2.2 (8/8) + v1.2.1 (8/6) + v1.2.0 stable (7/30)** 在最近一個月內密集發版。**Repo `pushed_at` 2026-08-22 00:13 UTC（台北時間 8/22 08:13, 同步 release commit）** + `created_at` 2026-02-21（**6 個月內從 0 衝到 5,509★**）+ 674 forks + 231 open issues（活躍 bug tracker）+ 20MB + 2 topics（`agent-teams` `openclaw`）+ License = **Apache-2.0**（case-A2 with explicit patent grant clause、case-A verified via file）。

### `perplexityai/bumblebee`

- **Repo 摘要：** perplexityai 維護的 **「Bumblebee is a read-only inventory collector for package, extension, and developer-tool metadata on macOS and Linux developer endpoints」** — 3 個月內到 5k★，single static binary (Go 1.25+) 0 非 stdlib dep，3 種 scan profile（baseline / project / deep）+ 14+ ecosystem coverage。**核心差異化**：(1) **read-only by design** — 不執行 `npm ls` / `pip show` / `go list`，只 parse lockfile + package-manager metadata + extension manifests，response team 在做 supply-chain 調查時不需要 execute 任何 code；(2) **14+ ecosystem 覆蓋**：npm / pnpm / Yarn (Classic + Berry) / Bun / PyPI / Go modules / RubyGems / Composer / MCP JSON / agent-skill locks / VS Code / Cursor / Windsurf / VSCodium extensions / Chromium + Firefox browser extensions / Homebrew；(3) **exposure catalog 整合** — 給定 advisory 名稱 + version，bumblebee 在所有 dev endpoints 上 match on-disk metadata 給出 NDJSON component records；(4) **`selftest` 內建** — `bumblebee selftest` 用 embedded fixtures（fake package names `bumblebee-selftest-evil@0.0.0`）做 end-to-end smoke test，0 network calls，CI / fleet rollout pre-deployment gate；(5) **`bumblebee version` 版本溯源** — `-ldflags -X main.Version` override + module version + in-tree VERSION，production 輸出的每筆 record 可追溯到 specific build。

- **3W1H：**
  - **What：** Go 寫的 single static binary CLI，read-only 掃 dev endpoint 上的 lockfiles + package metadata + extension manifests + MCP configs，產出 NDJSON component records。可選配 exposure catalog 做「這台機器有沒有暴露在某個 advisory」的即時查詢。
  - **Why：** 解決「supply-chain 攻擊時 SBOM 說 what shipped、EDR 說 what ran / touched network，但 response team 需要的是『哪些 developer machines 在 on-disk metadata 上有 match』」的痛點。Bumblebee 把散落的 messy local state 結構化成 NDJSON。
  - **Who：** Security response / incident response team / CISO office / DevSecOps engineer；在做 supply-chain compromise（Tianxiong / xz-utils / Solana web3.js 那類攻擊）調查時需要 fleet-wide endpoint 盤點；想做 supply-chain 持續監控的 platform team。
  - **How：** (a) **`go install` 編 latest tag**：`go install github.com/perplexityai/bumblebee/cmd/bumblebee@latest`；(b) **`go install` pin specific tag**：`go install github.com/perplexityai/bumblebee/cmd/bumblebee@v0.1.1`；(c) **build from checkout**：`go build -o bumblebee ./cmd/bumblebee && go test ./...`；(d) **stamp version**：`go build -ldflags "-X main.Version=v0.1.1" -o bumblebee ./cmd/bumblebee`；(e) **3 種 scan profile cadence**：baseline（cron / launchd 跑的 recurring lightweight inventory）/ project（`~/code` `~/src` 等 known project workspace）/ deep（explicit `--root` on-demand incident 檢查）；(f) **`bumblebee selftest`**：跑一次 end-to-end smoke test。

- **安裝方式：**
  - **Go 1.25+ required**。**0 非 stdlib dependencies**。
  - **`go install` 編 latest tagged release**（**主推**，進 `$GOBIN`）：
    ```sh
    go install github.com/perplexityai/bumblebee/cmd/bumblebee@latest
    ```
  - **`go install` pin specific tag**：
    ```sh
    go install github.com/perplexityai/bumblebee/cmd/bumblebee@v0.1.1
    ```
  - **Build from checkout**：
    ```sh
    go build -o bumblebee ./cmd/bumblebee
    go test ./...
    ```
  - **Stamp version**（package metadata trace）：
    ```sh
    go build -ldflags "-X main.Version=v0.1.1" -o bumblebee ./cmd/bumblebee
    ```
  - **Self-test smoke**：`bumblebee selftest`（正 exit = local install 仍可偵測該偵測的）。
  - **典型 quick start**：`bumblebee scan --profile baseline > inventory.ndjson`（baseline 掃 common global/user package roots + language toolchains + editor extensions + browser extensions + MCP configs）。
  - **未找到明確 `pip install` / `npm install` / `brew install` 安裝方式**（單一 Go binary，走 `go install` 是正路）。

- **近期 release：** `v0.1.2` — **2026-06-18 15:18 UTC 發佈（台北時間 6/18 23:18, **73 天前**，repo 仍 pre-1.0 早期）**, pre-release = false, draft = false。Body：「Changelog / Add Laravel Lang exposure catalog (@adel-pplx) / Merge pull request #9 from perplexityai/psi/exposure/laravel-lang-2026-05-23 / clean up」。**v0.1.x 早期版本，semantic-versioning pre-stable**。**Release 雖 73 天前，但 repo `pushed_at` 2026-08-07 17:19 UTC（台北時間 8/8 01:19, 23 天前）** — 持續開發中但 release tag cadence 慢（pre-stable 階段 + exposure catalog 是內容更新、不需要 bump version）。**Repo `created_at` 2026-05-20（**3 個月內從 0 衝到 4,979★**）+ 447 forks + 40 open issues + **只有 191KB**（**single static binary + minimal source**）+ 3 topics（`golang` `package-inventory` `supply-chain-security`）+ License = **Apache-2.0**（case-A2 with explicit patent grant clause、case-A verified via file）。

## 重點觀察

- **Tier-A top 3 全部都是 3 天內 repeat**（`tt-a1i/archify` 08-28 pick #1 從 #4 升上來、`bilawalsidhu/gods-eye-view` 08-28 pick #2、`K-Dense-AI/scientific-agent-skills` 08-28 pick #3）— 5/15 = 33% 跨日 repeat rate 是 08-27/28/29 三天 streak 的延伸；**今天 Trending algorithm 還沒 rotate 出新 viral-tail**。Web exploration 2 個挑了 `agentscope-ai/AgentTeams`（8 天前 release、Apache-2.0、Matrix multi-runtime）+ `perplexityai/bumblebee`（73 天前 release 但 pushed_at 23 天前、supply-chain scanner）補償新鮮度。

- **3/5 permissive license milestone**（case-A + case-A2）：`tt-a1i/archify`（MIT）+ `K-Dense-AI/scientific-agent-skills`（MIT）+ `agentscope-ai/AgentTeams`（Apache-2.0）+ `perplexityai/bumblebee`（Apache-2.0）= **4/5 全 permissive**，**`bilawalsidhu/gods-eye-view` 是 `NOASSERTION`（case-G）**，custom LICENSE 條款需逐一驗證。今天 4/5 = 80% permissive 與 08-17/20/21 3-consec 5/5 permissive milestone 之後第一個 4/5 (=80%) permissive tick；對主人 horo-agent / horo-webui downstream 影響為 **1 license friction**（gods-eye-view 商用前需讀 LICENSE）。

- **5 個 repo 5 種 install type 全部不同**（今天 install type diversity 達 peak）：Type-11 agent skill marketplace（`tt-a1i/archify`）+ Type-19 `npm install` + Vite dev server（`bilawalsidhu/gods-eye-view`）+ **Type-20 plugin marketplace install with per-host command**（`K-Dense-AI/scientific-agent-skills` 跨 6 hosts：`npx skills add` + `gh skill install` + `hermes skills tap add` + Cursor symlink + Codex `plugins install` + 手動 git clone）+ Type-13 `curl | bash -s --` arg-passing + Docker bundled installer（`agentscope-ai/AgentTeams`）+ **Type-3 `go install` static binary**（`perplexityai/bumblebee`）。**5 picks = 5 distinct install types**，符合 08-21「install type diversity is the new bar」codification。

- **Release 新鮮度 tier-split（5 picks）**：超 fresh (≤1 天) = **1** (`K-Dense-AI/scientific-agent-skills` v2.65.0, 15 小時前 daily-cadence SemVer)、fresh (≤14 天) = **2** (`agentscope-ai/AgentTeams` v1.2.3 8 天前 + `tt-a1i/archify` v2.15.0 11 天前)、stale (>30 天) = **2** (`bilawalsidhu/gods-eye-view` **無 release** + `perplexityai/bumblebee` v0.1.2 73 天前但 pushed_at 23 天前 pre-stable)。**2/5 = 40% zero-GH-release** 比 08-20 的 1/5 = 20% natural floor 高，與 08-17 baseline 60% 同級。**Operational reading**：gods-eye-view 的「無 release」反映其 consumer-facing web app 走 source-only + npm install + dev server（`pushed_at` 2 天前）模式；bumblebee 的「stale release」反映其 pre-stable 0.1.x 階段 + exposure catalog 內容更新不觸發 SemVer bump。

- **Master MEMORY signal 命中**：今天 **2/5 picks**（`tt-a1i/archify` 4-host switcher + `K-Dense-AI/scientific-agent-skills` 6-host install 含 **`hermes skills tap add K-Dense-AI/scientific-agent-skills`** 明確 Hermes badge）獨立 fire Hermes-agent-host signal。**`agentscope-ai/AgentTeams` README Key Features 明列**：「OpenClaw, QwenPaw, **and Hermes Workers** coexist in the same IM room... Hermes Workers for autonomous code execution」+ v1.1.0 release blog 標題「Kubernetes-native control plane, **Hermes autonomous coding agent runtime**, 1.7 GB image shrink, agt CLI replaces shell scripts」。**3/5 picks fire Hermes-as-official-host signal** — **本日為 08-20 + 08-21 後的 MEMORY peak**（08-20 是 2/5 picks fire、08-21 是 0/5 break），**對主人 hermes-agent / horo-agent 下游** 訊號強度回到 4-tick peak 水準。`K-Dense-AI/scientific-agent-skills` 的 `hermes skills tap add` 是 **canonical install command**，可以直接抄到 horo-agent lite downstream 規劃的 skill registry 路徑。
