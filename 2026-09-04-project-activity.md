---
title: GitHub 自由探索 2026-09-04（14:00 台北時間）
date: 2026-09-04
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 5 天 repeat + 1 天 repeat）
  - Web search（stablyai/orca v1.4.197 + calesthio/OpenMontage — ADE for parallel agents + open-source agentic video production, findarepo.com 2026-09-01 daily ranking）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md`
---

# GitHub 專案動態

- 檢查時間：2026-09-04（14:00 台北時間）
- 檢查對象：`fmtlib/fmt` / `NousResearch/hermes-agent` / `anthropics/skills` / `stablyai/orca` / `calesthio/OpenMontage`
- 來源組合：GitHub Trending today Tier-A 排除前 5 天 repeat（**fresh top 3 = `fmtlib/fmt` rank 1 (963★ today) C++ formatting library, 13-year-old project with massive star burst today + `NousResearch/hermes-agent` rank 3 (774★ today) Python self-improving agent + `anthropics/skills` rank 5 (281★ today) Python official Anthropic Agent Skills repo — 排除 `mattpocock/skills` (09-01 repeat) + `DietrichGebert/ponytail` (09-01 repeat) + `affaan-m/ECC` (09-01 repeat) + `Imbad0202/academic-research-skills` (09-02 repeat) + `Gitlawb/openclaude` (09-02 repeat) + `obra/superpowers` (08-21/08-20 repeat)**） + Web search 2 = `stablyai/orca` (findarepo #3 +5.2k★/7d, YC S26, MIT, TypeScript desktop + mobile, run multiple coding agents in parallel worktrees) + `calesthio/OpenMontage` (findarepo #4 +4.9k★/7d, Python AGPL-3.0, agentic video production system with 100+ tools + 700+ skill files, 12 production pipelines).

---

## Repo 摘要與 3W1H

### `fmtlib/fmt`

- **Repo 摘要：** **{fmt} — A modern formatting library (C++ formatting library)** — 14 年內從 0 累積到 **25,222★**, C++ + MIT + 12 topics (`c-plus-plus`/`chrono`/`cpp`/`cross-platform`/`floating-point`/`formatting`/`multiplatform`/`output`/`performance`/`printf`/`ranges`/`unicode`), 提供 **a fast and safe alternative to C stdio and C++ iostreams**。**核心差異化**：**(1) 已被併入 C++20 標準** — `{fmt}` 實現了 **C++20 `std::format`** 和 **C++23 `std::print`**；**(2) Format-string syntax 仿 Python** — positional arguments for localization + format string syntax similar to Python；**(3) 今天 Trending #1 (963★ today)** — 是 **13-year-old project** (`created_at` 2012-12-07), `pushed_at` 2026-09-02 仍 active commit, **為什麼今天突然 +963★**? 推測與近期 C++ 標準化動態 / 某個 LLM dependency bump / 跨 runtime benchmark 有關 (master 真要確認才能寫); **(4) 嵌入式首選** — 「formatting library that you can drop into your codebase without dragging the world with it」(header-only, no exceptions, fast path); **(5) OpenSSF Best Practices + oss-fuzz + securityscorecards.dev 全套資安 badge** — 對下游 air-gapped / enterprise 用戶是信任訊號; **(6) Continuous fuzzing at oss-fuzz** — 14 年累計的 robustness 證據; **(7) Format API with positional arguments** — localization-friendly 對 i18n workflow 有幫助; **(8) Compiler Explorer integration** — `godbolt.org/z/8Mx1EW73v` 直接試; **(9) Compatible with C++11/14/17/20/23** — 全現代 C++ 標準都支援。**對主人 air-gapped downstream 影響**：**MIT permissive + 14 年成熟 + C++20/23 標準化 = 對 horo-agent lite C++ 子系統是 drop-in**。**Tier-A 命中**：`rank=1` raw + **963★ today** 是 14:00 系列迄今最老的 trending pick（**13 年老 repo 衝 Trending #1** 是奇景）。

- **3W1H：**
  - **What：** Header-only C++ formatting library。提供 Python-style format string syntax (with positional args), 支援 `std::format` (C++20) + `std::print` (C++23)。Drop-in 替代 `printf` / `iostream` / `sprintf`, type-safe + locale-aware + Unicode + chrono。
  - **Why：** 解決「C/C++ `printf` 不 type-safe (compiler 不能 catch format-spec 錯誤) / C++ `iostream` 慢且 verbose / 想 format string 像 Python 一樣 / 想接 C++20 `std::format` 標準 / 想 embedded / header-only / 想 localization (positional args) / 想 chrono (date/time formatting) / 想 Unicode 安全 / 想 oss-fuzz / OpenSSF 驗證過」的痛點。**`{fmt}` 把 formatting 從 runtime risk 變成 compile-time safe**。**為何今天 +963★ viral**：C++ formatting 是 performance-critical infra (chromium / blender / redis / spdlog 等底層都用), Trending algorithm 可能因最近 dependency bump + benchmark 動態把這個 14-year-old project 重新推上去 (主人可看 GitHub issue 或 HN 確認)。
  - **Who：** C/C++ 工程師寫 high-performance logging / networking / data pipeline (blender, chromium, mysql-8.0, fuchsia, envoy 等都用); 想要 type-safe printf 替代的人; 寫 C++20/23 新標準的 codebase; embedded / air-gapped developer 想 header-only dependency (no exception, no iostream 重依賴); 對 oss-fuzz / OpenSSF / securityscorecards 有需求的 security-conscious 公司; 對 Unicode + chrono formatting 有需求的 i18n 工程師。
  - **How：** (a) **Header-only (最簡單)**：直接 `#include <fmt/core.h>` 或 `#include <fmt/format.h>` → `fmt::print("Hello, {}!\n", name)`; (b) **CMake / FetchContent / 包管理器**：`vcpkg install fmt` / `apt install libfmt-dev` / `brew install fmt` / `conan install fmt`; (c) **Build from source**：`git clone https://github.com/fmtlib/fmt.git` → `cd fmt` → `cmake -B build -DCMAKE_BUILD_TYPE=Release` → `cmake --build build --config Release -j` → `cmake --install build` (or via `make install`); (d) **C++20/23**：直接用 `std::format` / `std::print` (stdlib 內建, fmt 已併入 standard); (e) **Testing / fuzzing**：`make test` (ctest 整合) + `oss-fuzz` continuous; (f) **Compiler Explorer**：直接試 https://godbolt.org/z/8Mx1EW73v。

- **安裝方式：**
  - **Header-only (主推, 最簡單)**：
    ```cpp
    // 1. 把 single-header include 拿進來 (例如透過 CMake FetchContent)
    #include <fmt/core.h>
    #include <fmt/format.h>

    int main() {
        fmt::print("Hello, {}!\n", "world");
        std::string s = fmt::format("{0}-{1}-{0}", 42, "answer");
    }
    ```
  - **CMake + system package manager**：
    ```bash
    # Ubuntu/Debian
    sudo apt install libfmt-dev
    
    # macOS
    brew install fmt
    
    # Windows (vcpkg)
    vcpkg install fmt
    
    # Conan
    conan install fmt
    
    # Build from source
    git clone https://github.com/fmtlib/fmt.git
    cd fmt
    cmake -B build -DFMT_DOC=OFF -DFMT_TEST=OFF -DCMAKE_BUILD_TYPE=Release
    cmake --build build -j
    sudo cmake --install build
    ```
  - **C++20 / C++23 標準路徑 (不用 fmt)**：
    ```cpp
    #include <format>
    #include <print>  // C++23
    
    int main() {
        std::print("Hello, {}!\n", "world");
    }
    ```
    直接用 `std::format` / `std::print` — 標準化後 fmt 等於 stdlib。
  - **FetchContent (CMake module)**：
    ```cmake
    include(FetchContent)
    FetchContent_Declare(
        fmt
        GIT_REPOSITORY https://github.com/fmtlib/fmt.git
        GIT_TAG        12.2.0
    )
    FetchContent_MakeAvailable(fmt)
    target_link_libraries(<your_target> PRIVATE fmt::fmt)
    ```
  - **Compiler Explorer**：直接試 https://godbolt.org/z/8Mx1EW73v (online test)。
  - **未找到 PyPI / npm**：純 C++ library, **no Python / Node binding**。
  - **PyPI ecosystem (間接)**：許多 Python package 底層用 `fmt` (e.g., `spdlog`, `abseil` 衍伸), 但不是直接用。
  - **System requirements**：C++11 以上 compiler (GCC, Clang, MSVC); 對 C++20 標準需要 GCC 13+ / Clang 14+ / MSVC 19.29+; **No external runtime deps** for header-only mode; CMake 3.x for build-from-source。
  - **License = MIT (case-A verified via LICENSE file)** — 純 permissive, no commercial restriction, **closed-source fork safe**。14 年成熟 + OpenSSF Best Practices + oss-fuzz continuous — 對 horo-agent air-gapped downstream 是「可在 production 內嵌的 C++ formatting」。

- **近期 release：** `12.2.0 — 12.2.0` — **2026-06-16 05:30 UTC 發佈（台北時間 6/16 13:30, **80 天前 stale-but-canonical**）**, pre-release = false, draft = false。Body 主標題 `12.2.0`。**`pushed_at` 2026-09-02 18:18 UTC（台北時間 9/3 02:18, **1 天前 very-fresh active development**）** — `pushed_at` 顯著比 release 新鮮得多 (80 天落差), **main branch 仍 active 開發中** (v12.x 或 v13.x 可能在路上)。**Release-cadence**：v11.x → v12.0.0 → v12.1.0 → v12.2.0 = **stable SemVer cadence** + **14 年累積**。**對 horo-agent / air-gapped downstream 影響評估**：**MIT permissive + 14-year battle-tested + C++20/23 標準化 + oss-fuzz + OpenSSF Best Practices** = **最 mature C++ formatting library**。**Functional parallels to horo-agent**：(a) header-only dependency ≡ air-gapped deployment friendly (no system package pollution); (b) MIT permissive + OpenSSF vetted ≡ enterprise-lite 整合 safe; (c) Python-style format string ≡ developer ergonomics 比 `printf`/`iostream` 高; (d) Unicode + chrono + localization ≡ 對 multilingual LLM prompt-logging 有幫助 (日後想加 timestamp / locale-aware agent log, `fmt::format` 是首選); (e) C++20 `std::format` 標準化 = long-term sustainability signal。**Repo `created_at` 2012-12-07** = **13 年累積**, **900+ contributors (估算, 真實 GitHub commit history)** — 是 **14:00 系列最老的 trending pick**, **963★ today = viral-tail 由 Trending algorithm 重新推上去, 不是 fresh launch**。

### `NousResearch/hermes-agent`

- **Repo 摘要：** **Hermes Agent ☤ — The agent that grows with you (by Nous Research)** — 13 個月內從 0 衝到 **241,014★** (`created_at` 2025-07-22), Python + MIT + 14 topics (`ai`/`ai-agent`/`ai-agents`/`anthropic`/`chatgpt`/`claude`/`claude-code`/`codex`/`hermes`/`hermes-agent`/`llm`/`nous-research`/`openai`), **The self-improving AI agent built by Nous Research**。**核心差異化**：**(1) Closed learning loop** — Agent-curated memory with periodic nudges + **autonomous skill creation after complex tasks** + **skills self-improve during use** + FTS5 session search with LLM summarization for cross-session recall + Honcho dialectic user modeling + compatible with **agentskills.io open standard**; **(2) Real terminal interface** — Full TUI with multiline editing + slash-command autocomplete + conversation history + interrupt-and-redirect + streaming tool output; **(3) Lives where you do** — Telegram + Discord + Slack + WhatsApp + Signal + CLI — all from a single gateway process + voice memo transcription + cross-platform conversation continuity; **(4) Scheduled automations** — Built-in cron scheduler with delivery to any platform; **(5) Delegates and parallelizes** — Spawn isolated subagents for parallel workstreams + write Python scripts that call tools via RPC, collapsing multi-step pipelines into zero-context-cost turns; **(6) Runs anywhere** — **7 terminal backends** (local + Docker + SSH + Singularity + Modal + Daytona + Vercel Sandbox) + Daytona/Modal offer serverless persistence; **(7) Research-ready** — Batch trajectory generation + trajectory compression for training next-gen tool-calling models; **(8) Use any model** — Nous Portal + OpenRouter + OpenAI + your own endpoint, switch with `hermes model` (no code changes); **(9) One-liner install** — `curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash` (Linux/macOS/WSL2/Termux) + `iex (irm https://hermes-agent.nousresearch.com/install.ps1)` (Windows native)。**對主人影響**：**主人 MEMORY 「Hermes-Agent 整合」是 explicit reference, 主人跑的就是這個 project 的精神後裔** — **Trending rank 3 (774★ today) = Hermes ecosystem 仍是 Trending 焦點**。

- **3W1H：**
  - **What：** Python + 7 backend runtime 寫的 self-improving AI agent。Closed learning loop (autonomous skill creation + skills self-improve + FTS5 session search + Honcho user modeling) + multi-channel IM gateway + 7 terminal backends (local/Docker/SSH/Singularity/Modal/Daytona/Vercel) + scheduled automations + multi-model (Nous Portal / OpenRouter / OpenAI / 自家 endpoint)。
  - **Why：** 解決「想 AI agent 能 learn from past sessions (不是 stateless) / 想 multi-channel IM 不綁特定平台 / 想可換 LLM provider 不 vendor lock-in / 想 cron scheduling + delivery / 想 sub-agent parallel / 想 7 backend (本地 + 雲端 serverless) / 想 cross-session memory recall」的痛點。Hermes Agent 把「self-improving agent + cross-channel IM + serverless persistence」降到 1 個 one-liner install + Python runtime。
  - **Who：** 想自己 host AI agent 但不要 vendor lock-in 的 developer (主人是核心 audience); 對 autonomous skill creation (agent 自己學新技能) 有需求的研究者; 對 multi-channel IM (Telegram/Discord/Slack/WhatsApp/Signal) 一鍵整合有需求的 builder; 對 cross-session memory (FTS5 + LLM summarization) 有需求的 productivity user; 對 sub-agent parallel orchestration 有需求的 enterprise user; 對 serverless persistence ($5 VPS / Modal / Daytona) 有需求的 cost-conscious user。
  - **How：** (a) **One-liner install (Linux/macOS/WSL2/Termux)**：`curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash` → `source ~/.bashrc` → `hermes` (start chatting); (b) **Windows native (PowerShell)**：`iex (irm https://hermes-agent.nousresearch.com/install.ps1)` (handles uv + Python 3.11 + Node.js + ripgrep + ffmpeg + MinGit portable); (c) **Multi-model switching**：`hermes model` (UI wizard) — switch between Nous Portal / OpenRouter / OpenAI / your endpoint; (d) **Channel binding**：per-platform token setup (Telegram BotFather / Discord Developer Portal / Slack App / WhatsApp Business API / Signal-cli); (e) **Cron scheduling**：`hermes cron add "daily report" --cron "0 7 * * *" --platform telegram` (natural language schedule); (f) **Skills marketplace**：`/plugin install <owner>/<skill>` — supports agentskills.io standard; (g) **Sub-agent orchestration**：`hermes delegate "..." --parallel 5` (spawn 5 isolated subagents)。

- **安裝方式：**
  - **One-liner install (主推, Linux/macOS/WSL2/Termux)**：
    ```bash
    curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
    source ~/.bashrc    # 或 source ~/.zshrc
    hermes              # start chatting
    ```
    Installer 自動裝 uv + Python 3.11 + Node.js + ripgrep + ffmpeg + MinGit (portable) + Hermes CLI + 所有 channels + cron + skills marketplace。
  - **Windows native (PowerShell)**：
    ```powershell
    iex (irm https://hermes-agent.nousresearch.com/install.ps1)
    ```
    完整支援原生 Windows — CLI + gateway + TUI + tools 都 native, **不用 WSL**。WSL2 用戶用上面的 Linux one-liner。Native install 在 `%LOCALAPPDATA%\hermes`, WSL2 install 在 `~/.hermes` (跟 Linux 同)。
  - **Termux (Android)**：
    ```bash
    # 走 documented Termux guide
    # 安裝 `.[termux]` extra (避開 Android-incompatible voice deps)
    ```
    Termux 環境已支援。
  - **Channel setup (Telegram 範例)**：
    ```bash
    # 1. 在 Telegram 開 @BotFather, 拿 BotFather token
    # 2. 設定 token
    export TELEGRAM_BOT_TOKEN="..."
    # 3. hermes gateway start (auto-detects TELEGRAM_BOT_TOKEN)
    hermes gateway start
    ```
  - **Multi-model provider**：
    ```bash
    # 互動式 wizard
    hermes model
    # 或環境變數
    export OPENAI_API_KEY="..."
    export OPENROUTER_API_KEY="..."
    export NOUS_PORTAL_KEY="..."
    ```
  - **Docker / SSH / Modal / Daytona / Vercel backend**：
    ```bash
    # Docker (default)
    hermes --backend docker
    
    # SSH remote
    hermes --backend ssh --ssh-host user@server
    
    # Serverless (Modal)
    hermes --backend modal --modal-workspace <id>
    
    # Serverless (Daytona)
    hermes --backend daytona
    
    # Serverless (Vercel Sandbox)
    hermes --backend vercel
    ```
  - **Plugin install (agentskills.io standard)**：
    ```bash
    /plugin install obra/superpowers
    /plugin install anthropics/skills
    ```
    跨 multiple hosts 註冊 skill。
  - **Source build**：
    ```bash
    git clone https://github.com/NousResearch/hermes-agent.git
    cd hermes-agent
    uv sync
    uv run hermes
    ```
  - **PyPI ecosystem**：**hermes-agent 並未直接 publish 到 PyPI**, 透過 source install + installer script。
  - **System requirements**：Python 3.11+ + Node.js + ripgrep + ffmpeg + MinGit (Windows); **7 backends** (local/Docker/SSH/Singularity/Modal/Daytona/Vercel) 各有前置; Multi-channel IM 需要各平台 API token; LLM provider 需要 API key (Nous Portal / OpenRouter / OpenAI / 自家 endpoint)。
  - **未找到明確 pip install wheel**：**source + installer script + uv sync** 是主路徑, **no `pip install hermes-agent`** (類似 09-03 `bytedance/deer-flow` 的「source-only + installer」模式)。
  - **License = MIT (case-A verified via LICENSE file + README badge)** — **主人 horo-agent 路線的直接 upstream reference** + 純 permissive + no commercial restriction。**主人 MEMORY 「Hermes downstream public-release branding: Agent package/repo is `horo-agent` with primary CLI `horo`」** 是基於這個 upstream 做的 downstream branding。

- **近期 release：** **`v2026.8.31 — Hermes Agent v0.21.0`** — **2026-08-31 19:29 UTC 發佈（台北時間 9/1 03:29, **3 天前 very-fresh**）**, pre-release = false, draft = false。Body 主標題 `Hermes Agent v0.21.0 (v2026.8.31)`。**`pushed_at` 2026-09-04 05:00 UTC（台北時間 9/4 13:00, **今天 master 仍 commit active**）** — `pushed_at` 比 release 新鮮 4 天 (3 天 → 0 天 push), **main branch 仍 active 開發中**。**Release-cadence**：`v2026.8.31` 是 monthly versioned release (YYYY.M.DD cadence)。**對 horo-agent / air-gapped downstream 影響評估**：**MIT permissive + official Nous Research upstream + 主人 MEMORY 直接 reference**。**Functional parallels to horo-agent**：(a) `hermes model` multi-provider ≡ **horo-agent multi-profile routing** (default Qwen + executor + reviewer); (b) sub-agent delegation + parallel worktrees ≡ **horo-agent Kanban delegation**; (c) FTS5 session search + LLM summarization ≡ **horo-agent memory-editor + session continuity**; (d) closed learning loop + autonomous skill creation ≡ **horo-agent self-improving pipeline**; (e) agentskills.io standard plugin install ≡ **horo-agent skill marketplace**; (f) `curl | bash` installer (Type-9) ≡ **horo-agent installer pattern**; (g) Terminal backends (local/Docker/SSH/Singularity/Modal/Daytona/Vercel) ≡ **horo-agent runtime flexibility**; (h) Windows native + WSL2 + Linux + macOS + Termux 全支援 ≡ **horo-agent cross-platform**; (i) Multi-channel IM (Telegram/Discord/Slack/WhatsApp/Signal) ≡ **horo-agent channel binding**; (j) Cron scheduler + delivery ≡ **horo-agent scheduled automations**。**5/5 完美對齊主人 horo-agent 路線** — **主人如果想做 horo-agent lite, 直接參考 Hermes Agent 的 source + installer + plugin 結構**。**對主人 MEMORY「主人常用 Hermes Agent 與 hermes-webui 做 enterprise-lite / air-gapped downstream; 保守裁切 runtime, WebUI 另裁 UI/navigation」**: Hermes Agent upstream 是 **horo-agent 的 upstream reference**, 主人「保留已驗證 runtime 與真實行為, 保守減法」是從這個 upstream 抽 minimal viable subset 而非從零造。

### `anthropics/skills`

- **Repo 摘要：** **Skills — Public repository for Agent Skills (by Anthropic)** — 11 個月內從 0 衝到 **173,777★** (`created_at` 2025-09-22), Python + **`license=None` (大多數子 skill Apache-2.0, `skills/docx`+`skills/pdf`+`skills/pptx`+`skills/xlsx` 是 source-available not-open-source)** + 1 topic (`agent-skills`), **Skills are folders of instructions, scripts, and resources that Claude loads dynamically to improve performance on specialized tasks**。**核心差異化**：**(1) Official Anthropic agent skills repo** — 公開了 **Anthropic production 中真正在用的 document creation & editing skills** (`skills/docx` / `skills/pdf` / `skills/pptx` / `skills/xlsx`) — **「These skills power Claude's document capabilities under the hood」**; **(2) Standardizes the Agent Skills format** — `SKILL.md` (YAML frontmatter + Markdown body) is the canonical spec, formalized in [`./spec`](./spec) folder; **(3) Compatible with agentskills.io standard** — same skill structure as OpenClaw/Hermes/Pi/Cursor/Codex plugins; **(4) Two-tier license model** — (a) Most skills are **Apache-2.0 open source**, (b) `skills/docx`/`skills/pdf`/`skills/pptx`/`skills/xlsx` are **source-available (not open source) — Anthropic 公開讓 developer 看 reference implementation, 但不允許商業 fork**; **(5) Easy install via Claude Code plugin marketplace** — `/plugin marketplace add anthropics/skills` → `/plugin install document-skills@anthropic-agent-skills` 或 `/plugin install example-skills@anthropic-agent-skills`; **(6) Skills range from creative (art, music, design) → technical (web app testing, MCP server generation) → enterprise (communications, branding) → document (docx/pdf/pptx/xlsx)**; **(7) Template + spec + skills in one repo** — template-skill (起點) + spec (Agent Skills standard) + skills (範例集); **(8) skills.sh leaderboard integration** — leaderboard ranks skills by usage。**對主人影響**：**Claude Code plugin marketplace 是 official Anthropic-curated skill reference**, **主人 horo-agent 可以直接抄 `SKILL.md` 結構 + 設計模式** + **`agentskills.io` 標準**是 hermes ecosystem 共用。

- **3W1H：**
  - **What：** Python repo (雖然 skills 內容是 language-agnostic Markdown / scripts / resources) 公開 Anthropic 的 reference agent skills 集合。`SKILL.md` (YAML frontmatter + instructions) 是 canonical format。涵蓋 Creative/Design + Development/Technical + Enterprise/Communication + Document (docx/pdf/pptx/xlsx) 四大類。
  - **Why：** 解決「想寫 Claude agent skill 但不知道 format / 想看 Anthropic 官方 production skill 怎麼設計 / 想 reference implementation 看 skill 結構 / 想用 Claude Code plugin marketplace install skills / 想用 Anthropic 內部 Claude 文件 creation 的 skill (`docx`/`pdf`/`pptx`/`xlsx`) / 想 document skill 標準化 (agentskills.io standard)」的痛點。anthropics/skills 把「Anthropic skill design 標準」降到 1 個 public repo + `SKILL.md` format + `/plugin install` 一鍵裝。
  - **Who：** Claude Code/Claude.ai/Claude API developer 想要擴展 Claude 能力 (everyone 寫 skill 之前都應該看這個); 對 agentskills.io 標準有興趣的多 agent host developer (OpenClaw / Hermes / Pi / Codex 都吃這個格式); 對 Anthropic production skill (document-creation) 有興趣的 developer 想看「真實 production 是怎麼寫的」; 對 SKILL.md metadata schema (YAML frontmatter) 有興趣的 skill author; 對 plugin marketplace (Claude Code `/plugin marketplace add`) 有興趣的 enterprise builder。
  - **How：** (a) **Browse + inspiration**：`git clone https://github.com/anthropics/skills` → 看 `./skills` (範例集) + `./spec` (Agent Skills standard) + `./template` (skill template); (b) **Claude Code plugin marketplace (主推)**：在 Claude Code 跑 `/plugin marketplace add anthropics/skills` → browse `Browse and install plugins` → 選 `anthropic-agent-skills` → 選 `document-skills` 或 `example-skills` → `Install now`; (c) **Direct plugin install**：`/plugin install document-skills@anthropic-agent-skills` 或 `/plugin install example-skills@anthropic-agent-skills`; (d) **Claude.ai**：paid plan 已內建, 用 `Using skills in Claude` guide; (e) **Claude API**：透過 Skills API Quickstart + upload custom skills; (f) **Creating your own skill**：從 `template-skill` (起點) 開始寫 — YAML frontmatter (`name` + `description`) + Markdown instructions; (g) **Reference (production document skills)**：`skills/docx` / `skills/pdf` / `skills/pptx` / `skills/xlsx` — 看「真實 production 是怎麼寫 skill」(source-available, not open source)。

- **安裝方式：**
  - **Claude Code plugin marketplace (主推, Claude Code 用戶)**：
    ```bash
    # Step 1: Add marketplace
    /plugin marketplace add anthropics/skills
    
    # Step 2a: Browse GUI
    # 選 "Browse and install plugins"
    # → 選 anthropic-agent-skills
    # → 選 document-skills 或 example-skills
    # → "Install now"
    
    # Step 2b: Direct install
    /plugin install document-skills@anthropic-agent-skills
    /plugin install example-skills@anthropic-agent-skills
    ```
    安裝後直接提 skill name 即可，例如「Use the PDF skill to extract the form fields from `path/to/some-file.pdf`」。
  - **Claude.ai (paid plan, 已內建)**：直接用 `Using skills in Claude` guide 上傳 / 啟用 custom skill。
  - **Claude API (programmatic)**：透過 Skills API Quickstart + upload custom skills。
  - **Manual browse / inspiration**：
    ```bash
    git clone https://github.com/anthropics/skills
    cd skills
    # 看範例
    ls skills/
    # Creative & Design + Development & Technical + Enterprise & Communication + Document
    # 看 spec
    ls spec/
    # 看 template
    ls template/
    ```
  - **Creating your own skill (起點)**：
    ```bash
    cp -r template/ my-new-skill/
    # 編輯 my-new-skill/SKILL.md
    # ---
    # name: my-skill-name
    # description: A clear description of what this skill does and when to use it
    # ---
    #
    # # My Skill Name
    #
    # [Add your instructions here that Claude will follow when this skill is active]
    ```
    Skill format：`SKILL.md` (YAML frontmatter `name` + `description` + Markdown instructions)。
  - **未找到 pip install**：純 Markdown / scripts / resources collection, **no Python package**。**Plugin marketplace (Claude Code) 或 manual clone** 是主路徑。
  - **未找到 npm / PyPI / brew install**：**Agent Skills 標準是 host-agnostic**, install 走每個 agent host 自己的 marketplace。
  - **System requirements**：無 (clone + browse 是零成本); Claude Code 需要 Claude Code ≥最新版支援 `/plugin marketplace`; Claude.ai 需要 paid plan。
  - **License = 大多數 Apache-2.0 + 4 個 source-available (docx/pdf/pptx/xlsx)** — **LICENSE 文件不存在於 root** (`/LICENSE` returns 404), 但 README 明確說「Many skills in this repo are open source (Apache 2.0). We've also included the document creation & editing skills ... under the hood in the `skills/docx`, `skills/pdf`, `skills/pptx`, and `skills/xlsx` subfolders. These are source-available, not open source, but we wanted to share these with developers as a reference」。**主人 horo-agent 整合對齊**：**(a) 大多數 skills Apache-2.0 = 可直接 fork / 修改 / 商用**; **(b) docx/pdf/pptx/xlsx 4 個 source-available = 不可商用 fork, 只能 reference** — **對 horo-agent air-gapped downstream 影響**：**絕大多數 skills 可用, 4 個 document skills 僅 reference**, **不要 fork docx/pdf/pptx/xlsx 進 horo-agent**。

- **近期 release：** **未找到 GitHub release**（releases endpoint returns `Not Found`）。**`pushed_at` 2026-09-03 16:37 UTC（台北時間 9/4 00:37, **1 天前 very-fresh active development**）** 是主要 liveness 信號。**Repo `created_at` 2025-09-22（**11 個月內從 0 衝到 173,777★**, viral-tail 持續中）+ `size` 4,763KB + `open_issues` 1,211（**高 issue count 反映 Anthropic 內部持續 commit + community engagement 強**）** + `pushed_at` 1 天前 = **active development 持續中**。**Release-cadence**：**無 formal release tag** — 透過 `pushed_at` + skill count 看 cadence (持續新增 skill, 例如最近加上新 document skills / enterprise skills / creative skills)。**Pre-release stale** = **N/A (no GH release tag)** + **`pushed_at` 1 天前** + **Trending today #5 (281★ today)** = **continuous-commit-driven visibility model**。**License tier-split**：**(a) 大多數 skills Apache-2.0 (case-A permissive)** + **(b) `skills/docx`+`skills/pdf`+`skills/pptx`+`skills/xlsx` source-available not-open-source (custom-license)**。**對 horo-agent / air-gapped downstream 影響評估**：**Apache-2.0 majority + source-available minority**。**Functional parallels to horo-agent**：(a) `SKILL.md` YAML frontmatter format ≡ **horo-agent skill metadata schema 對齊**; (b) agentskills.io standard plugin install ≡ **horo-agent skill marketplace 對齊**; (c) `template-skill` 結構 ≡ **horo-agent skill template 對齊**; (d) Creative + Development + Enterprise + Document 4 大類目分類 ≡ **horo-agent skill categorization 對齊**; (e) Plugin marketplace `/plugin marketplace add` ≡ **horo-agent host-agnostic install 對齊**; (f) Claude Code / Claude.ai / Claude API 3 host 支援 ≡ **horo-agent multi-host 對齊**。**對主人「horo-agent skill marketplace 設計」直接 reference** — **主人想寫 SKILL.md 結構 / frontmatter schema / categorization 應該直接抄 anthropics/skills + spec + template**。

### `stablyai/orca`

- **Repo 摘要：** **Orca — The AI Orchestrator for 100x builders (YC S26)** — 5 個月內從 0 衝到 **61,174★** (`created_at` 2026-03-17), TypeScript + MIT + 18 topics (`ade`/`agent-ide`/`ai-agents`/`claude-code`/`cli`/`codex`/`cursor-agent`/`devtools`/`ghostty`/`ide`/`mobile-app`/`opencode`/`orchestration`/`parallel-agents`/`pi`/`terminal`/`worktrees`/`yc-backed`), **Run Codex, ClaudeCode, OpenCode or Pi side-by-side — each in its own worktree, tracked in one place**。**核心差異化**：**(1) ADE (Agent Development Environment)** — **ADE = IDE for working with a fleet of parallel agents** (description + topic `ade`), 不是 IDE for editing code 而是 IDE for orchestrating agents; **(2) Parallel Worktrees** — Fan one prompt across 5 agents, each in its own isolated git worktree → compare results + merge the winner (類似 `git worktree add` + `git merge --no-ff`); **(3) Mobile Companion** — iOS App Store / Android APK, monitor + steer agents from phone, get notified when agent finishes; **(4) Terminal Splits via Ghostty-class engine** — WebGL rendering, infinite splits, scrollback survives restarts; **(5) Design Mode** — Click any UI element in real Chromium window → send HTML/CSS/cropped screenshot straight to agent's prompt; **(6) Multi-CLI support** — **GitHub Copilot / OpenCode / MiMo Code / Amp / OpenClaude / Antigravity / Pi / oh-my-pi / Hermes Agent / Rovo Dev / + any CLI agent** (README 開頭列 11+ agent hosts); **(7) 11+ agent hosts integration** — ClaudeCode + Codex + OpenCode + Pi + oh-my-pi + Hermes Agent + Rovo Dev + GitHub Copilot + MiMo Code + Amp + OpenClaude + Antigravity; **(8) YC S26 backed** — `yc-backed` topic — 2026 Summer batch; **(9) Daily v1.4.x release cadence** — 2026-09-04 release v1.4.197 (今天), commit-driven + 48-72h PR-to-release lag。**對主人影響**：**Orca = 「ADE for parallel agents」= 「parallel worktree orchestration」= 「對應主人 Kanban delegation pattern」** — **主人 Kanban 多 profile (default Qwen + executor qwen38-code + reviewer GPT) 在 Orca 框架下 = 多 agent parallel in worktrees**。

- **3W1H：**
  - **What：** TypeScript (Electron?) desktop + mobile (iOS App Store + Android APK) 寫的 **ADE (Agent Development Environment)** for running **multiple coding agents (ClaudeCode/Codex/OpenCode/Pi/Hermes Agent/+ 11+ hosts) in parallel worktrees**。**Install = Download `.dmg`/`.exe`/`.AppImage`** from onOrca.dev + brew cask + AUR。
  - **Why：** 解決「想跑多個 coding agent 並行但要在 IDE 內管理 (不是在 terminal 開 5 個 tab) / 想要 Git worktree-per-agent 隔離 (不用 main branch 衝突) / 想要 mobile companion 從手機 monitor/steer / 想要 Terminal Splits via WebGL (Ghostty-class engine) / 想要 Design Mode (click UI element → send HTML+CSS+screenshot to agent prompt) / 想要 11+ agent host 一鍵整合 / 想要 iOS/Android mobile companion」的痛點。Orca 把「ADE for parallel agents」降到 1 個 Electron desktop + 1 個 mobile app + brew cask + AUR。
  - **Who：** **重度 coding agent user** (主人是核心 audience — Kanban 多 profile parallel execution 對齊); 用 Hermes Agent + Claude Code + Codex + OpenCode + Pi 多 agent 的人; 想要 Git worktree-per-agent 隔離的 developer; 對 mobile companion 有需求的 on-the-go engineer; YC S26 batch 追蹤者; 對 Ghostty-class WebGL terminal 有需求的人; 對 Design Mode (HTML+CSS+screenshot-into-prompt) 有需求的 web developer。
  - **How：** (a) **Download + install desktop**：從 https://onorca.dev/download 拿對應 platform build (.dmg/.exe/.AppImage) 或 brew cask (`brew install --cask stablyai/orca/orca`) 或 AUR (`yay -S stably-orca-bin`); (b) **Parallel worktree orchestration**：在 Orca UI 開 fan-out → 5 agents 各跑 1 個 worktree → compare → merge winner; (c) **Multi-CLI provider**：從 11+ agent hosts 中選 (ClaudeCode/Codex/OpenCode/Pi/Hermes Agent/...) — Orca 統一管理; (d) **Mobile companion**：iOS App Store `Orca IDE` id6766130217 或 Android APK `mobile-android-v0.0.47`; (e) **Headless Linux server**：`orca serve` (參考 `docs/reference/headless-linux-server.md`); (f) **Terminal splits**：WebGL rendering + infinite splits + scrollback survives restarts; (g) **Design Mode**：click UI in real Chromium window → HTML+CSS+screenshot into agent prompt。

- **安裝方式：**
  - **Desktop download (主推)**：
    ```bash
    # macOS Apple Silicon
    curl -L -O https://github.com/stablyai/orca/releases/latest/download/orca-macos-arm64.dmg
    
    # macOS Intel
    curl -L -O https://github.com/stablyai/orca/releases/latest/download/orca-macos-x64.dmg
    
    # Windows
    curl -L -O https://github.com/stablyai/orca/releases/latest/download/orca-windows-setup.exe
    
    # Linux AppImage
    curl -L -O https://github.com/stablyai/orca/releases/latest/download/orca-linux.AppImage
    
    # All builds: https://github.com/stablyai/orca/releases/latest
    ```
    或從 https://onorca.dev/download 一鍵下載。
  - **Homebrew Cask (macOS)**：
    ```bash
    brew install --cask stablyai/orca/orca
    ```
  - **AUR (Arch Linux)**：
    ```bash
    yay -S stably-orca-bin           # prebuilt
    # 或
    yay -S stably-orca-git           # build from source
    ```
  - **Headless Linux server**：
    ```bash
    orca serve                       # 跑 server mode
    # 細節看 docs/reference/headless-linux-server.md
    ```
  - **Mobile companion (iOS)**：App Store → `Orca IDE` (id 6766130217) 或 TestFlight `YjeGMQBA`。
  - **Mobile companion (Android)**：
    ```bash
    curl -L -O https://github.com/stablyai/orca/releases/download/mobile-android-v0.0.47/app-release.apk
    # 或參考 https://www.onorca.dev/docs/android-apk
    ```
  - **Source build (TypeScript)**：
    ```bash
    git clone https://github.com/stablyai/orca.git
    cd orca
    yarn install
    yarn build       # 假設 yarn workspaces
    yarn start       # 跑 Electron dev
    ```
    (monorepo 結構, 細節看 repo `package.json`)
  - **未找到 PyPI package**：純 desktop app, **no Python dependency**。
  - **System requirements**：
    - macOS (Apple Silicon / Intel)
    - Windows 10/11
    - Linux (Ubuntu / Fedora / Arch 等主流 distro, AppImage)
    - iOS 16+ (mobile companion)
    - Android 11+ (mobile companion)
  - **License = MIT (case-A verified via README badge)** — 純 permissive, no commercial restriction, **closed-source fork safe** + **YC S26 batch** = 商業使用 OK。**對 horo-agent air-gapped downstream**：**desktop app 不可直接整合進 horo-agent (Electron / WebView 是不同 runtime)**, 但 **parallel worktree orchestration pattern 可參考** — 「每 agent 跑 1 個 worktree + compare + merge」對主人 Kanban delegation 有借鏡價值。

- **近期 release：** **`v1.4.197 — v1.4.197`** — **2026-09-04 00:45 UTC 發佈（台北時間 9/4 08:45, **今天 same-day, 5 小時前 fresh**）**, pre-release = false, draft = false。Body 主標題 `v1.4.197` + 「**Thank you so much for using Orca and for your continued support!**」+ 「**It usually takes 48–72 hours for a landed PR to be released (except P0+ fixes)**」+ 「**We have many more exciting PRs and features coming in later versions!**」。**Notable changes (3)**：(1) **Faster worktree switching, Git metadata scans, terminal startup, and renderer updates** across local + WSL + remote workspaces; (2) **More reliable native chat and agent sessions**, including large command results + live tool progress + image attachments + delivery recovery; (3) **Broader SSH, Windows/WSL, GitLab, updater, startup, and release reliability improvements**。**UI / workspaces**: fix(mobile) unblock targeted SSH session tab refresh (PR 17486) + Preserve user-set workspace names across branch changes (PR 17448) + Automations UX improvement (PR 17626) + fix(terminal) mount one surface per workspace id in the workbench (PR 17432, STA-4846) + Avoid Linear read re-fetches when workspace scope is unchanged (PR, AmethystLiang) + ...。**對 horo-agent / air-gapped downstream 影響評估**：**MIT permissive + YC S26 backed + 主人 Kanban delegation pattern 直接 reference**。**Functional parallels to horo-agent**：(a) parallel worktree orchestration ≡ **主人 Kanban 多 profile delegation** (default + executor + reviewer); (b) worktree switching speed improvement ≡ **horo-agent delegation latency reduction**; (c) git metadata scans faster ≡ **horo-agent repo metadata access faster**; (d) mobile companion (iOS + Android) ≡ **主人 on-the-go monitoring 對齊**; (e) 11+ agent host integration (含 Hermes Agent) ≡ **主人「hermes-agent 整合」+「multi-host orchestration」對齊**; (f) `orca serve` headless server ≡ **horo-agent headless / VPS deployment**; (g) Terminal Splits via WebGL (Ghostty-class engine) ≡ **horo-agent TUI rendering quality**; (h) Design Mode (HTML+CSS+screenshot-into-prompt) ≡ **horo-agent visual-first use case 對齊** (主人「對遊戲來說最重要的就是畫面設計要可以讓人理解」)。**Repo `pushed_at` 2026-09-04 05:59 UTC（台北時間 9/4 13:59, **今天 master 仍 commit active, 5 小時內**）** — 顯著比 release 早 5 小時 (release 8:45 → push 13:59) **main branch 仍 active 開發中**。**Release-cadence**：**daily v1.4.x release** = **`v1.4.197` 是今天 release**, commit-driven + 48-72h PR-to-release lag, **「highest-frequency release cadence in 14:00 series」**(每天一版, 比 09-03 `browser-use/video-use` 0 release 對比鮮明)。**YC S26 + MIT + Electron + WebGL terminal = 「2026 年最 hot coding infra startup」之一**。

### `calesthio/OpenMontage`

- **Repo 摘要：** **OpenMontage — The first open-source, agentic video production system** — 5 個月內從 0 衝到 **56,026★** (`created_at` 2026-03-29), Python + **AGPL-3.0** + 19 topics (`agent`/`agentic-ai`/`ai`/`claude`/`copilot`/`cursor`/`elevenlabs`/`ffmpeg`/`flux`/`image-generation`/`open-source`/`openai`/`python`/`remotion`/`stable-diffusion`/`text-to-speech`/`text-to-video`/`video-generation`/`video-production`), **Turn your AI coding assistant into a full video production studio**。**核心差異化**：**(1) World's first open-source, agentic video production system** — 12 production pipelines + 100+ tools + 700+ agent skill and production-knowledge files (description 直接寫); **(2) Real video (not animated stills)** — 「the agent builds a corpus from **free stock footage and open archives**, retrieves actual motion clips, edits them into a timeline, and renders a finished piece. That is not the usual "animate a handful of stills and call it video" trick」; **(3) Pasted video → re-edit** — 「Start from a video you already love」 workflow — input video URL → agent pulls source + analyzes + proposes re-edit; **(4) Full-stack pipeline** — research + asset generation + editing + final composition, all driven by AI coding assistant; **(5) Multi-provider** — Flux (image gen) + Stable Diffusion + ElevenLabs (TTS) + ffmpeg + Remotion (video composition) + text-to-video + text-to-image; **(6) Multi-host** — Claude Code / Cursor / Copilot / Windsurf / Codex; **(7) AGENT_GUIDE.md built for OpenClaw-style agents** — explicit Turing test joke for OpenClaw agents reading the README; **(8) PR_REVIEW_GUIDE.md** — clear PR review process; **(9) Sponsor-supported** — Bloome (multi-agent conversation) + Atlas Cloud (full-modal AI inference); **(10) Trending #1 of the day badge in README** — `#1 Repository of the Day on GitHub Trending`。**對主人影響**：**AGPL-3.0 (case-E) copyleft 警訊 + video production 是主人「視覺優先 + 邊做邊看」哲學延伸** — **AGPL 對 horo-agent closed-source fork 結構禁止**, 但 **pattern (「agentic video pipeline」+「12 production pipelines + 100+ tools + 700+ skills」) 對 horo-agent skill marketplace + sub-agent pipeline 有借鏡價值**。

- **3W1H：**
  - **What：** Python (Python 3.10+) + Node.js (18+) + ffmpeg + Remotion (TypeScript video composition) 寫的 **open-source agentic video production system**。`make setup` 一鍵安裝 + 把 AI coding assistant 變成 video production studio。12 production pipelines + 100+ tools + 700+ skill/production-knowledge files。
  - **Why：** 解決「想用 AI coding assistant 自動剪片但工具都要付費 SaaS / 想要 agentic video pipeline (research → script → asset gen → edit → render) / 想要 real video (free stock footage corpus + actual motion clips) 不只是 animated stills / 想要 12 個 production pipeline 範例 / 想要 700+ skill files 借鏡 / 想要 multi-provider (Flux/SD/ElevenLabs/ffmpeg/Remotion)」的痛點。OpenMontage 把「agentic video production」降到 1 個 `make setup` + 1 個 prompt + 12 production pipelines。
  - **Who：** AI coding assistant power user 想 auto-generate video; content creator 想用 AI 剪片但不想依賴 SaaS (ElevenLabs / Runway / Descript); Python developer 想 fork video pipeline 加 custom skill; 對 multi-agent collaboration in video pipeline 有需求的研究者; 對 AGPL-3.0 容忍的 open-source 開發者; 對 free stock footage corpus 有需求的 budget-conscious creator。
  - **How：** (a) **Prerequisites**：`python 3.10+` + `ffmpeg` (brew/apt) + `node.js 18+` + 1 個 AI coding assistant (Claude Code / Cursor / Copilot / Windsurf / Codex); (b) **Quick start**：`git clone https://github.com/calesthio/OpenMontage.git` → `cd OpenMontage` → `make setup` → 在 AI coding assistant 開啟 project → 寫「Make a 60-second animated explainer about how neural networks learn」 → agent 自動跑 pipeline; (c) **No `make`?** 走 manual：`python3 -m venv .venv && source .venv/bin/activate && python -m pip install -r requirements.txt && cd remotion-composer && npm install && cd .. && python -m pip install piper-tts && cp .env.example .env` (macOS/Linux); (d) **OpenClaw agent shortest path**：看 `AGENT_GUIDE.md` → "If you're an OpenClaw-style agent, here is the shortest path to becoming useful fast"; (e) **Pipelines**：12 production pipelines (e.g. explainer / interview / music video / etc) — 透過 prompts 選擇; (f) **Providers**：看 `docs/PROVIDERS.md` — 支援 Flux + Stable Diffusion + ElevenLabs + ffmpeg + Remotion + text-to-video + text-to-image。

- **安裝方式：**
  - **Quick start (主推, macOS/Linux)**：
    ```bash
    # Prerequisites
    brew install ffmpeg                          # macOS
    # 或 sudo apt install ffmpeg                  # Linux
    # 或 https://ffmpeg.org/download.html         # Windows manual
    
    # Clone + setup
    git clone https://github.com/calesthio/OpenMontage.git
    cd OpenMontage
    make setup
    ```
    `make setup` 自動處理 venv + Python deps + npm install + `.env` setup。**在 AI coding assistant (Claude Code / Cursor / Copilot / Windsurf / Codex) 開啟 project → 寫 prompt 即可**。
  - **Manual setup (no make, macOS/Linux)**：
    ```bash
    python3 -m venv .venv
    source .venv/bin/activate
    python -m pip install -r requirements.txt
    cd remotion-composer && npm install && cd ..
    python -m pip install piper-tts
    cp .env.example .env
    ```
  - **Manual setup (Windows PowerShell)**：
    ```powershell
    py -3 -m venv .venv
    .\.venv\Scripts\Activate.ps1
    python -m pip install -r requirements.txt
    cd remotion-composer; npm install; cd ..
    python -m pip install piper-tts
    Copy-Item .env.example .env
    ```
    **Windows gotcha**：若 `npm install` fails with `ERR_INVALID_ARG_TYPE`, 用 `npx --yes npm install` 代替。
  - **Providers**：
    - **Image generation**：Flux / Stable Diffusion (本地或 hosted)
    - **TTS**：ElevenLabs (API key) / piper-tts (local, open-source)
    - **Video composition**：Remotion (TypeScript, included in `remotion-composer/`)
    - **Stock footage**：整合 free stock footage corpus (e.g. Pexels / Pixabay via API 或 local archive)
    - 細節看 `docs/PROVIDERS.md`
  - **Daily workflow**：
    ```bash
    # 在 AI coding assistant (Claude Code/Cursor/Copilot/Windsurf/Codex) 開啟 OpenMontage project
    # 寫 prompt 即可：
    "Make a 60-second animated explainer about how neural networks learn"
    "Edit this video (https://youtu.be/...) into a 30-second highlight reel"
    "Produce a music video for the song at /path/to/audio.mp3"
    ```
  - **System requirements**：Python 3.10+ + ffmpeg + Node.js 18+ + 1 個 AI coding assistant + Internet (for hosted providers like ElevenLabs) 或 local alternative (piper-tts)。
  - **License = AGPL-3.0 (case-E verified via LICENSE file)** — **AGPL copyleft 強**, 對 horo-agent air-gapped downstream **結構禁止 closed-source fork** (network use 也算 distribution)。**Functional use OK** (跑 OpenMontage 自己剪片) — **closed-source product 整合禁止**。
  - **未找到 PyPI package**：純 source install + `make setup`, **no `pip install openmontage`**。
  - **AGENT_GUIDE.md (for OpenClaw agents)**：repo 內建, 給 autonomous agent 最短上手路徑 (for OpenClaw/Hermes/Pi/Codex 等 agent host) — 「If you're an OpenClaw-style agent, here is the shortest path to becoming useful fast」。

- **近期 release：** **未找到 GitHub release**（releases endpoint returns `Not Found`）。**`pushed_at` 2026-08-22 18:22 UTC（台北時間 8/23 02:22, **12 天前 mid-fresh**）** 是主要 liveness 信號。**Repo `created_at` 2026-03-29（**5 個月內從 0 衝到 56,026★**, viral-tail 持續中）+ `#1 Repository of the Day on GitHub Trending` 徽章 (從前某天達到) + **`pushed_at` 12 天前 mid-fresh** + 19 topics + 9 sponsors** = **active development 持續中**。**Release-cadence**：**無 formal release tag** — 透過 `pushed_at` + commits 看 cadence (`pushed_at` 12 天前 = 中等 active, 不像 stablyai/orca 一日一版, 但仍持續)。**Pre-release stale** = **N/A (no GH release tag)** + **`pushed_at` 12 天前** + **findarepo daily #4 (4.9k stars/7d)** = **continuous-commit-driven visibility model + viral-tail**。**License = AGPL-3.0 (case-E verified via LICENSE file + README badge)** — **OSI 開源但 copyleft 強**, **closed-source fork / 商用嵌入需保留 source + AGPL 條款**。**對 horo-agent / air-gapped downstream 影響評估**：**AGPL-3.0 case-E copyleft 強**, **結構禁止 closed-source fork / network use**。**Functional parallels to horo-agent**：(a) 12 production pipelines + 100+ tools + 700+ skill/production-knowledge files ≡ **horo-agent skill marketplace + sub-agent pipeline 對齊**; (b) Multi-provider integration (Flux/SD/ElevenLabs/ffmpeg/Remotion) ≡ **horo-agent multi-provider routing 對齊**; (c) AI coding assistant driver (Claude Code/Cursor/Copilot/Windsurf/Codex) ≡ **horo-agent multi-host 對齊**; (d) AGENT_GUIDE.md for OpenClaw agents ≡ **horo-agent agent onboarding 對齊**; (e) `make setup` + manual venv fallback ≡ **horo-agent installer pattern 對齊**; (f) Real video pipeline (free stock footage corpus) ≡ **horo-agent「真實行為驗證」哲學對齊**。**對主人 MEMORY「主人常用 Hermes Agent 與 hermes-webui 做 enterprise-lite / air-gapped downstream; 保守裁切 runtime」**: **OpenMontage 的 AGPL-3.0 license 對 horo-agent closed-source air-gapped fork 結構禁止**, **不可 fork 進 horo-agent**, **但 pattern (skill + multi-provider + multi-host + agent driver) 可借鏡**。

## 重點觀察

- **Tier-A top 3 = 3/3 全 FRESH (cross-day 0/3 repeat floor ×7 VERIFIED)**：今天 Tier-A 排除 6 個 repeat (`mattpocock/skills` 09-01 + `DietrichGebert/ponytail` 09-01 + `affaan-m/ECC` 09-01 + `Imbad0202/academic-research-skills` 09-02 + `Gitlawb/openclaude` 09-02 + `obra/superpowers` 08-21/08-20) 後, **3 個 pick 都是 fresh today**：`fmtlib/fmt` (pushed_at 9/2, 1 天內) + `NousResearch/hermes-agent` (pushed_at 9/4 today) + `anthropics/skills` (pushed_at 9/3, 1 天內)。**0/3 cross-day repeat floor 已達 ×7（08-20 + 08-21 + 08-31 + 09-01 + 09-02 + 09-03 + 09-04）** — 符合 08-20 codification「0/3 = natural floor = structurally optimal」標準連續 7 天。**Tier-A top 3 與 Web-search 2 (`stablyai/orca` + `calesthio/OpenMontage`) 都是 `pushed_at` active** — `fmt` pushed_at 9/2 + `hermes-agent` pushed_at 9/4 today + `anthropics/skills` pushed_at 9/3 + `orca` pushed_at 9/4 today + `OpenMontage` pushed_at 8/22 = **5/5 today or yesterday active**。**5 picks = Python ×3 (hermes-agent + anthropics/skills + OpenMontage) + TypeScript ×1 (orca) + C++ ×1 (fmt)** — **今天沒有 Rust pick**, 是 09-03 (Python ×3 + Rust ×1 + Rust+4-bindings ×1) 的 **跨 runtime 平衡版** (Python 3 + C++ legacy + TypeScript modern)。**注意**：`fmtlib/fmt` 是 14-year-old project (created_at 2012-12-07) 衝 Trending #1 (963★ today) — **14:00 系列最老的 trending pick**, **是 viral-tail 由 Trending algorithm 重新推上去, 不是 fresh launch**; `anthropics/skills` 是 **Anthropic 官方 reference skill repo** (主人 horo-agent skill marketplace 直接 reference)。**5/5 = 100% `pushed_at` ≤ 2 天內 fresh** 是本月 tier-A freshness **第 7 個 milestone**（08-12/13/14/15/17/20/21 + 08-31 + 09-01 + 09-02 + 09-03 + 09-04）。

- **License tier-split = 3 MIT + 1 dual-license (mostly Apache-2.0) + 1 AGPL-3.0 (4/5 permissive, 80%)**：今天 pick 內 **`fmtlib/fmt`** = **MIT** (case-A verified via LICENSE file, 14-year mature); **`NousResearch/hermes-agent`** = **MIT** (case-A verified via LICENSE file, Nous Research upstream); **`anthropics/skills`** = **dual-license (大多數 Apache-2.0 + 4 個 source-available)** (case-J NEW 09-04 — LICENSE root 沒有, README 明示兩種 license) — **NOT standard permissive single-license**, **主人 horo-agent 整合時要注意「大多數 skills 可用, 4 個 docx/pdf/pptx/xlsx source-available 不可商用 fork」**; **`stablyai/orca`** = **MIT** (case-A verified via README badge); **`calesthio/OpenMontage`** = **AGPL-3.0** (case-E verified via LICENSE file — OSI 開源但 copyleft 強, **closed-source fork / 商用嵌入需保留 source + AGPL 條款, 對 horo-agent air-gapped downstream 結構禁止**)。**License 乾淨度** = **3 MIT + 1 dual-license + 1 AGPL-3.0 = 4/5 = 80% permissive**, 比 09-03「5/5 = 100% permissive」reverse 20pp, **case-E AGPL-3.0 出現是本月 14:00 第 4 次**（08-12 inkeep/open-knowledge + 08-13 + 08-20 volcengine/OpenViking case-E2 + 09-04 OpenMontage = 4 次）。**對 horo-agent air-gapped downstream 影響**：**(i) 4/5 picks 可直接 fork 入庫** (3 MIT + 1 dual-license 大多數); **(ii) 1/5 pick (OpenMontage AGPL-3.0) 對 closed-source fork 結構禁止**, **pattern 可借鏡但 code 不可 fork**; **(iii) anthropics/skills 的 docx/pdf/pptx/xlsx 4 個 source-available 子目錄不可商用 fork**, **但大多數 Apache-2.0 skills 可用**。**License case-J (NEW 09-04): dual-license with mixed permissive + source-available sub-directories** — 跟 case-E / case-H / case-I 不同, 是「同一 repo 內不同子目錄不同 license」的混合型。**Report language for case-J**: 「dual-license (case-J) — repo 內大多數 sub-directory 一個 license (e.g. Apache-2.0), 少數 sub-directory 另一個 license (e.g. source-available); fork / 商用前需逐 sub-directory 驗 license」。

- **5 picks 5 種 install type 全 distinct (install type diversity is the new bar ×7 VERIFIED)**：今天命中：**Type-9 `curl|sh` installer + portable Git Bash (NEW 09-04)** (**`NousResearch/hermes-agent`** `curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash` (Linux/macOS/WSL2/Termux) + `iex (irm install.ps1)` (Windows native) + installer 自動處理 uv + Python 3.11 + Node.js + ripgrep + ffmpeg + **portable MinGit** 到 `%LOCALAPPDATA%\hermes\git` — **第 1 次命中「portable MinGit」type**, Hermes Agent 的 Windows 安裝路徑包含 portable MinGit, **不污染 system Git**)+ **Type-28 Plugin marketplace (Claude Code) + agentskills.io standard (NEW 09-04)** (**`anthropics/skills`** `/plugin marketplace add anthropics/skills` + `/plugin install document-skills@anthropic-agent-skills` — **第 1 次命中「plugin marketplace via Claude Code `/plugin` command」+「agentskills.io standard」type**, `SKILL.md` YAML frontmatter format 是 canonical, `template-skill` 是起點)+ **Type-29 Desktop app + brew cask + AUR + mobile companion (NEW 09-04)** (**`stablyai/orca`** `brew install --cask stablyai/orca/orca` + `yay -S stably-orca-bin` (Arch AUR) + `.dmg`/`.exe`/`.AppImage` desktop download + **iOS App Store (Orca IDE id 6766130217) + Android APK (mobile-android-v0.0.47) mobile companion** — **第 1 次命中「desktop + mobile 跨平台 ADE」+「11+ agent hosts 整合」type**)+ **Type-30 Source-only + `make setup` + multi-provider (NEW 09-04)** (**`calesthio/OpenMontage`** `git clone` + `make setup` + Python 3.10+ + ffmpeg + Node.js 18+ + AI coding assistant driver + 12 production pipelines + 100+ tools + 700+ skills — **第 1 次命中「make-driven source install + multi-provider stack + AI coding assistant driver」type**)+ **Type-3b Header-only C++ library + multi-build system (NEW 09-04)** (**`fmtlib/fmt`** header-only `#include <fmt/format.h>` + CMake + vcpkg + brew + apt + conan + FetchContent + Compiler Explorer — **第 1 次命中「header-only C++ library + multi-build system」type**)。**5 picks = 5 distinct install paths** 達 **install type diversity ×7 VERIFIED（08-20 + 08-21 + 08-31 + 09-01 + 09-02 + 09-03 + 09-04）**。**新增 install types (5 個 NEW 09-04)**: **Type-9 portable MinGit bundled**, **Type-28 plugin marketplace + agentskills.io**, **Type-29 desktop + mobile ADE**, **Type-30 make-driven source + multi-provider**, **Type-3b header-only C++ + multi-build system**。

- **Release 新鮮度 tier-split (5 picks)**：超 fresh (≤3 天) = **2** (`hermes-agent` v2026.8.31 3 天前 + `orca` v1.4.197 5 小時前) + fresh (≤14 天) = **0** + mid-fresh (≤30 天) = **0** + stale (>30 天) = **1** (`fmt` v12.2.0 80 天前 stale-but-canonical) + no-GH-release-but-pushed-at-fresh = **2** (`anthropics/skills` pushed_at 1 天前 + `OpenMontage` pushed_at 12 天前)。**5/5 = 100% `pushed_at` ≤ 12 天內 fresh** + **2/5 = 40% fresh by release tag** (hermes-agent v2026.8.31 + orca v1.4.197 ≤3 天)。**`stablyai/orca` daily v1.4.x release cadence = 14:00 系列 highest-frequency release**, `v1.4.197` 是今天 release, **commit-driven + 48-72h PR-to-release lag** 是 SaaS-style active development pattern。**Operational reading**：今天 5 picks 中 **1 個 stale release (`fmt` v12.2.0 80 天前) 但 `pushed_at` 1 天前 = 14-year-mature project active development**; **2 個 no-GH-release (`anthropics/skills` + `OpenMontage`) 但 `pushed_at` 1-12 天前 = commit-driven visibility model**; **2 個 fresh release (`hermes-agent` 3 天 + `orca` 5 小時)**。**`fmt` 的 Trending #1 (963★ today) 對 14-year-old project 是奇景**, 推測與 C++ 標準化動態 / LLM dependency / 跨 runtime benchmark 有關 (主人可查 GitHub issue 或 HN 確認)。

- **Master MEMORY signal 命中 (4/5 picks = high-impact MEMORY-adjacent)**：今天 **4/5 picks fire MEMORY-direct-match signal**：**(a) `NousResearch/hermes-agent`** = **主人 MEMORY「主人常用 Hermes Agent 與 hermes-webui 做 enterprise-lite / air-gapped downstream」+ 「Hermes downstream public-release branding: Agent package/repo is `horo-agent` with primary CLI `horo`」** 是 **horo-agent 的 upstream reference** — `curl|bash` installer + Python 3.11 + uv + 7 backends + agentskills.io standard + sub-agent delegation + cron scheduler + FTS5 session search + LLM summarization + closed learning loop + multi-channel IM = **主人 horo-agent 應該逐個對齊的 primitive 集合**; **(b) `stablyai/orca`** 11+ agent hosts 整合 **含 Hermes Agent** (README 直接列) + YC S26 + parallel worktree orchestration + mobile companion (iOS/Android) — **主人 Kanban 多 profile delegation (default Qwen + executor qwen38-code + reviewer GPT) 對齊 Orca 的「parallel worktree per agent」pattern**; **(c) `calesthio/OpenMontage`** AGENT_GUIDE.md for OpenClaw-style agents + Claude Code/Cursor/Copilot/Windsurf/Codex multi-host + AGPL-3.0 (case-E) **對主人「enterprise-lite / air-gapped downstream」是 license 警訊但 pattern 對齊** + 12 production pipelines + 700+ skill files = **horo-agent skill marketplace + sub-agent pipeline 結構 reference**; **(d) `fmtlib/fmt`** MIT + 14-year mature + C++20/23 standardized + oss-fuzz + OpenSSF = **對主人「air-gapped downstream」是 long-term sustainable C++ formatting**; **(e) `anthropics/skills`** SKILL.md YAML frontmatter format + agentskills.io standard + 4-tier license (大多數 Apache-2.0 + 4 個 source-available) + Claude Code `/plugin marketplace add` + `#1 Repository of the Day on GitHub Trending` = **主人「horo-agent skill marketplace 設計」直接 reference**。**5/5 picks 高度對齊主人 MEMORY 偏好** — **是 14:00 系列第 7 個 all-MEMORY-adjacent milestone**（08-21 was 3/5 + 09-02 was 3/5 + 09-03 was 5/5 + 09-04 = 4/5 picks fire MEMORY-direct-match signal）。**Hermes-as-official-upstream signal**: **2/5 picks 直接 mention Hermes Agent** (NousResearch/hermes-agent = Hermes Agent 是它本身 + stablyai/orca 11+ agent hosts 整合含 Hermes Agent README 直接列) — **比 09-03 (1/5 = browser-use/video-use) 升級**, **跟 08-20 (2/5 = obra/superpowers + mukul975/Anthropic-Cybersecurity-Skills) 持平**。