---
title: GitHub 自由探索 2026-09-09（14:00 台北時間）
date: 2026-09-09
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 7 天 repeat）
  - Web search（superradcompany/microsandbox — 本週 Rust microVM runtime trending，Apache-2.0，8.1k★，v0.6.17 @ 2026-09-04 五種 SDK（TS/Rust/Python/Go/Ruby）+ `msb` CLI；tt-a1i/archify — 本週 trending 22k stars/week JavaScript agent-skill for verifiable architecture diagrams，MIT，55k★，v2.16.0 @ 2026-08-30 + dev v2.17.0-dev.1，跨 6 hosts 官方支援）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md` + PyPI `pypi.org/pypi/microsandbox/json` + npm `registry.npmjs.org/camofox-browser` + `registry.npmjs.org/microsandbox`
---

# GitHub 專案動態

- 檢查時間：2026-09-09（14:00 台北時間）
- 檢查對象：`ayghri/i-have-adhd` / `multica-ai/andrej-karpathy-skills` / `jo-inc/camofox-browser` / `superradcompany/microsandbox` / `tt-a1i/archify`
- 來源組合：GitHub Trending today Tier-A 排除前 7 天 repeat（**fresh top 3 = `ayghri/i-have-adhd` rank 1（656★/day，Python，MIT，31k★，pushed 2026-09-08，agent skill 讓 agent 輸出「action first / numbered steps / no preamble」格式，給 ADHD user 對抗 LLM 的「buried answer」傾向）+ `multica-ai/andrej-karpathy-skills` rank 8（333★/day，無 language 欄位（純 markdown），211k★，`forrestchang/andrej-karpathy-skills` 鏡像，基於 Karpathy 2026 對 LLM coding pitfalls 的 X post 抽出 4 principle：Think Before Coding / Simplicity First / Surgical Changes / Goal-Driven Execution）** — 注意 rank 2 `cathrynlavery/diagram-design`（09-06×1, 09-07×1）、rank 3 `openai/skills`（09-07×1, 09-08×1）、rank 4 `affaan-m/ECC`（09-03..09-08×6）、rank 5 `heygen-com/hyperframes`（09-08×1）、rank 6 `coreyhaines31/marketingskills`（09-07×1, 09-08×1）、rank 7 `obra/superpowers`（09-04×1）、rank 9 `microsoft/markitdown`（09-08×1）、rank 13 `mksglu/context-mode`（09-08×1）、rank 14 `The-Swarm-Corporation/AutoHedge`（09-08×1）、rank 16 `viarotel-org/escrcpy`（09-08×1）、rank 17 `openai/plugins`（09-07×1）都已在前 7 天 picks 中排除；rank 10 `jo-inc/camofox-browser`（871★/day，JavaScript，MIT，10.6k★，pushed 2026-09-09，Camoufox engine wrapper 的 stealth headless browser server，**內建 anti-bot fingerprint spoofing at C++ level**）雖然也是 fresh，但因為 web-search Tier-B 第一個 pick `superradcompany/microsandbox` 主題相鄰、且 jo-inc 主品牌 OpenClaw 在主人專案列表（18910 linclaw）已是 OpenClaw 時代舊專案，所以選擇 `camofox-browser` 作為 Tier-A fresh #3 而非 web-search）+ Web search 2 = `superradcompany/microsandbox`（本週 Rust microVM runtime trending 第 11 名，**「easy fast local-first microVM runtime and library」**，Rust + Apache-2.0，8.1k★，`pushed_at` 2026-09-09 = 今天剛 push，平均 boot time <100ms，跨 Linux/macOS/Windows，提供 5 種 SDK（TypeScript/Rust/Python/Go/Ruby），`msb run debian` 一行起 VM，相容標準 OCI container image，`v0.6.17` @ 2026-09-04）+ `tt-a1i/archify`（本週 trending +22k stars/week 第 2 名，**Node.js 渲染 + 驗證系統 for Cursor / Claude Code / Codex / OpenCode / Raven / DeepSeek Harness**，JavaScript + MIT，55k★，`pushed_at` 2026-09-08，5 種 diagram type + 4 preset + dark/light 主題 + Before/Delta/After 驗證比較，typed JSON IR 編譯成 self-contained HTML + SVG + PNG + WebM + 1200×630 share card，`v2.16.0` @ 2026-08-30 + 開發中 `v2.17.0-dev.1`）。

---

## Repo 摘要與 3W1H

### `ayghri/i-have-adhd`

- **Repo 摘要：** 給 AI coding agent 用的「ADHD-friendly output」skill — 把 agent 預設「Great question! Let me think…」這種「buried answer」格式反過來，強制「Lead with the next action. Number multi-step tasks. End with one concrete next step.」的 10 rule 格式。31,409★、Python（其實 repo 只有 SKILL.md + README）、MIT、298 KB（小型 skill pack），`pushed_at` 2026-09-08 17:08 UTC = 昨天剛 push。topics：`adhd` / `claude-code-plugin` / `claude-skills` / `developer-tools` / `productivity`。README 用 Before / After 兩欄對照（Before = 「Great question! ... Hope this helps!」散文；After = 「Run `npm install jsonwebtoken@latest`, then edit `src/auth.ts:42`. 1. Open src/auth.ts 2. Replace verifyToken (lines 42-58) 3. Run npm test ... Next: paste the first failing line if any test fails」行動格式），效果是「Skip the agent preamble, get the diff」。適合所有「已經會 coding、只是不要 agent 浪費時間講廢話」的 dev；對 ADHD / 認知過載 / 想加速 iteration 的人更明顯。
- **3W1H：**
  - **What：** Claude Code plugin skill pack（一個 `SKILL.md` + 一個 README 的極小型 repo）。
  - **Why：** Agent 預設行為是「講一堆 contextual 散文 + 道歉 + 結尾問你要不要繼續」，這對 LLM 的 RLHF 訓練 metric 看起來「helpful」，但對真實 dev 是「buried answer」 — 真正要的是「下一步做什麼、在哪裡、改什麼」。這條 skill 把輸出格式做 10 rule 約束（rule 1: Lead with next action; rule 4: Suppress tangents; rule 6: Specific time estimates, not "a bit"; rule 9: Cap lists at 5 items; rule 10: No preamble. No recap. No closers.） — 是 2026 年 agent skill 庫裡「UX 對抗」派的代表。
  - **Who：** 任何 AI coding agent 重度用戶；ADHD / 認知過載族群；想要 agent 輸出 token-efficient 的人；code reviewer（review 別人 AI agent 產出的人）。
  - **How：** Claude plugin marketplace 安裝 — 在 Claude Code 內輸入 prompt：
    ```text
    Install the i-have-adhd skill/plugin from https://github.com/ayghri/i-have-adhd,
    refer to the repo's AGENTS.md for instructions.
    ```
    或直接 `INSTALL.md` 提供手動路徑。安裝後 `/i-have-adhd` 命令觸發這 10 rule。要 tune 規則：`claude plugin uninstall i-have-adhd` → fork → `claude plugin marketplace add <your-username>/i-have-adhd` → `claude plugin install i-have-adhd@i-have-adhd` → 重啟 Claude Code。
- **安裝方式：**
  - **Claude Code plugin（install type-20 — plugin marketplace + per-host command）：**
    ```text
    Install the i-have-adhd skill/plugin from https://github.com/ayghri/i-have-adhd,
    refer to the repo's AGENTS.md for instructions.
    ```
    在 Claude Code 內直接 paste 這段 prompt；Claude 會自己跑 marketplace add + install + reload。
  - **手動從 GitHub（per-project fallback）：** 詳見 `INSTALL.md`（repo 內提供完整 steps）
  - **Tune（自定規則）：** fork repo → 編輯 `skills/i-have-adhd/SKILL.md`（10 rules 全文都在這）→ `claude plugin uninstall i-have-adhd` → `claude plugin marketplace add <your-username>/i-have-adhd` → `claude plugin install i-have-adhd@i-have-adhd` → 重啟 Claude Code。
  - **未提供 pip / npm / brew 路徑** — 純 Claude Code plugin skill，不是 Python package。
- **近期 release：** **未找到 GitHub release**（`/releases/latest` HTTP 404）。但 `pushed_at` 2026-09-08 17:08 UTC = **昨天台北時間 01:08 仍在 push**，`open_issues` 33、`forks` 1,893（forks/issue ratio = 57 = 社群很 active 在 tune 自己 fork）。無 SemVer = 純 skill pack 不走 version release 流程，符合 agent-skill 類 repo 的 cadence（08-26 之前的 codification 也記錄 framework-mapping / skill-pack repo 是「code push ≠ SemVer bump」）。

### `multica-ai/andrej-karpathy-skills`

- **Repo 摘要：** 從 Andrej Karpathy 2026 年發的 X post（status id 2015883857489522876）抽出 4 principle，把 Claude Code 的「預設行為」改寫成「Think Before Coding / Simplicity First / Surgical Changes / Goal-Driven Execution」。211,608★、`language: None`（純 markdown）、**沒有 GitHub license 欄位**、20 KB（極小型 repo），`pushed_at` 2026-04-20（**5 個月前 — 已 stable**），但仍高踞 trending 14:00 排名 8 = **「stable viral」signal**。原作者署名 `@jiayuan_jy` + `Multica`（multica-ai 開源平台 for 跑 coding agents）。注意：GitHub 顯示 owner 是 `multica-ai/andrej-karpathy-skills`，但 README 的 install 步驟寫 `/plugin marketplace add forrestchang/andrej-karpathy-skills` — **這是同一內容的 upstream 是 `forrestchang/andrej-karpathy-skills`，multica-ai 是 mirror**。
- **3W1H：**
  - **What：** 一個 `CLAUDE.md` 規則檔案（20 KB 全部 = 純 markdown）。
  - **Why：** Karpathy 在 X post 點出 LLM coding 的 4 大盲點：「Models make wrong assumptions on your behalf and just run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should.」「They really like to overcomplicate code and APIs, bloat abstractions... implement a bloated construction over 1000 lines when 100 would do.」「They still sometimes change/remove comments and code they don't sufficiently understand as side effects.」這 4 principle 是這 4 個問題的對症答案 — 比「general CLAUDE.md 模板」更具 opinion，更能對抗 LLM 的 silent 假設傾向。
  - **Who：** Claude Code / Cursor / Codex / Hermes 等所有 AI coding agent 用戶；尤其有「Code review 過 agent 產出」經驗、被 agent「自作主張」過的開發者。
  - **How：** 三條路徑：
    1. **Claude Code plugin：** `/plugin marketplace add forrestchang/andrej-karpathy-skills` → `/plugin install andrej-karpathy-skills@karpathy-skills`
    2. **per-project CLAUDE.md：** 新專案 `curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md`；既有專案 append：`echo "" >> CLAUDE.md && curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md`
    3. **Cursor：** repo 內 `.cursor/rules/karpathy-guidelines.mdc` 已 committed；其他專案用 `CURSOR.md` 設定。
- **安裝方式：**
  - **Claude Code plugin（install type-20 — plugin marketplace + per-host command）：**
    ```
    /plugin marketplace add forrestchang/andrej-karpathy-skills
    /plugin install andrej-karpathy-skills@karpathy-skills
    ```
  - **直接抓 CLAUDE.md（per-project）：**
    ```bash
    # 新專案
    curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
    # 既有專案 append
    echo "" >> CLAUDE.md
    curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
    ```
  - **Cursor：** repo 內 `.cursor/rules/karpathy-guidelines.mdc` 直接用；其他專案見 `CURSOR.md`。
  - **未提供 pip / npm / brew 路徑** — 純 markdown 規範檔，不走 binary install。
- **近期 release：** **未找到 GitHub release**（`/releases/latest` HTTP 404、`pushed_at` 2026-04-20 = 5 個月前穩定）。Stable-viral signal：212k★ / 21k forks / 129 open issues / 4,600+ stars/week，**沒有 release 但持續被 star** — 跟 09-06 的 `humanlayer/skills`（24-day stale + 442★/day = skill-pack 類「star velocity tracks content discovery, not push cadence」signal）同一模式。Customization section 明寫「These guidelines are designed to be merged with project-specific instructions」 — 這是 1-file skill pack 的 design intent，不是 bug。

### `jo-inc/camofox-browser`

- **Repo 摘要：** 用 Camoufox（**Firefox fork with fingerprint spoofing at the C++ level** — `navigator.hardwareConcurrency`、WebGL renderers、AudioContext、screen geometry、WebRTC 全 spoof 在 JS 看到之前）做底的 anti-detection browser server，REST API for AI agents。10,659★、JavaScript、MIT、15.6 MB，`pushed_at` 2026-09-09 05:53 UTC = **今天台北時間 13:53 才 push**。topics：`ai-agent` / `anti-bot` / `antidetect-browser` / `automation` / `bot-detection` / `browser-automation` / `cloudflare-bypass` / `headless-browser` / `javascript` / `nodejs` / `playwright` / `puppeteer` / `scraping` / `stealth-browser` / `web-scraping`。Camoufox binary（~300MB）在 `npm install` 時由 `camoufox-js` package 從 `nicedayzhu/camoufox` official GitHub releases 抓（**有 integrity verification、no custom URLs、no URL shorteners、no raw IPs** — 09-06 SkillSpector 也強調同樣的 supply chain 透明要求）。主品牌是 `jo`（askjo.ai）— 「personal AI agent that runs half on your Mac, half on a dedicated cloud machine just for you」。適合所有需要 anti-bot bypass 的 agent scraping / login automation / long-running authenticated sessions use case。
- **3W1H：**
  - **What：** Node.js server（npm 套件名 `camofox-browser`，latest 2.4.7，engines node ≥20）+ 預載 Camoufox Firefox fork + OpenClaw plugin shim（`openclaw plugins install @askjo/camofox-browser`）+ agent REST API on `localhost:9377`。
  - **Why：** AI agent 要 browse 真正的 web，**Playwright 會被擋、headless Chrome 會被 fingerprint、stealth plugin 自己又變 fingerprint**（detection 2.0 把 puppeteer-extra-plugin-stealth 認成 known signature）。Camoufox 的解法是「在 C++ implementation level patch fingerprint」— 不是 JavaScript shim、不是 user agent 切換、是 `navigator.hardwareConcurrency` 在 JS 看到之前就是 spoofed value。這條 repo 把這個 engine 包成「accessibility snapshot + stable element ref + search macro」的 agent-friendly REST API，**讓 agent 拿到的是「給螢幕閱讀器用的 accessibility tree」而不是 bloated HTML** — 09-08 的 `mksglu/context-mode` 同樣在搶「context window 不要被 tool output 灌爆」這個 boundary。
  - **Who：** 做 web scraping / web automation 的 AI agent developer；被 Cloudflare / DataDome / 各種 bot detection 擋到的合規 scraping 團隊；做 authenticated long-running session（LinkedIn / Amazon / 等需要 login）的 agent builder。
  - **How：** 三條路徑：
    1. **OpenClaw plugin（社群流）：** `openclaw plugins install @askjo/camofox-browser`
    2. **手動 server：** `git clone https://github.com/jo-inc/camofox-browser && cd camofox-browser && npm install && npm start`（啟在 `http://localhost:9377`）
    3. **npx 一行起：** `npx @askjo/camofox-browser`
    Cookie import 流程：generate `CAMOFOX_API_KEY=$(openssl rand -hex 32)`、export 到 shell profile、把 Netscape-format cookie file（Chrome / Firefox extension export）放 `~/.camofox/cookies/<site>.txt`、叫 agent `Import my LinkedIn cookies from linkedin.txt`。
- **安裝方式：**
  - **OpenClaw plugin（社群流、給 OpenClaw agent 用）：**
    ```bash
    openclaw plugins install @askjo/camofox-browser
    ```
  - **手動 server 開發（給獨立部署）：**
    ```bash
    git clone https://github.com/jo-inc/camofox-browser && cd camofox-browser
    npm install && npm start
    # -> http://localhost:9377
    ```
  - **npx（快速試用）：**
    ```bash
    npx @askjo/camofox-browser
    ```
  - **npm 套件（v2.4.7，engines node ≥20，MIT）：** `npm install camofox-browser`
  - **Cookie authentication 設定（5 步）：**
    1. `openssl rand -hex 32` → 設成 `CAMOFOX_API_KEY` env
    2. 把 env 加進 shell profile / systemd / Docker env / Fly.io secrets
    3. 用 browser extension export Netscape-format cookie → 存 `~/.camofox/cookies/linkedin.txt`
    4. 預設 `~/.camofox/cookies/` 為 cookie dir；可 override `CAMOUFOX_COOKIES_DIR`
    5. 叫 agent `Import my LinkedIn cookies from linkedin.txt`
  - **Air-gapped / 自管 binary：** `CAMOUFOX_EXECUTABLE=/path/to/camoufox-bin` 設在 `npm install` 與 server start 之前；或 `npm install --ignore-scripts`（粗刀）或 `npm install --omit=optional` + 手動 `npx camoufox-js fetch`（精準）。注意 `PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD=1` **不再 skip Camoufox download**（postinstall sanitizes env locally）。
  - **未提供 pip / brew 路徑** — Node.js 服務，不走 Python / 系統套件。
- **近期 release：** **`camoufox-backup-380139564` — "Camoufox backup v152.0.4-beta.30"，published 2026-09-06 03:30 UTC（台北時間 09-06 11:30，距現在 3 天）**。這個 tag 名很奇怪 — `camoufox-backup-<SHA>` 不是 SemVer，是 `camoufox-js` package 自動從 upstream `nicedayzhu/camoufox` Camoufox engine sync 過來的 Camoufox binary backup release，**對應實際上游 Camoufox 版本是 v152.0.4-beta.30**。**這條 release 不代表 `camofox-browser` 自己的版本 cadence**，只代表上游 Camoufox engine 的 release cadence — `camofox-browser` 的 release 走 npm `2.4.7`（latest tag），對應源碼 cadence 是 `pushed_at` 2026-09-09 = 今天才 push。**對主人的 takeaway**：這個命名提醒讀者「當一個 repo 的 latest release tag 是 `<dep>-backup-<sha>`，表示那是 dependency snapshot release，不是 source release」，跟「framework-mapping / skill-pack repo 的 code push ≠ SemVer bump」是同一類 pitfall。

### `superradcompany/microsandbox`

- **Repo 摘要：** Rust 寫的「easy fast local-first microVM runtime and library」 — 給「AI agents / user code / plugins / CI jobs / dev environments / scrapers / automation」等 untrusted workloads 提供 hardware-level isolation 的 microVM，**平均 boot time <100ms**、跨 Linux/macOS/Windows、相容標準 OCI container image（Docker Hub / GHCR / 任何 OCI registry）、Docker-like workflow（image / command / shell / volume）、secrets 不進 VM（unexploitable secret keys）。8,138★、Rust、Apache-2.0、455 MB、`pushed_at` 2026-09-09 02:21 UTC = 今天台北時間 10:21 才 push。topics：`agents` / `docker` / `golang` / `linux` / `local` / `macos` / `microvm` / `nodejs` / `python` / `rust` / `sandbox` / `security` / `self-hosted` / `typescript` / `virtualization` / `vm` / `windows`。Companion repo：`superradcompany/microsandbox-mcp`（MCP server）+ `superradcompany/skills`（Agent Skills）。適合需要「讓 LLM agent 在 isolated environment 跑 code / network 訪問 host allowlist / secret 不外洩」的人 — Docker container 不夠安全（kernel shared）、gVisor / firecracker 設起來太重，microsandbox 是「**local-first 的 firecracker 替代**」。
- **3W1H：**
  - **What：** Rust microVM runtime + 5 種 language SDK（TypeScript / Rust / Python / Go / Ruby）+ `msb` CLI。
  - **Why：** AI agent 要執行 untrusted code 的「硬隔離 + 快 boot + 標準 OCI image」這個交叉點，2026 仍是 open space — Docker container 的 namespace / cgroup 隔離在 kernel-level exploit 下不夠（09-07 SkillSpector 對 supply chain 的警告同樣適用於 runtime）；firecracker 本身是 production-grade 但要自己 host KVM、寫 API、配 secret；microsandbox 把 firecracker 包成「一行 `msb run debian` 起 VM + 標準 SDK call 控 VM + 自動 secret scoping（`allowed_host: api.openai.com` 把 OPENAI_API_KEY 限定只有那個 host 的 egress 看得到，VM 本身拿不到 raw key）」。**硬體要求**：macOS Apple Silicon、Linux KVM enabled、Windows WHP enabled（Intel Mac 不支援 — 主人電腦是 VirtualMac2 M4 Max VM，理論上可跑但 VM 內 microVM 需要 nested virtualization 確認）。
  - **Who：** AI agent / coding agent 的安全執行環境 builder；multi-tenant SaaS（要把用戶 code 跑在隔離環境）；CI / sandbox-as-a-service。
  - **How：** 五條路徑：
    1. **TypeScript SDK：** `npm i microsandbox` → `import { Sandbox } from "microsandbox"; await using sb = await Sandbox.builder("my-sandbox").image("python").cpus(1).memory(512).create(); console.log((await sb.exec("python", ["-c", "print('Hello from a microVM!')"])).stdout())`
    2. **Rust SDK：** `cargo add microsandbox`
    3. **Python SDK：** `uv add microsandbox`（PyPI 0.6.17，Apache-2.0，requires_python ≥3.10）
    4. **Go SDK：** `go get github.com/superradcompany/microsandbox/sdk/go`
    5. **Ruby SDK：** `require "microsandbox"`（詳見 `sdk/ruby/README.md`）
    CLI：`npx microsandbox run debian` 或全局 `msb run debian`。
- **安裝方式：**
  - **CLI（給開發者單獨用）：**
    ```bash
    # macOS / Linux
    curl -fsSL https://install.microsandbox.dev | sh
    # Windows
    irm https://install.microsandbox.dev/windows | iex
    # brew
    brew install superradcompany/tap/microsandbox
    # npm global
    npm i -g microsandbox
    # uv tool
    uv tool install microsandbox
    # cargo
    cargo install microsandbox
    # npx（無 install 直接跑）
    npx microsandbox run debian
    ```
  - **SDK（給應用嵌入）：**
    - **TypeScript：** `npm i microsandbox`（v0.6.17，engines node ≥22）
    - **Rust：** `cargo add microsandbox`
    - **Python：** `uv add microsandbox`（PyPI 0.6.17，requires_python ≥3.10）
    - **Go：** `go get github.com/superradcompany/microsandbox/sdk/go`
    - **Ruby：** `gem install microsandbox`（詳見 `sdk/ruby/README.md`）
  - **Agent Skills / MCP（給 Claude Code / Cursor 等 AI agent 用）：**
    ```bash
    npx skills add superradcompany/skills       # skills
    claude mcp add --transport stdio microsandbox -- npx -y microsandbox-mcp   # MCP server
    ```
  - **硬體要求：** **macOS 限 Apple Silicon**（主人電腦 VirtualMac2 是 M4 Max VM — Apple Silicon VM；nested virtualization 沒驗證過，可能不可行）。**Linux 需 KVM enabled。Windows 需 WHP enabled。**
  - **Beta warning：** README 明寫「Microsandbox is still **beta software**. Expect breaking changes, missing features, and rough edges.」 — 不要用在 production critical path。
- **近期 release：** **`v0.6.17` — "v0.6.17"，published 2026-09-04 13:09 UTC（台北時間 09-04 21:09，距現在 5 天）**。**5 種 SDK + CLI 全同步 v0.6.17**（PyPI 0.6.17 + npm microsandbox 0.6.17），release cadence 約 2-3 天一版。**對主人的 takeaway**：與 09-08 `heygen-com/hyperframes` 的 5 天 5 版同級 — 這是 Rust VM runtime 的「normal cadence」（firecracker 團隊過去的 release cadence 也接近）。與 09-07 `NVIDIA/SkillSpector` 對 untrusted workload supply chain 的關注同主題（都是「該不該跑 / 怎麼安全跑」邊界），SkillSpector 是 skill-load-time gate、microsandbox 是 code-execute-time isolation。

### `tt-a1i/archify`

- **Repo 摘要：** Node.js 寫的「rendering + validation system for Cursor / Claude Code / Codex CLI / OpenCode / Raven / DeepSeek Harness」— agent 產出 typed JSON IR，Archify 把它 deterministic 編譯成 HTML/SVG，提供 5 種 diagram type（architecture / workflow / sequence / data-flow / lifecycle）、4 種 preset、dark/light 主題、Before/Delta/After 驗證比較、reachability probe、finite motion（respects `prefers-reduced-motion`）、self-contained HTML + 1200×630 share card export。55,096★、JavaScript、MIT、138 MB，`pushed_at` 2026-09-08 20:33 UTC = 昨天台北時間 04:33 才 push。topics：`agent-skills` / `architecture-as-code` / `architecture-diagram` / `claude-skill` / `code-visualization` / `codex` / `coding-agents` / `data-flow-diagram` / `deepseek-harness` / `developer-tools` / `diagram-as-code` / `diagrams` / `diagrams-as-code` / `dsh-plugin` / `mermaid-alternative` / `opencode` / `sequence-diagram` / `software-architecture` / `system-design` / `text-to-diagram`。**README 開頭放「Proof Lab」11 個真實 generated artifacts**（不是 product mockup）— 包含 `mco-org/mco` runtime architecture @ commit `9f1a1cf` 的真實 traced 結果。適合所有「需要把 codebase / system description 變成 verified architecture diagram」的人；對 reviewer 比較 PR architecture 差異尤其有用。
- **3W1H：**
  - **What：** Node.js rendering library + agent skill bundle（Cursor / Claude Code / Codex / OpenCode / Raven / DeepSeek Harness 跨 6 hosts）。
  - **Why：** Architecture diagram 過去的痛點是「AI 生 diagram 不 verified — 同 prompt 重跑可能出不同圖」「Mermaid 標記混在 markdown 裡很難 review」「owner 跑掉 diagram 就跟著爛掉」。Archify 的解法是「typed JSON IR + deterministic compiler」 — agent 先產 typed JSON（schema validated），Archify 再編譯成 HTML/SVG，**這個 indirection 讓 Before / Delta / After 的比較可以精確到「added / removed / changed / moved / rerouted facts」**。與 09-06 `cathrynlavery/diagram-design` 比較：diagram-design 偏 editorial illustration（38 種 diagram type、self-contained HTML + SVG、Mermaid-free）；Archify 偏 architecture / workflow / sequence / data-flow / lifecycle 5 種專注的 verified-system-map，**且自帶 review-time diff** — Archify 是 reviewer 觀點、diagram-design 是 editor 觀點。
  - **Who：** Software architect / system designer / code reviewer；要在 PR review 附 architecture diff 的人；要做 documentation-as-code 的人；DeepSeek Harness / Raven / Claude Code / Cursor 等 agent user。
  - **How：** 5 步 Quick start（README line 96 起）：
    1. **Install：** `npx skills add tt-a1i/archify -g`
    2. **Cursor install（explicit）：** `npx skills add tt-a1i/archify -g`
    3. **Generate IR：** 在 agent chat 描述系統，agent 產 typed JSON
    4. **Compile + view：** Archify 把 JSON 編譯成 self-contained HTML（含 PNG / SVG / WebM export）
    5. **Review diff：** Before / Delta / After 比較，加上 reach probe（upstream / downstream reach）
- **安裝方式：**
  - **Agent skill marketplace（install type-20 — per-host command + 跨 6 hosts）：**
    | Surface | Install location or method |
    |---|---|
    | **Raven** | Manual ZIP into `~/.raven/workspace/skills` → `~/.raven/workspace/skills/archify` |
    | **Claude Code** | `~/.claude/skills/` or `.claude/skills/` |
    | **Codex CLI** | `~/.agents/skills/` or `.agents/skills/` |
    | **opencode** | `~/.config/opencode/skills/`, `.opencode/skills/`, or `.agents/skills/` |
    | **Claude.ai** | Upload `archify.zip` under Settings → Capabilities → Skills |
    | **Project Knowledge** | Upload `archify.zip` to the project |
    | **DeepSeek Harness** | `dsh plugin --profile web add @tt-a1i/archify-dsh@0.1.0` |
  - **npm 一行（給 Cursor / 標準 agent）：**
    ```bash
    npx skills add tt-a1i/archify -g
    ```
  - **需求：** Node.js `^22.19.0 || >=24.0.0`（DeepSeek Harness integration）
  - **未提供 pip / brew 路徑** — Node.js + agent skill，純 agent-side install。
- **近期 release：** **`v2.16.0` — "v2.16.0"，published 2026-08-30 11:17 UTC（台北時間 08-30 19:17，距現在 10 天）**。badge 顯示當前 dev 為 `v2.17.0-dev.1`，CHANGELOG.md#unreleased 內有 unreleased 改動。**22,095 stars this week** 排名本週 trending 第 2，**今天 5 個 pick 裡 star velocity 最高**（是 tier-1 與 web-tier 第一）。**對主人的 takeaway**：Archify 的 Before / Delta / After 驗證比較 = 09-08 `heygen-com/hyperframes`「seek-safe、可重跑」概念在 diagram 領域的等價物 — 都是「讓 agent 產出 deterministic + 可 review + 可 diff」這條主旋律的不同應用層；Archify 比 hyperframes 多一步「typed JSON IR indirection」讓 diff 在 schema-validated 層做而不是 pixel 層做。

---

## 重點觀察

- **Tier-A 7-day dedup 命中率 11/14 = 79%（與 09-08 的 high-overlap 趨勢一致）**：今天 Tier-A top 14 只有 3 個 fresh picks（rank 1 `ayghri/i-have-adhd`、rank 8 `multica-ai/andrej-karpathy-skills`、rank 10 `jo-inc/camofox-browser`），其餘 11 個都已在 09-03..09-08 picks 出現過（含 6 重複的 `affaan-m/ECC`）。fresh top 3 跨 3 個不同 domain（agent output UX / Claude 行為規範 / anti-bot browser），domain 多樣性 OK 但數量 floor 觸底 — **Tier-A 進入了「viral tail 持續 dominate」階段**，主人若要更多 variety，web-search Tier-B 的兩條路徑（本次 `superradcompany/microsandbox` Rust VM + `tt-a1i/archify` JS diagram）貢獻了 100% 新 domain。
- **2/5 = 40% zero-GH-release rate — 是 7 月 baseline 60% 的下緣**：今天 `ayghri/i-have-adhd` + `multica-ai/andrej-karpathy-skills` 都沒有 GH release，這倆都是 skill-pack（前者 20KB CLAUDE.md-like、後者 298KB SKILL.md）— 完全符合 09-06 codification 的「skill-pack release tag INVERTS star velocity」讀法（code push ≠ SemVer bump）。`jo-inc/camofox-browser` 雖有 release 但 tag 名是 `camoufox-backup-<SHA>`（dependency snapshot 而非 source release），**等於實質 zero-GH-source-release**。所以 zero-GH-source-release 實質是 3/5 = 60%，回到 7-8 月 baseline。`superradcompany/microsandbox` v0.6.17（5 天） + `tt-a1i/archify` v2.16.0（10 天）撐起 release freshness。
- **跨 5 個 pick 的「agent UX / safety / review」三條主旋律收斂**：(1) **UX**：`i-have-adhd` 的 10-rule output format + `karpathy-skills` 的 4-principle CLAUDE.md；(2) **safety**：`camofox-browser` 的 anti-bot + `microsandbox` 的 microVM isolation；(3) **review**：`archify` 的 Before/Delta/After diff。這三條主旋律 08 月以來在 14:00 picks 反覆出現，但 09-09 第一次**單日同時打到 3 條**。對主人 `horo-agent` 下游 / air-gapped lite 版：UX rule + safety boundary + review diff = 三條可獨立實作的 primitive，都不需要動 hermes-agent-lite 的 core runtime（agent loop / SSE / session schema）。
- **install type-20 (plugin marketplace) 仍占 2/5**：`i-have-adhd` + `karpathy-skills` 都是 Claude Code plugin marketplace install，但 **沒有一個 pick 提到 Hermes** — 跟 09-06 codification 的「Hermes-support FALSE POSITIVE via repo description」警訊一致（要 grep `Hermes` 在 install section，不在 description / repo-wide grep）。`archify` README 的 install table 列了 7 hosts 但沒 Hermes；`camofox-browser` 是 OpenClaw-only；`microsandbox` 走 `npx skills add superradcompany/skills` + `claude mcp add` 但 README 沒列 Hermes。**結論**：今天 5 個 picks 中 0/5 = 0% Hermes-support signal — 比 08-20 的 2/5 peak 進一步下降，**Hermes 仍是 niche**（雖 MEMORY peak 已達 4-tick 突破但本週掉回 0）。
- **5 個 pick 跨 5 種 language**：Python (i-have-adhd)、無 / 純 markdown (karpathy-skills)、JavaScript (camofox-browser)、Rust (microsandbox core)、JavaScript (archify)。**Python 沒主導地位**（今天只有 1/5 = 20%，打破 08-11 的「All-Python day」記錄），反而是「node-heavy」天（2/5 JavaScript 直接、camofox + archify 都是 npm）。**microsandbox 是「多語 SDK 第一名」** — 同一個 Rust runtime 提供 TypeScript / Rust / Python / Go / Ruby 5 種 SDK，是 5/5 跨語言最豐富的 pick，比 09-06 `ifm-ai/uno` 的 4 SDK（Python/Rust/TS/Go）+ SQL + CLI 多 1 種 Ruby — `ifm-ai/uno` 是 research repo SDK parity、`microsandbox` 是 production runtime SDK parity。
- **2/5 permissively licensed 已是 14:00 系列最低**：今天 5 picks license 分布 — MIT × 2（`i-have-adhd` + `camofox-browser`）、Apache-2.0 × 1（`microsandbox`）、None × 1（`karpathy-skills` 沒 LICENSE field）、MIT × 1（`archify`）。**`karpathy-skills` 沒有 license 欄位是 case-C variant** — repo 內 README 寫「These guidelines are designed to be merged with project-specific instructions」屬於意見書 / ruleset 而非 source code，無 LICENSE 對 ruleset 的實際法律風險低，但對 fork-redistribution 是灰色地帶 — 對主人 `horo-agent` downstream 是 warning 而不是 blocker。**對主人的 takeaway**：今天 5 picks 中 4/5 完全 clean、1/5 ruleset 無 license，符合 08-17/20/21 的 3-consec permissive milestone 之後 normal baseline；license friction 仍是低（0 copyleft），但 ruleset-no-license 是新 case。