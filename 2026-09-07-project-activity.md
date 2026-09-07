---
title: GitHub 自由探索 2026-09-07（14:00 台北時間）
date: 2026-09-07
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 5 天 repeat）
  - Web search（NVIDIA/SkillSpector — startupcorners.com 2026-09-06 Trending Recap「Agent Context and Security」段，NVIDIA 官方 agent skill 安全掃描、Apache-2.0、26.1% 技能含漏洞 / 5.2% 惡意意圖的 baseline；jlcodes99/cockpit-tools — 同上 Trending Recap「Dev Workflow」段，中文 IDE 多帳號管理器、Rust、09-06 當天 v1.3.42）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md`
---

# GitHub 專案動態

- 檢查時間：2026-09-07（14:00 台北時間）
- 檢查對象：`openai/skills` / `coreyhaines31/marketingskills` / `aipoch/open-science` / `NVIDIA/SkillSpector` / `jlcodes99/cockpit-tools`
- 來源組合：GitHub Trending today Tier-A 排除前 5 天 repeat（**fresh top 3 = `openai/skills` rank 5（OpenAI 官方 Codex skill 目錄，25,696★）+ `coreyhaines31/marketingskills` rank 13（marketing skill 集合，47,657★/day，**今天 5 個 pick 裡星數最高**）+ `aipoch/open-science` rank 14（本地 AI 研究工作台，3,956★，當天 release v0.26.0）— 排除 `affaan-m/ECC`（rank 1，09-01/03/04/05/06 repeat×5）+ `mattpocock/skills`（rank 2，09-04/05/06×3）+ `cathrynlavery/diagram-design`（rank 3，09-06×1）+ `NousResearch/hermes-agent`（rank 4，09-04/05/06×3）+ `anomalyco/opencode`（rank 6，09-05/06×2）+ `blader/humanizer`（rank 7，09-05/06×2）+ `llvm/llvm-project`（rank 8，巨型 monorepo 不適合 3W1H）+ `DietrichGebert/ponytail`（rank 9，09-01/04/05/06×4）+ `ruvnet/ruflo`（rank 10，09-06×1）+ `magnitudedev/magnitude`（rank 11，09-05/06×2）+ `BraveOPotato/FckSignups`（rank 12，新但純清單型不算 3W1H 主力）**）+ Web search 2 = `NVIDIA/SkillSpector`（startupcorners.com 2026-09-06 「Agent Context and Security」段，**NVIDIA 官方開源的 agent skill 靜態安全掃描器**，Python 3.12+ / Apache-2.0，引述研究「26.1% skills 含漏洞、5.2% 惡意意圖」；71 條 pattern 覆蓋 17 類威脅，含 prompt injection / data exfiltration / supply-chain / MCP least privilege 等；最新 v2.11.0 09-06 已擴充到 npm lockfile 版本解析 + lifecycle hook BH1-BH3）+ `jlcodes99/cockpit-tools`（startupcorners.com 2026-09-06「Dev Workflow」段，**Rust 寫的中文向 AI IDE 多帳號管理器**，支援 Antigravity / Codex / Copilot / Windsurf / Kiro / Cursor / Grok CLI / CodeBuddy / Qoder / Trae / Zed / ZCode 12+ 種 IDE，含多開實例、配額監控、自動喚醒；09-06 才發 v1.3.42 主要修 Codex API 容量錯誤重試邏輯）。

---

## Repo 摘要與 3W1H

### `openai/skills`

- **Repo 摘要：** OpenAI 官方維護的 Codex agent skill 目錄，把 `.system`（內建）、`.curated`（策展）、`.experimental`（實驗）三層 skills 集中託管。25,696★、Python 為主語言（README、skill metadata），原始 repo 沒有 LICENSE.txt（個別 skill 自帶）—— **但 README 第一行就是「This repository is deprecated」**，目前已引導到 [`openai/plugins`](https://github.com/openai/plugins) 新家與 Codex plugins 建置指南。適合要在 Codex 上找官方策展 skill 範本、以及研究 Codex skill YAML / SKILL.md schema 的人。
- **3W1H：**
  - **What：** skill 目錄（不是 library、不是 CLI；內容是 SKILL.md + scripts + 設定 package 的資料夾集合）。
  - **Why：** Codex 的 skill 系統從這個 repo 起家，但 2026-09 OpenAI 已決定把這個倉庫凍結；保留這個 repo 是給歷史引用 + Codex CLI 內建的 `.system` skill 仍從這裡拉。
  - **Who：** Codex CLI 使用者、研究 OpenAI agent skill schema 標準（agentskills.io 開放標準的早期實作）、或要從 `.system` 範本開始寫自家 skill 的人。
  - **How：** Codex 內建 `.system` skills 自動載入；裝 curated/experimental 走 Codex 內建指令 `$skill-installer gh-address-comments` 或 `$skill-installer install https://github.com/openai/skills/tree/main/skills/.experimental/create-plan`，裝完重啟 Codex。
- **安裝方式：**
  - **Codex 內建指令：** 在 Codex session 內跑 `$skill-installer <skill-name>`（curated 直接給名字，例如 `$skill-installer gh-address-comments`）或 `$skill-installer install <description>`（experimental 用描述）+ `$skill-installer install <GitHub directory URL>`（任何 GitHub 子目錄）。裝完重啟 Codex。
  - **未提供 pip / npm / brew / curl** 路徑——這個 repo 是「給 Codex 讀的 skill 集合」，不是給人或給其他工具安裝的。
  - 開發 / 引用：直接 `git clone https://github.com/openai/skills` 讀 SKILL.md 範本。
- **近期 release：** **未找到 GitHub release**（`/releases/latest` 回 404，且 README 自承 deprecated）。活躍度以 `pushed_at 2026-07-14` 判讀——距離今天已 **55 天沒動**，與「deprecated」的狀態一致；後續 skill 改走 `openai/plugins`。

### `coreyhaines31/marketingskills`

- **Repo 摘要：** 給 AI agent 用的「行銷技能庫」，由 Conversion Factory 創辦人 Corey Haines 維護，47,657★、MIT、JavaScript（其實是 markdown skill 集合，語言欄位偏 primary tooling）、7,396 fork。技能涵蓋 CRO、SEO、廣告、寫作、email、analytics、referral、sales enablement、launch、pricing 等十大類、50+ 個 skill，內部有相依圖（`product-marketing` 是所有 skill 的根）。適合做 SaaS、需要技術行銷（growth / content / SEO）的人，把這包裝進 Claude Code / Cursor / Windsurf / Codex 任何支援 agentskills.io 標準的 agent。**今天 5 個 pick 裡星數最高**，主因是行銷 skill 是大多數技術 founder 的真實痛點。
- **3W1H：**
  - **What：** agent skill 集合（SKILL.md 內容庫 + 跨 agent 共用 metadata），無 CLI、無 npm 套件。
  - **Why：** 通用 agent skill 包多半偏 engineering；這包專門把行銷 workflow 模組化，且每個 skill 內含相依圖（先讀 `product-marketing` 再做其它），讓 agent 在不同任務間共享上下文。最新 v2.11.1 還加了 `ai-seo` 對 ChatGPT 5.6 格式漂移（listicle 被砍 50.5%、comparison -32.1%）的回應——比通用 prompt 模板紮實很多。
  - **Who：** 用 Claude Code / Codex / Cursor 經營 SaaS 的人；以及像主人這樣偶爾要做 GTM/landing page copy 的工程背景 founder。
  - **How：** 標準 `npx skills add coreyhaines31/marketingskills`（agentskills.io CLI），逐 skill 安裝可加 `--skill cro copywriting`；Claude Code 顯式指定 agent：`npx skills add coreyhaines31/marketingskills -a claude-code`（避免在 agent session 內跑時只裝到 `.agents/skills/`，那個 Claude Code 不讀）。
- **安裝方式：**
  - **npx（agentskills.io CLI）：**
    - `npx skills add coreyhaines31/marketingskills`（一次裝全部）
    - `npx skills add coreyhaines31/marketingskills --skill cro copywriting`（逐 skill）
    - `npx skills add coreyhaines31/marketingskills --list`（先看）
  - **Claude Code plugin：** `/plugin marketplace add coreyhaines31/marketingskills` → `/plugin install marketing-skills`
  - **clone + copy：** `git clone https://github.com/coreyhaines31/marketingskills.git && cp -r marketingskills/skills/* .agents/skills/`
  - **Git submodule：** `git submodule add https://github.com/coreyhaines31/marketingskills.git .agents/marketingskills`
  - **SkillKit 多 agent：** `npx skillkit install coreyhaines31/marketingskills`（跨 Claude Code / Cursor / Copilot 一鍵裝）
  - 未提供 pip / npm library / brew 路徑；沒有 CLI binary 產出。
- **近期 release：** **`v2.11.1` — 「ai-seo (2.4.0 → 2.5.0)：new references/format-volatility.md」，published 2026-09-05**（2 天前）。v2.5.0 的核心是引用「ChatGPT 5.6 格式漂移」（Peec AI 2026-08 數據）：fan-out 查詢丟掉「vs / comparison / top / best / reviews」修飾詞、`site:` 與「official」激增、listicle 引用 -50.5%、comparison page -32.1%。**主人若做行銷頁或 AI 引用最佳化，這份 release body 的具體數字是最 actionable 的引用素材。**

### `aipoch/open-science`

- **Repo 摘要：** AIPOCH 做的「本地優先、模型中立、可重現」的 AI 科研工作台桌面 app，覆蓋 macOS（ARM64 / x64）、Windows、Linux，3,956★、Apache-2.0、TypeScript。22 個 scientific skill（AlphaFold2 / Boltz / ESM-2 / Evo 2 / DiffDock / scGPT / scvi-tools / Remote Compute SSH 等）+ 24 個 built-in 資料 connector（PubMed / bioRxiv / Genomes / Protein Annotation / Chemistry / Clinical Trials / Drug Regulatory 等）。**今天 5 個 pick 裡唯一有繁體中文 README**（`docs/zh-Hant/README.md`）。適合做生醫 / 化學 / 材料 / 統計的研究者，需要把文獻回顧 + agent 執行 + Python/R notebook + 可追溯 artifact 串在一個 workspace。
- **3W1H：**
  - **What：** 跨平台桌面應用（Electron + 原生殼，repo 大頭是 TS）+ 一組 SKILL.md 研究 skill + connector 系統。
  - **Why：** 既有 ChatGPT / Claude web chat 沒辦法跑 code 或查 scientific DB；現成 IDE（VS Code + Copilot）又不會自動串文獻 + 蛋白質結構 + notebook。可重現性（每個 artifact 是 immutable、checksummed、附 provenance）與 BiomniBench-DA Public 50 #1（79.05 分，GPT-5.6-sol xhigh + Gemini 3.1 Pro judge + DeepSeek v4-pro judge 等權平均）是它的價值錨。
  - **Who：** 生醫 / 化學 / 蛋白質 / 材料 / 統計 / 環境科學研究者；以及想在本地跑「文獻 + 程式 + 結構視覺化」workflow 的人。
  - **How：** 從 release page 抓 platform-specific installer（macOS Apple Silicon DMG / Intel DMG / Windows x64 / Linux AppImage 或 Debian package）→ first-run 5 步驟（環境檢查 / 資料位置 / agent runtime 選擇 / model provider / notebook runtime）→ 建 project → 開 session 描述研究目標。
- **安裝方式：**
  - **桌面安裝檔（install type-14b — Electron 跨平台 desktop app，**有 macOS DMG + Windows installer + Linux AppImage**）：** 從 [`aipoch/open-science/releases/latest`](https://github.com/aipoch/open-science/releases/latest) 對應平台抓安裝檔：
    - macOS Apple Silicon (M1+)：`macOS DMG for Apple Silicon / ARM64`
    - macOS Intel：`macOS DMG for Intel / x64`
    - Windows x64：`Windows x64 installer`
    - Linux x64：`Linux x64 AppImage` 或 `Debian package`
  - **下載驗證：** `SECURITY.md#verifying-your-download` 有 GPG/sha256 流程。
  - **未提供 pip / npm 開發者安裝**——這個專案本體是 Electron 桌面 app，安裝只看 release assets；要看程式碼或自己改，repo 內有 `docs/development/` 與 build 配置。
- **近期 release：** **`v0.26.0` — 「Open Science v0.26.0」，published 2026-09-07 01:13 UTC（**台北時間今天上午 9 點多，距現在 < 5 小時**）**。本版主軸：「HPC-class compute + literature workspace」—— remote compute hosts 多了 per-host Slurm 執行模式（除了 direct SSH 還能 submit Slurm job，含 durable submission / polling / recovery / cancellation / cleanup），以及新的 reference library（identifier-aware import、duplicate merge、PDF attach、citation formatting with provenance）。其它亮點：Apodex provider 加入；GPT-6 Astra 與 Claude Fable 5.1 可直接選；notebook tool calls 變 readable summary card；streaming 與 long-session 資源邊界更穩。**今天 5 個 pick 裡最新鮮的 release，且是當天發版。**

### `NVIDIA/SkillSpector`

- **Repo 摘要：** NVIDIA 官方維護的「agent skill 安全掃描器」，專門盯 Claude Code / Codex / Gemini CLI 等 agent 用 skill（SKILL.md + 附帶 scripts + 設定包）在安裝前是否有漏洞、惡意意圖、supply-chain 風險。16,440★、Python 3.12+、Apache-2.0、3 MB repo size，README 直接引用研究數字：「**26.1% of skills contain vulnerabilities**、**5.2% show likely malicious intent**」。**71 條 vulnerability pattern 跨 17 類**：prompt injection / data exfiltration / privilege escalation / supply chain / excessive agency / output handling / system prompt leakage / memory poisoning / tool misuse / rogue agent / anti-refusal / trigger abuse / dangerous code（AST）/ taint tracking / YARA signatures / MCP least privilege / MCP tool poisoning。屬 NVIDIA Verified Skills pipeline 的一環，掃過的 skill 才會被簽章放到 [`NVIDIA/skills`](https://github.com/NVIDIA/skills) 目錄。
- **3W1H：**
  - **What：** Python CLI（`skillspector scan`）+ Python library + Pi extension + Docker image。
  - **Why：** 現在 agent skill 市場爆炸（agentskills.io / Claude marketplace / Codex / Hermes / Pi / Cursor 都在裝），但 skill 是「執行 + 控制 prompt + 工具呼叫」三合一的隱形信任邊界——裝一個惡意 skill 等於把 agent 讓渡給攻擊者。SkillSpector 把這層信任邊界做成可在裝前掃的開源工具。對主人 horo-agent / horo-webui air-gapped downstream：裝任何第三方 skill 之前先用 SkillSpector 過一遍，是低成本的安全門檻。
  - **Who：** Claude Code / Codex / Gemini CLI 使用者；agent skill 套件維護者（要在上架前自掃）；企業要在自家 agent skill 目錄加掃描 gate 的安全團隊。
  - **How：** 最短路徑 `uv tool install git+https://github.com/NVIDIA/skillspector.git` 拿 CLI → `skillspector scan ./my-skill/`；需要 LLM 二次驗證可加 `--llm` 旗標（需 `ANTHROPIC_API_KEY` / DeepSeek 等）；要看 JSON/Markdown/SARIF 報告加 `--format` + `--output`；要批量掃整個 skill 目錄用 `contrib/batch_scan/batch_scan.py`（支援 20 workers）。
- **安裝方式：**
  - **uv tool install（install type-12 — uv tool pinned CLI）：**
    ```bash
    uv tool install git+https://github.com/NVIDIA/skillspector.git
    # 更新：uv tool update skillspector
    ```
  - **含 MCP extra：** `uv tool install 'skillspector[mcp] @ git+https://github.com/NVIDIA/skillspector.git'`（之後可跑 `skillspector mcp`）
  - **pip（傳統路徑）：** clone → `uv venv .venv && source .venv/bin/activate` → `make install`（或 `make install-dev`）
  - **Docker（無 Python 環境）：**
    - `make docker-build`
    - `docker run --rm -v "$PWD:/scan" skillspector scan ./my-skill/ --no-llm`
    - 加 LLM：建 `.env` 帶 `SKILLSPECTOR_PROVIDER` + `ANTHROPIC_API_KEY`，然後 `docker run --rm -v "$PWD:/scan" --env-file .env skillspector scan ./my-skill/`
  - **輸入格式：** 本地資料夾 / 單檔 SKILL.md / Git repo URL / zip——皆可。Size 上限：100 MiB per ingest、10,000 zip members，超過 fail-closed（`IngestLimitExceededError`）。
- **近期 release：** **`v2.11.0` — 「SkillSpector v2.11.0」，published 2026-08-28**（10 天前）。本版主軸：(1) npm `package-lock.json` v1/v2/v3 解鎖，依 direct + transitive 版本對照 OSV.dev CVE；(2) 新增 BH1-BH3 finding：bundled lifecycle hook（`hooks/hooks.json` / `.claude/settings.json`）、直接驗證的遠端外傳敏感內容、寬鬆/被忽略的 project permission 模式；(3) 加 `SKILLSPECTOR_TEMPERATURE` + `SKILLSPECTOR_SEED` 控制 LLM 採樣穩定性；(4) 修兩個 MP3/P6 false positive 但不削弱 directive detection。**對主人的下游 horo-agent 來說，BH1-BH3 特別值得注意——bundled hook 的遠端外傳驗證是 supply-chain 攻擊最常見的入口。**

### `jlcodes99/cockpit-tools`

- **Repo 摘要：** Rust 寫的「通用 AI IDE 帳號管理工具」，主要給中文圈 founder / 開發者管理多個 AI IDE 訂閱帳號——Antigravity IDE / Codex / GitHub Copilot / Windsurf / Kiro / Cursor / Grok CLI / CodeBuddy / CodeBuddy CN / Qoder / Trae / TRAE SOLO / Trae CN / TRAE SOLO CN / Zed / ZCode 12+ 種 IDE，每家都支援「OAuth 授權 / Token 導入 / 配額監控 / 一鍵切號 / 多開實例 / 自動喚醒 / 標籤管理」。17,180★、Rust、license 欄位為 `null`——但實際 `LICENSE` 檔案未確認，需商用前自行驗證。**今天 5 個 pick 裡唯一一個「繁體中文文件齊全 + 18 種 UI 語言 + 主人向」（主人是中文用戶）**。適合同時訂閱多個 AI IDE 帳號、要輪流切號拉 quota 的中文 founder / agency。
- **3W1H：**
  - **What：** 跨平台桌面 GUI app（Tauri + Rust，repo 主要是 Rust + 一組 IDE 的 OAuth/Token 邏輯）。
  - **Why：** 訂閱制 AI IDE 的「多帳號輪流拉配額」是中文圈的常見痛點，但每家 IDE 的 OAuth / Token / 配額 API 都自成一格。cockpit-tools 把 12+ 種 IDE 的這層抽象成單一 GUI，配額即時顯示 + 一鍵切號 + 多開實例（每實例獨立 user dir / config / 啟動參數）。
  - **Who：** 同時訂閱 2+ 個 AI IDE、要管理多帳號的中文開發者 / agency / 小團隊。
  - **How：** 從 release page 抓對應平台安裝檔（macOS / Windows / Linux）→ 啟動後在 IDE 清單加帳號（OAuth 或 Token / JSON）→ 主介面顯示配額 → 一鍵切號 / 開新實例 / 設自動喚醒排程。
- **安裝方式：**
  - **未找到明確 pip/npm 安裝方式**——這是 GUI 桌面 app，安裝從 GitHub releases page 的平台 binary 拿。
  - **可行路徑（release binary）：**
    - 從 [`jlcodes99/cockpit-tools/releases/latest`](https://github.com/jlcodes99/cockpit-tools/releases/latest) 抓對應平台安裝檔（README 提到 macOS / Windows / Linux 全支援）
    - 開發 / 自編：clone → 需 Rust toolchain（未在 README 給 build 指令細節，建議讀 `CONTRIBUTING.md` 或掃 `Cargo.toml` workspace）
  - **未提供 pip / npm / brew / curl 路徑**——是 GUI 工具不是 library。
- **近期 release：** **`v1.3.42` — 「Cockpit Tools v1.3.42」，published 2026-09-06 18:47 UTC（台北時間今天凌晨 2:47，距現在 ~11 小時）**。本版主軸：Codex API Service 的「capacity error retry 恢復」——HTTP 與 WebSocket 請求把初始 handshake 事件暫存起來，在 retry budget 內重試容量錯誤；output 開始後的失敗直接報告不重放。其它還有：把「容量錯誤」與「帳號額度耗盡」分開（前者不再誤把健康帳號送冷卻）、OAuth 出站 `Version` header 與 `User-Agent` 版本一致、保留仍有可用 Credits 的 Codex 帳號、`config.toml` 損壞時容錯（優先備份恢復；無備份時隔離壞檔繼續用空配置）。**今天 5 個 pick 裡唯一昨日當天發版的 release。**

---

## 重點觀察

- **Release 新鮮度照例兩極，且今天是「當天 release = 2/5」的新高。** 5 個 pick 有 3 個有 GH release：`aipoch/open-science` v0.26.0（**當天，< 5 小時內**）+ `jlcodes99/cockpit-tools` v1.3.42（**昨天**）+ `coreyhaines31/marketingskills` v2.11.1（2 天前）+ `NVIDIA/SkillSpector` v2.11.0（10 天前）。另外 `openai/skills` 自承 deprecated（55 天沒動）屬 60% baseline 的例外情境。**「當天 release = 2/5 = 40%」是 09-21 以來同類型 5-repo report 觀察到的最高當日 release 比例**，但今天不是因為 4/5 picks 有 release，而是 2/5 picks 都在「v0.x 早期快速迭代」+「中文圈生態頻密小版」——`aipoch/open-science` 6 月才出生（`created_at 2026-07-03`）走 v0.26.0 = **67 天 26 版** = 約 2.5 天一版；`jlcodes99/cockpit-tools` 1 月出生走 v1.3.42 = **約 2 天一版**（中文 founder 工具節奏）。判讀 release freshness 要看版本**語意**（早期迭代 vs 穩態維護）而非單看天數。
- **安裝門檻跨 4 個數量級，且形成「npx skills add → uv tool install → Electron desktop → bare binary」四級光譜。** 最低是 `coreyhaines31/marketingskills` 一行 `npx skills add coreyhaines31/marketingskills`（agentskills.io 標準 CLI，agent host auto-detect）；中間是 `openai/skills` 走 Codex 內建 `$skill-installer`；中上是 `NVIDIA/SkillSpector` 一行 `uv tool install git+https://...`（uv tool 安裝但 pinned 到 git URL，與 09-06 `MoneyPrinterTurbo` 的 `uv sync --frozen` 不同——SkillSpector 是 release-style CLI，MoneyPrinterTurbo 是 repo-as-deployment）；最高是 `aipoch/open-science` 走 Electron 跨平台 desktop DMG / installer / AppImage + `jlcodes99/cockpit-tools` 走 Rust GUI binary。install type 覆蓋 4 種零重複：type-20（marketingskills 多 agent CLI install，6 種安裝路徑可選）、Codex `$skill-installer`（openai/skills）、type-12 uv tool pinned CLI（SkillSpector）、type-14b Electron 跨平台桌面（open-science）+ bare Rust GUI binary（cockpit-tools）。
- **License 呈現「主人挑食的訊號」：3 個有 license、2 個沒有。** `coreyhaines31/marketingskills` MIT、`aipoch/open-science` Apache-2.0（case-A2）、`NVIDIA/SkillSpector` Apache-2.0（case-A2）—— 前兩者對主人 horo-agent / horo-webui downstream 是 **0 license friction**。`openai/skills` 個別 skill 各自帶 `LICENSE.txt`、repo 本身沒有 `LICENSE` 檔——屬於「case-C 變體：個別子目錄有 license、root 沒有」，需逐個 skill 驗。`jlcodes99/cockpit-tools` API `license: null` 且本次未翻 LICENSE 檔內容——**正式商用前需自行驗證**，歸 case-C 風險群。**對主人 downstream 影響**：今天 5 個 pick 裡 3 個是 clean permissive、2 個需要逐檔驗 license——比 09-06「4 MIT + 1 Apache-2.0」的 100% 純度略降，但比起 09-15 / 09-20 那些 AGPL/GPL-3.0-or-later 的地雷，仍是安全週。
- **語言生態呈「agent skill 層 = JavaScript、substrate 層 = Python + Rust + TypeScript」三分裂。** Trending fresh top 3 = openai/skills（Python 元資料，內容 markdown）+ marketingskills（JavaScript，內容 markdown）+ aipoch/open-science（TypeScript + Electron 桌面）；兩個 web-search pick = SkillSpector（Python CLI/scanner）+ cockpit-tools（Rust GUI）。**今天值得注意的訊號**：(a) **Python 不再是 skill-pack 的預設**——`marketingskills` 與 `openai/skills` 的內容主體都是 markdown，語言欄位反映的是 tooling / build / metadata，不是 skill 本身；(b) **Rust 出現在中文 founder 工具**（cockpit-tools），呼應 09-21 `AprilNEA/OpenLogi`（同樣是中文開發者的 Rust 工具）的觀察——Rust 在「中文個人 / 小團隊桌面工具」這個 niche 已站穩；(c) **TypeScript + Electron 仍是 desktop app 的主流**（open-science），與 09-15 `holaboss-ai/holaOS` 同型。
- **今天最值得記的「安全 + 信任邊界」訊號是 SkillSpector + openai/skills 的 deprecated 雙線。** NVIDIA SkillSpector 引述研究：「**26.1% of skills contain vulnerabilities, 5.2% show likely malicious intent**」——這個數字是對「現在 agent skill 市場爆炸」這個敘事的反向制衡。而 OpenAI 自己在 09-07 把 `openai/skills` repo 標 deprecated、把新家搬到 `openai/plugins` 與 Codex plugins guide——這是 OpenAI **承認「把 skill 散在 GitHub 各處沒有 curation gate」這個形態不可持續**，所以把入口收回到 plugins marketplace。**對主人 horo-agent / horo-webui 設計 takeaway**：當自家 skill 目錄開始成長，必須在「裝前掃描」（SkillSpector pattern）+ 「上架 curation gate」（OpenAI plugins pattern）兩端都投入——只靠其中一端都會出問題。今天 5 個 pick 沒有任何一個有 Hermes Agent badge（同 09-06 觀察），但 SkillSpector 與 SkillNet 都把 Claude Code / Codex / Gemini CLI / Hermes 列為受影響 target，這個 security perimeter 是跨 host 共用的——主人若在 horo-agent 內建 skill 目錄，SkillSpector 是當前成本最低的安全門檻工具。
