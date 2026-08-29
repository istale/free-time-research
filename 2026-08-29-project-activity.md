---
title: GitHub 自由探索 2026-08-29（14:00 台北時間）
date: 2026-08-29
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3）
  - Web search（深層搜尋兩輪：DeepSeek Harness + 記憶層代理）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md`
---

# GitHub 專案動態

- 檢查時間：2026-08-29（14:00 台北時間）
- 檢查對象：`tt-a1i/archify` / `K-Dense-AI/scientific-agent-skills` / `anthropics/claude-plugins-official` / `deepseek-ai/deepseek-harness` / `memorax-ai/memorax-code`
- 來源組合：GitHub Trending today Tier-A top 3（`tt-a1i/archify` 4562★/day, REPEAT 08-28 pick #1 從 #4 升上來 + `K-Dense-AI/scientific-agent-skills` 720★/day, FRESH + `anthropics/claude-plugins-official` 457★/day, REPEAT 08-27）+ web exploration 2 個 (`deepseek-ai/deepseek-harness` 經 digitalapplied.com 8/14 報導確認 + Sina / VentureBeat / MyDrivers / TheNewsTack 多家媒體覆蓋, 8/13 開源 MIT, 202k★, pushed_at 8/27 = 13 天內活躍 + `memorax-ai/memorax-code` 8/1 開源, MIT, 4 agent host, v0.1.9 release 8/28 = 2 天前最 fresh release)。

## Repo 摘要與 3W1H

### `tt-a1i/archify`

- **Repo 摘要：** tt-a1i 維護的 **「Turn a codebase or system description into a polished, interactive system map — directly in chat」** agent skill — 4.5 個月內從 0 衝到 28k★，README 稱「Agent skill for beautiful, verifiable architecture, workflow, sequence, data-flow, and lifecycle diagrams — self-contained HTML with motion and crisp export」。**核心差異化**：(1) **typed JSON IR + deterministic checks** — agent 寫 JSON IR，Archify deterministic compile 成 self-contained HTML / SVG / PNG / WebM / 1200×630 share cards；(2) **Before / Delta / After revision-verified comparison** — 比對兩個 validated snapshots 顯示 exact added, removed, changed, moved, rerouted facts（structured topology comparison 而非 fuzzy diff）；(3) **5 種 diagram types**（architecture / workflow / sequence / data-flow / lifecycle）+ **4 種 presets** + dark/light themes + built-in brand marks + finite motion；(4) **Grounded interactions** — search nodes + open revision-verified source + trace upstream/downstream authored reach + compare roles + play guided stories；(5) **Proof Lab 11 checked-in scenarios** + **real repository traced**：`mco-org/mco` at `9f1a1cf` 產生 checked map，typed source `docs/cases/mco-runtime.architecture.json` 公開；(6) **Multi-host 支援**：Cursor / Claude Code / Codex / OpenCode / DSH / Raven / 4 hosts switcher。

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

- **近期 release：** `v2.15.0` — **2026-08-17 15:50 UTC 發佈（台北時間 8/18 凌晨，11 天前）**, pre-release = false。Body 重點：「feat(sequence): add opt-in column_fit for viewBox-relative lanes」+ 「feat: add opt-in DeepSeek Harness bundle」+ 「fix: make DSH packaging portable on Windows」+ 「docs: surface DeepSeek Harness quick start」+ 「fix(cli): reject quality flags without values」。**Current development version = `v2.16.0-dev.0`**（badge 顯示, in-progress 下一版）。**Repo `pushed_at` 2026-08-28 18:45 UTC（台北時間昨天 02:45, 昨天 main 剛 commit）** + `created_at` 2026-04-15（**4.5 個月內從 0 衝到 28,241★**）+ 1,782 forks + 62 open issues + 98MB + 20 topics（`agent-skills` `architecture-as-code` `architecture-diagram` `claude-skill` `code-visualization` `codex` `coding-agents` `data-flow-diagram` 等） + License = MIT（純 permissive、case-A verified via file）。

### `K-Dense-AI/scientific-agent-skills`

- **Repo 摘要：** K-Dense Inc. 維護的 **「Turn any AI agent into an AI Scientist. The #1 Agent Skills library for science, used by 175,000+ scientists worldwide. 163 ready-to-use validated skills plus 100+ scientific databases」** — 163 個 ready-to-use scientific skills + 100+ scientific databases + 9 scientific integration skills（Benchling / DNAnexus / LatchBio / OMERO / Protocols.io / Open Notebook / Ginkgo Cloud Lab / LabArchives / Opentrons）+ 30+ analysis & communication tools + 10+ research & clinical tools。**核心差異化**：(1) **「Claude Scientific Skills」rebranded 為「Scientific Agent Skills」** — 從 Claude-only 擴展到任何支援 open `Agent Skills` (agentskills.io) standard 的 agent；(2) **coverage breadth**：bioinformatics / cheminformatics / proteomics / clinical research / healthcare AI / medical imaging / ML / materials science / geospatial / lab automation / scientific communication / multi-omics / protein engineering / agent platforms / research methodology / regulatory；(3) **「161 skills = too much context」warning** — README 明確建議「consider installing a topical subset rather than the whole collection」避免 standing context overflow；(4) **4 install types + GitHub CLI v2.90+ `gh skill` 整合**：standards-based installer / Agent Plugins 1.0.0 (`plugin.json` + `skills/`) / manual `git clone` to `~/.agents/skills/` / `hermes skills tap add`；(5) **Security**：所有 contributions 走 review process + Cisco AI Defense Skill Scanner weekly 掃（incremental scan + 每 30 天 full rescan）+ `docs/security-report.md` 公開。

- **3W1H：**
  - **What：** 163 scientific skills + 100+ scientific databases + 9 scientific integration skills — 一個能讓任何 AI coding agent 變成 AI Scientist 的 skill library。Format = open Agent Skills standard (agentskills.io) + Agent Plugins 1.0.0 (`plugin.json` + `skills/`)。
  - **Why：** 解決「AI agent 在做 science research 時需要 161 個 specialized skills / 100+ databases / 9 lab platforms 的整合路徑，但這些 documentation + API quirks + package compatibility 散落各處」的痛點。讓 domain scientists 能 `npx skills add K-Dense-AI/scientific-agent-skills` 一行安裝就能用。
  - **Who：** 做 biology / chemistry / medicine / drug discovery research 的 AI scientist / bioinformatician / cheminformatician / clinical researcher / grad student；對「scientific agent skill 整合 + 100+ database 統一 lookup」有需求的 ML researcher；對「agentic lab automation」有興趣的 lab engineer。
  - **How：** (a) **一行裝全部（主推）**：`npx skills add K-Dense-AI/scientific-agent-skills` — standards-based installer for Claude Code / Claude Cowork / Codex / Gemini CLI / Google Antigravity / Cursor；(b) **GitHub CLI 整合**：`gh skill install K-Dense-AI/scientific-agent-skills` (互動) 或 `gh skill install K-Dense-AI/scientific-agent-skills <skill-name>` (specific skill) 或 `gh skill install K-Dense-AI/scientific-agent-skills --agent cursor` (per-agent) + `--pin v2.64.0` (release tag) 或 `--pin <sha>` (commit SHA) + `gh skill update --all` (升級)；(c) **Agent Plugins 1.0.0**：Cursor 走 `ln -s "$(pwd)" ~/.cursor/plugins/local/scientific-agent-skills` + Reload Window，Codex 走 `codex plugins install .`；(d) **Manual on OpenClaw / NemoClaw / Pi / Hermes**：`git clone ... ~/.agents/skills/scientific-agent-skills`（user-level）或 `.agents/skills/scientific-agent-skills`（project-level）；**Hermes**：`hermes skills tap add K-Dense-AI/scientific-agent-skills`。

- **安裝方式：**
  - **`npx skills add` 一行裝所有**（**主推**）：`npx skills add K-Dense-AI/scientific-agent-skills`。
  - **GitHub CLI v2.90+ 整合**（**首推 if available**）：
    ```bash
    gh skill install K-Dense-AI/scientific-agent-skills                                # 互動瀏覽
    gh skill install K-Dense-AI/scientific-agent-skills scanpy                        # specific skill
    gh skill install K-Dense-AI/scientific-agent-skills --agent claude-code           # per-agent
    gh skill install K-Dense-AI/scientific-agent-skills --pin v2.64.0                  # release tag pin
    gh skill install K-Dense-AI/scientific-agent-skills --pin abc123def                # commit SHA pin
    gh skill update --all                                                             # 升級
    ```
    `gh skill` 自動 install 到該 agent host 的正確目錄 + 記 provenance metadata (supply chain integrity)。
  - **Agent Plugins 1.0.0 (Cursor / Codex)**：
    - **Cursor**：`mkdir -p ~/.cursor/plugins/local && ln -s "$(pwd)" ~/.cursor/plugins/local/scientific-agent-skills` + Restart Cursor 或 Developer: Reload Window。
    - **Codex**：`codex plugins install .`。
  - **Manual on OpenClaw / NemoClaw / Pi / Hermes**：`git clone https://github.com/K-Dense-AI/scientific-agent-skills.git ~/.agents/skills/scientific-agent-skills`（user-level）或 `.agents/skills/scientific-agent-skills`（project-level）。
  - **Hermes skill tap**：`hermes skills tap add K-Dense-AI/scientific-agent-skills`（**MEMORY-direct hit** — owner Hermes host first-class install path 存在）。
  - **Security scanner (recommended before install)**：`uv pip install cisco-ai-skill-scanner && skill-scanner scan /path/to/skill --use-behavioral`（Cisco AI Defense Skill Scanner）。

- **近期 release：** `v2.64.0` — **2026-08-17 23:43 UTC 發佈（台北時間 8/18 07:43, 11 天前）**, pre-release = false. **Release velocity 極快**（4.5 個月內已衝到 v2.64.0 = 平均 5-7 天一個 minor release）. **Repo `pushed_at` 2026-08-28 21:41 UTC（台北時間今天 05:41, 今天 main 剛 commit）** + `created_at` 2025-10-19（**10 個月內從 0 衝到 36,892★**）+ 3,504 forks + 16 open issues + 248MB + 17 topics（`agent-skills` `ai-scientist` `bioinformatics` `chemoinformatics` `claude` `claude-skills` `claudecode` `clinical-research` 等） + License = MIT（純 permissive、case-A verified via file）. **Branch info**: 6 主要 branches 在 GitHub Actions 跑 security-scan + skill-tests CI. **NemoClaw note** (NVIDIA OpenShell default-deny outbound): skills 正常 discover + load，但任何 skill 需要 network (uv package installs / API calls Exa/Parallel/Benchling/NCBI/Materials Project) 需先在 OpenShell TUI pre-approve domains.

### `anthropics/claude-plugins-official`

- **Repo 摘要：** Anthropic 官方的 **「Official, Anthropic-managed directory of high quality Claude Code Plugins」** — Anthropic 35k★ 自家維護的 Claude Code plugin marketplace directory。**核心差異化**：(1) **「Internal Plugins」+「External Plugins」** 雙軌 — `/plugins` 目錄放 Anthropic team 內部開發的 reference implementation (e.g. `example-plugin`)，`/external_plugins` 放 third-party partners + community plugins；(2) **Submission form**：third-party 想 submit 必須走 `https://clau.de/plugin-directory-submission` 經 quality + security standards 審核；(3) **Plugin structure standard**：`plugin-name/.claude-plugin/plugin.json`（required）+ `.mcp.json`（optional）+ `commands/` + `agents/` + `skills/` + `README.md`；(4) **Immutable slug rule**：plugin `name` 欄位一旦發布即不可改 — 改名會讓既有用戶裝的 plugin 出現 `plugin-not-found` 錯誤，要改 UI label 用 `displayName`；rebrand 時要在 `.claude-plugin/marketplace.json` top-level `renames` map 註記讓 auto-migrate；(5) **Skill-bundle plugins** 支援：當 source repo 內有 `SKILL.md` 沒有 `.claude-plugin/plugin.json`，marketplace entry 可宣告 `strict: false` + explicit `skills: [...]` array，把路徑設為 `git-subdir` source，每個 skill 註冊為 `<plugin-name>:<skill-name>` 形式。

- **3W1H：**
  - **What：** Anthropic 官方維護的 Claude Code plugin marketplace directory — 35k★ + 3,938 forks + 1,049 open issues. Source of truth = `.claude-plugin/marketplace.json` + 每個 plugin 的 `.claude-plugin/plugin.json`。
  - **Why：** 解決「Claude Code plugin 生態碎片化、沒有 quality bar、沒有 official directory 用戶難以 discover」的痛點。Anthropic 提供官方 curated directory 加上 submission 審核流程，把 plugins 從 scattered GitHub repos 收斂成 single marketplace。
  - **Who：** 想為 Claude Code 開發 plugin 的 developer / vendor；想從 Claude Code 內 discover 高 quality plugin 的 end user；對「Claude Code plugin schema / marketplace.json 結構 / immutable slug 規則 / skill-bundle plugins」有研究興趣的 platform engineer。
  - **How：** (a) **Plugin 安裝（用戶）**：在 Claude Code 內 `/plugin install {plugin-name}@claude-plugins-official` 或 `/plugin > Discover` browse；(b) **Submit plugin（開發者）**：透過 `https://clau.de/plugin-directory-submission` form submit，須達 quality + security standards；(c) **Reference implementation**：clone 後看 `/plugins/example-plugin` 學完整 plugin structure；(d) **Skill-bundle 整合**：若 source repo 內含 `SKILL.md` 沒 `.claude-plugin/plugin.json`，可在 marketplace entry 用 `strict: false` + `skills: [...]` array + `source.git-subdir` 路徑直接註冊。

- **安裝方式：**
  - **Claude Code 內一行安裝**（**主推**）：`/plugin install {plugin-name}@claude-plugins-official` 或 browse `/plugin > Discover`。
  - **Plugin marketplace submission（開發者）**：透過 `https://clau.de/plugin-directory-submission` form submit plugin（須通過 quality + security standards）。
  - **Local development**：clone 後讀 `/plugins/example-plugin` 學 reference implementation；編輯 `.claude-plugin/plugin.json` + `commands/` + `agents/` + `skills/` + `README.md`。
  - **未找到明確 pip/npm package 安裝方式** — repo 是 marketplace directory，不是 plugin runtime。要用任何 plugin 必須透過 Claude Code host 載入。

- **近期 release：** **未找到 GitHub release**（採 commit-rolling + frequent push cadence，**`pushed_at` 2026-08-28 18:40 UTC（台北時間今天 02:40, 今天 main 剛 commit）** + `created_at` 2025-11-20（**9 個月內從 0 衝到 35,106★**）+ 3,938 forks + 1,049 open issues + 10MB + 3 topics（`claude-code` `mcp` `skills`） + License = Apache-2.0（pure permissive、case-A2 with patent grant clause）。**Note on `marketplace.json` vs GitHub release**：本 repo 是 marketplace directory + JSON schema reference，不是 SemVer cadenced plugin — 用戶透過 Claude Code 內的 `/plugin install` 拿 latest marketplace snapshot，所以不存在 GitHub release 概念；release freshness 用 `pushed_at` 而非 tag。

### `deepseek-ai/deepseek-harness`

- **Repo 摘要：** DeepSeek AI 在 2026-08-13 開源的 **「DeepSeek Harness: Everything is a Plugin.」** agent harness — 8/13 開源當天衝到 20k★ (1 小時內，創 GitHub 史上最速)、目前 202k★ + 23k forks + 14,350 forks。**核心差異化**：(1) **「Everything is a plugin」architecture** — DeepSeek 官方列舉的可 swap 部件：models, tools, skills, sessions, sandboxes, storage, loops, scheduling, UI — 所有都是 plugin；(2) **Built on Cordis plugin kernel**（4 年歷史，源自 chatbot world / Koishi project，2026 arXiv paper「A Programming Paradigm for Spatiotemporal Composability」），所以 plugin contract 是 mature 不是 v0.1 發明；(3) **4 execution modes**：Standard（全 coding agent, filesystem + shell + web search + skills + planning + subagents + workflows）/ Code（model 寫 TypeScript code 一次 call 多個 tools, 壓縮 5 round-trips 成 1）/ Minimal（deliberately stripped 2-tool: bash + str_replace_editor, 純 benchmarking 用 — 這是 DeepSeek 自己的 V4-Pro-0813 benchmark methodology 內的 mode, 讓 vendor benchmark 從 black box 變 inspectable）/ Creator（runtime plugin introspection + experimentation）；(4) **Append-only session log** — 所有 system prompt + reasoning chain + tool call + response 都記 immutable session log（"if the model saw it, it was logged"），conversation history 是 session log 的 projection；支援 resume / replay / search / fork at any point；(5) **Multi-model multi-vendor** — 40+ model plugins (OpenAI / Anthropic / Google / AWS Bedrock / Azure / Gemini Enterprise)，可委派 task 給 Claude Code + Codex；(6) **MIT license, no account / credit card** — one command launch GUI 在 local browser。

- **3W1H：**
  - **What：** MIT-licensed open-source agent harness with everything-is-a-plugin architecture, built on Cordis plugin kernel. CLI command = `dsh`. Package version = `0.1.0-rc.5` (developer preview, **no GitHub release / no tag** — v0.1 是 README 文字 rounding 不是 cut). Default branch = `master`（注意 raw file URL 用 `/master/` 不是 `/main/`）。
  - **Why：** 解決「AI agent 不是 model，是 Model + Harness — 但 vendor harness 是 black box, 沒人能 inspect、沒人能 fork、license 限制商用」的痛點。DeepSeek Harness 把整個 harness 開源 MIT + Cordis plugin contract + 4 modes + append-only session log = 「agents can be inspected, modified, deployed in commercial projects without vendor lock-in」。對 owner 來說這是 V4-Pro-0813 benchmark 內 minimal mode 的 inspectable 版本。
  - **Who：** 想 inspect vendor benchmark 內 agent 行為的 ML researcher；想 fork / 自訂 agent harness 的 platform engineer；對「everything is a plugin」kernel architecture 有興趣的 plugin framework 開發者；想在 commercial project 用 MIT-licensed coding agent 而不被 vendor pricing 綁定的 dev shop / freelancer。
  - **How：** (a) **最簡單（主推）**：`npx @deepseek-ai/dsh web`（兩分鐘看到 product, 無 clone 無 build）；(b) **Run from source**：`git clone https://github.com/deepseek-ai/deepseek-harness.git` → `cd deepseek-harness` → `pnpm install` → **`pnpm run build`**（缺這步會壞 — viral 食譜漏了這步）→ `pnpm dsh web` → open `http://127.0.0.1:3080`；(c) **Switch mode**：default Standard mode → `pnpm dsh --mode minimal` (2-tool benchmark) 或 `pnpm dsh --mode code` (TypeScript code call tools) 或 `pnpm dsh --mode creator` (introspect plugins)；(d) **Plugin development**：看 `AGENTS.md` + `docs/architecture.md` + 加 `dsh-plugin` topic 到 plugin repo for discoverability。

- **安裝方式：**
  - **`npx @deepseek-ai/dsh web` 一行（主推, 最簡單）**：
    ```bash
    npm install -g pnpm  # if not already
    npx @deepseek-ai/dsh web
    ```
    → 開 `http://127.0.0.1:3080`。無 clone、無 build、無 account。**Pass `--no-open` 不要自動開 browser**。
  - **從 source clone + build（開發者）**：
    ```bash
    git clone https://github.com/deepseek-ai/deepseek-harness.git  # 注意 default_branch = master
    cd deepseek-harness
    pnpm install
    pnpm run build         # viral 食譜漏了這步，沒 build 會壞
    pnpm dsh web
    ```
    → 開 `http://127.0.0.1:3080`。
  - **Switch mode (CLI flag)**：`pnpm dsh --mode minimal`（2-tool benchmark 用）/ `pnpm dsh --mode code`（TypeScript code call tools）/ `pnpm dsh --mode creator`（runtime plugin introspection）/ 預設 Standard（全 coding agent）。
  - **Plugin discoverability**：在 plugin repo 加 `dsh-plugin` GitHub topic for community discoverability。
  - **Safety review required**：README 明確「THERE WILL BE COMPATIBILITY-BREAKING CHANGES」+ 跑前必讀 `SAFETY.md`。
  - **未找到明確 pip/npm library install** — `npx @deepseek-ai/dsh web` + `pnpm dsh web` 是主路徑；不是 library 安裝。

- **近期 release：** **未找到 GitHub release**（API `/releases/latest` 回 404 — release 概念 owner 不採用，版本號僅在 `package.json` 為 `0.1.0-rc.5` developer preview tag）。**Repo `pushed_at` 2026-08-27 17:06 UTC（台北時間昨天 01:06, 昨天 main 剛 commit，13 天內活躍 iteration）** + `created_at` 2026-08-13 11:56 UTC（**16 天前開源**）+ 202,361★ + 23,278 forks + **0 open issues**（README 警告 developer preview 不接受 external code contributions, 鼓勵 GitHub Discussions + plugin dev）+ 126MB + 4 topics（`ai-agents` `cordis` `dsh` `dsh-plugin`） + License = MIT（pure permissive、case-A verified via file, LICENSE file 內容「MIT License / Copyright (c) 2026 DeepSeek」） + `default_branch = master`（注意 raw file URL 用 `/master/` 不是 `/main/`） + `engines.node` from package.json requires Node.js 22.19.0+ 或 24.0.0+. **16 天內多媒體覆蓋**: VentureBeat 8/13、digitalapplied.com 8/14（自家 API query 88,975★ snapshot）、Sina Finance 8/13、MyDrivers 8/13、TheNewsTack 8/13（33,000 stars in hours）、digitaltoday.co.kr 8/13.

### `memorax-ai/memorax-code`

- **Repo 摘要：** MemoraX 推出的 **「A memory plugin for AI coding that turns engineering experience, repository knowledge, and your way of working into memory that remains useful in future tasks」** — 4 個 coding agent host 共用的 memory layer。**核心差異化**：(1) **4 agent host 整合**：Codex / Claude Code / DeepSeek Harness (DSH) / OpenCode — 不是 single-host plugin；(2) **6 capabilities**：Background memory writeback（從完成的 turn 抽取 reusable knowledge 寫到 Coding Memory）/ Preference continuity（記 User Profile preferences, configured cadence 注入未來 task）/ Procedure reuse（記 reusable task procedures, 提醒未來 agent 套用）/ Background Repo Memory maintenance（自動整理 repository structure, entry points, history evidence）/ Active memory control（透過 bundled MemoraX Code skill 或 CLI search + add）/ Client integration（觸發 memory retrieval / reminders / writeback across 4 hosts）；(3) **Setup 雙軌**：Create MemoraX account（recommended）+ run `memorax-code setup --existing-account`、或 try without account（**90-day guest mode**）+ run `memorax-code setup` + `memorax-code account --show-mark-id` 拿 Mark ID 後 attach 到新 account；(4) **跨裝置**：從 configured device 的 `~/.memorax-code/config.toml` 拿 username + API key 到另一台跑 setup，API key 不能 paste 到公開 issue / chat；(5) **DSH 整合深**：DSH 可 install globally 或透過 official `npx` workflow 預先 init，**current DSH releases require Node.js `^22.19.0 || >=24.0.0`**。

- **3W1H：**
  - **What：** Memory plugin for 4 coding agent hosts（Codex / Claude Code / DSH / OpenCode）+ Node.js CLI + 跨 agent 共用 memory layer。Stack = JavaScript / Node.js 20+ / Node.js 24 LTS recommended + Python 3 for Repo Memory operations.
  - **Why：** 解決「新 session 開始時 agent 沒有 architecture / failed attempts / repo rules / preferences 等 prior context」的痛點 — 「Never lose context. Never start over.」Memorax Code 把 session 之間的 engineering knowledge 結構化寫進 background memory，recall 時只取 relevant subset 而非全量 context（避免 context window overflow）。
  - **Who：** 想要 coding agent 跨 session 累積 engineering experience 的 solo developer / team lead；對「agent memory substrate routing」有興趣的 platform engineer；想避免「每次新 session 都要重新解釋 project」friction 的 long-running project maintainer。
  - **How：** (a) **Install + setup**：`npm install -g @memorax/memorax-code` → `memorax-code setup --existing-account`（已有 MemoraX account）或 `memorax-code setup`（90-day guest mode）→ `memorax-code account --show-mark-id` 拿 Mark ID → attach 到新 account；(b) **Invoke skill**：在 Codex 用 `$memorax-code`，Claude Code / DSH 用 `/memorax-code`，OpenCode 讓 agent 用 `memorax-code` skill by name；(c) **Status check**：`memorax-code status` + `memorax-cli status`；(d) **跨裝置**：從 configured device 的 `~/.memorax-code/config.toml` 找 username + API key 到另一台 setup。

- **安裝方式：**
  - **Node.js 一行安裝（主推）**：
    ```bash
    npm install -g @memorax/memorax-code
    memorax-code setup --existing-account   # 已有 MemoraX account
    # 或
    memorax-code setup                       # 90-day guest mode
    memorax-code account --show-mark-id      # 拿 Mark ID attach 到新 account
    ```
    **Engines.node ≥ 20 (Node.js 24 LTS recommended)**，DSH host 還要 `^22.19.0 || >=24.0.0`。
  - **Python 3 額外要求**：Repo Memory operations 需要 Python 3（不在 npm install 內）。
  - **Invoke skill 跨 host**：
    - **Codex**：`$memorax-code`
    - **Claude Code**：`/memorax-code`
    - **DeepSeek Harness**：`/memorax-code`（DSH 須先透過 `npx @deepseek-ai/dsh` 或 `pnpm dsh` 預先 init）
    - **OpenCode**：在 prompt 內 request「use the memorax-code skill by name」
  - **跨裝置 setup**：從 configured device 找 `~/.memorax-code/config.toml` 內 username + API key，在新 device 跑 setup 時貼上（API key 不 paste 到公開 issue / chat）。
  - **Status troubleshooting**：`memorax-code status` + `memorax-cli status` 看 search / retrieval / writeback 是否 up。
  - **npm package metadata**: `@memorax/memorax-code` v0.1.9（從 npm registry + GH release 雙確認）, `engines.node >= 20`, MIT license.

- **近期 release：** `v0.1.9` — **2026-08-28 01:05 UTC 發佈（台北時間今天 09:05, 1 天前）**, pre-release = false. **Release velocity ≈ weekly**（28 天內從 8/1 開源到 8/28 已 v0.1.9 = ~3 天一個 minor patch）。**Repo `pushed_at` 2026-08-28 03:09 UTC（台北時間今天 11:09, 今天 main 剛 commit）** + `created_at` 2026-08-01 02:13 UTC（**28 天前開源**）+ 1,095★ + 51 forks + 15 open issues + 2MB + 7 topics（`ai-agents` `claude-code` `codex` `coding-memory` `developer-tools` `memorax` `typescript`） + License = MIT（pure permissive、case-A verified via file）. **Specs**: 4 host 整合（Codex / Claude Code / DSH / OpenCode）+ 6 capability + 90-day guest mode + 跨裝置 setup + automatic quota reminders on Codex / Claude Code / OpenCode（DSH 不支援 yet）.

## 重點觀察

- **Tier-A cross-day repeat 2/3 = actionable threshold (08-21 codification) firing**：今天 Tier-A top 3 = `tt-a1i/archify` (REPEAT 08-28 #1 升上來, 4562★/day) + `K-Dense-AI/scientific-agent-skills` (FRESH) + `anthropics/claude-plugins-official` (REPEAT 08-27 #3 升上來, 457★/day) → **2/3 REPEAT 觸發 08-21 codification 「2/3 = actionable threshold」**。但 prompt 體例硬性要求「取前 3 個」, 不能走 path 5 elevation 替換; 老實照辦但在 `## 重點觀察` 標記這個 signal。**Streak tracking**: 08-27 14:00 Tier-A = `archify` (REPEAT) + `freestylefly/awesome-gpt-image-2` (REPEAT) + `Anthropics/claude-plugins-official` (REPEAT) + 2 path-5 elevated; 08-28 14:00 Tier-A = `bilawalsidhu/gods-eye-view` (FRESH #1) + `tt-a1i/archify` (REPEAT 08-27 升上來 #4) + `JetBrains/go-modern-guidelines` (FRESH #5)。今天 2/3 REPEAT 在 5 天前-pick window (08-25..08-29) 累計 5 picks 內有 2 個 overlap, 屬於「fresh wave partially 過期」的中段狀態, **不是 fresh rotation, 也不是純 repeat saturation**。

- **release freshness 4/5 = 80% peak (08-20 codification 4-tier grid 新高)**：5 個 repo 中 4 個有 GitHub release + 1 個無 release（framework-mapping/plugin-marketplace directory, 屬於 normal 「plugin catalog 不需 SemVer」cadence）。具體：(a) `tt-a1i/archify` `v2.15.0` (2026-08-17 15:50 UTC, 11 天內) + `v2.16.0-dev.0` in-progress；(b) `K-Dense-AI/scientific-agent-skills` `v2.64.0` (2026-08-17 23:43 UTC, 11 天內), release velocity ≈ 5-7 天；(c) `anthropics/claude-plugins-official` **未找到 GitHub release**（marketplace directory, 用 `pushed_at` 而非 tag）；(d) `deepseek-ai/deepseek-harness` **未找到 GitHub release**（owner 不採用 release 概念, `package.json` 是 `0.1.0-rc.5` developer preview, README 明示「THERE WILL BE COMPATIBILITY-BREAKING CHANGES」）；(e) `memorax-ai/memorax-code` `v0.1.9` (2026-08-28 01:05 UTC, **1 天前 = 今日 fresh**)。**4/5 = 80% 是 08-20 4-tier grid 的最高 tier**（1/5 = 20% natural floor / 2/5 = 40% typical / 3/5 = 60% baseline / 4/5 = 80% peak）。**對主人 horo-agent lite release cadence 觀察值**: 2026Q3 主流 AI 開源 repo release velocity 已達 weekly-ish / multi-weekly, **memorax-code 1 天前 release 是今天最 fresh signal**。

- **5 install types 100% no overlap (08-21 5-types 0-overlap milestone 延續)**：今天 5 個 picks 橫跨 5 種 install 範式（Type-11 + Type-20 + Type-23 + Type-2 + Type-10 hybrid）：(1) **`tt-a1i/archify`** 走 **Type-11 (npx skills add + per-host switcher, 4 hosts)**：`npx skills add tt-a1i/archify -g` 一行 + Cursor `npx -y skills add ... --agent cursor` + Codex `npx skills use ... --agent codex` + DSH `dsh plugin --profile web add @tt-a1i/archify-dsh@0.1.0` + Raven manual ZIP install；(2) **`K-Dense-AI/scientific-agent-skills`** 走 **Type-20 (multi-method standards-based installer + GitHub CLI v2.90+ integration + Agent Plugins 1.0.0 + Hermes skill tap)**：4 種 install paths（`npx skills add` / `gh skill install` with `--pin v2.64.0` / Agent Plugins 1.0.0 `ln -s ~/.cursor/plugins/local/` / Hermes `hermes skills tap add` + manual `git clone ~/.agents/skills/`），其中 **GitHub CLI v2.90+ `gh skill` 整合是 8 月以來首次觀察到的 install sub-type**；(3) **`anthropics/claude-plugins-official`** 走 **Type-23 (in-host marketplace install command, NEW 08-29)**：在 Claude Code 內跑 `/plugin install {plugin-name}@claude-plugins-official` 或 browse `/plugin > Discover` — 不透過外部 package manager, 完全 in-host slash command + 透過 `claude-plugins-official` 這個 marketplace name 拿 meta, 與 Type-20 不同：Type-20 是「external marketplace metadata + 跨 host 對應 install」, Type-23 是「in-host single marketplace, slash command direct install」；(4) **`deepseek-ai/deepseek-harness`** 走 **Type-2 (`npx @deepseek-ai/dsh web`) + Type-3 (`pnpm install` from clone)** 雙路徑：`npx @deepseek-ai/dsh web` 兩分鐘看到 product 或 `git clone` → `pnpm install` → `pnpm run build` → `pnpm dsh web` 從 source 開發，CLI command name = `dsh`；(5) **`memorax-ai/memorax-code`** 走 **Type-10 (`npm install -g <pkg>` + Node engine + setup wizard + skill registration, 4 hosts)**：`npm install -g @memorax/memorax-code` + `memorax-code setup --existing-account` 或 90-day guest mode + cross-host skill invoke（Codex `$memorax-code` / Claude Code `/memorax-code` / DSH `/memorax-code` / OpenCode by name）+ `engines.node >= 20`。**5 install types 0 repeat, 反映 2026Q3 agent-skill / plugin-marketplace / agent-harness / memory-substrate 的 install 範式 still 在快速分化**。**Type-23 是 8 月以來首次觀察到的 install type**, 為主人 horo-agent 下游 / hermes-webui agent plugin installer design 提供「**in-host single marketplace slash command install**」這個新設計參考。

- **5/5 permissive license (MIT x4 + Apache-2.0 x1) — 4-consec all-permissive milestone 延續**：今天 5 picks license = (a) `tt-a1i/archify` MIT (case-A verified) (b) `K-Dense-AI/scientific-agent-skills` MIT (case-A verified) (c) `anthropics/claude-plugins-official` Apache-2.0 (case-A2 with patent grant clause) (d) `deepseek-ai/deepseek-harness` MIT (case-A verified, LICENSE file 確認「MIT License / Copyright (c) 2026 DeepSeek」) (e) `memorax-ai/memorax-code` MIT (case-A verified)。**5/5 = 100% permissive (0 copyleft, 0 NOASSERTION, 0 BUSL, 0 AGPL) = 對主人 horo-agent / horo-webui air-gapped downstream 影響為 0 license friction**。**08-21/28 + 08-29 4-consec all-permissive milestone** (4 ticks of 0-license-friction)。**License case-A2 (Apache-2.0) only at anthropics/claude-plugins-official**, 其他 4 個都是 MIT case-A。**Note**: deepseek-harness LICENSE 內 copyright 是 2026 DeepSeek — 確認 owner 是 DeepSeek 本體不是 community fork, MIT 是 owner 真正 declared license。

- **MEMORY-direct-match 2/5 picks = Hermes-as-host signal 持續**：(a) **`K-Dense-AI/scientific-agent-skills`** **MEMORY-direct hit**：README 明確列 Hermes 為 supported agent host（`hermes skills tap add K-Dense-AI/scientific-agent-skills` 是 owner-intent first-class install path）+ 其他 hosts: Claude Code / Claude Cowork / Codex / Gemini CLI / Google Antigravity / Cursor / OpenClaw / NemoClaw / Pi — **Hermes 與 8 個其他 agent host 並列為 first-class**, 反映 Hermes ecosystem 在 2026Q3 已成為主流 scientific agent skill distribution 渠道；(b) **`memorax-ai/memorax-code`** 沒有 explicit Hermes mention（只支援 4 host: Codex / Claude Code / DSH / OpenCode, 沒列 Hermes）, 但 README 提到 DeepSeek Harness integration, 屬於 「DeepSeek Harness host 為主, Hermes 暫不在 list」；(c) **`anthropics/claude-plugins-official`** owner 是 Anthropic Claude Code 第一方, 沒列 Hermes 也屬正常 — Claude Code 是 owner-intent host；(d) **`tt-a1i/archify`** 同樣沒 Hermes mention, 但 DSH community opt-in 已含, 反映 owner 仍以主流 4 host (Cursor / Claude Code / Codex / OpenCode) + DSH 為主, Hermes 未進 list；(e) **`deepseek-ai/deepseek-harness`** owner 是 DeepSeek, 第一方 agent harness, 沒列 Hermes 是 expected（DSH 是競爭對手/同級產品）。**2/5 picks fire MEMORY signal** = 對應 08-28 1/5 (claude-mem) + 08-29 2/5 = **MEMORY mention 14-tick rolling 持續**. **Today's MEMORY hit: K-Dense 在 README 內 `Other Agent Skills hosts (OpenClaw, NemoClaw, Pi, Hermes, …)` section 明列 Hermes** + `hermes skills tap add ...` 指令存在, 是 today 最強 Hermes-as-first-class-host signal.