---
title: GitHub 自由探索 2026-09-16（14:00 台北時間）
date: 2026-09-16
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 7 天 repeat）
  - web_search 當日 AI 開源新聞（Tier-B web 探索 2）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md`
---

# GitHub 專案動態

- 檢查時間：2026-09-16（14:00 台北時間）
- 檢查對象：`addyosmani/agent-skills` / `pacifio/atlas` / `danny-avila/LibreChat` / `huggingface/funes` / `MemTensor/MemOS`
- 來源組合：GitHub Trending today 共解析 14 筆，**前 5 名全 repeat**（rank 1 `alibaba/open-code-review` + rank 2 `JustVugg/colibri` + rank 3 `ever-co/ever-gauzy` + rank 4 `debpalash/VoiceStudio` + rank 5 `Homebrew/BrewUI` 中前 4 名 7 天內已寫過），其下 `melgarafael/DeskcommCRM`、`nab138/iloader`、`max-sixty/worktrunk`、`vxcontrol/pentagi`、`p1neappleXpress/OpenFlux` 也都是 7 天內 repeat。**Fresh top 3 = rank 5 `Homebrew/BrewUI`（Swift、Homebrew official macOS GUI、271★/day）+ rank 8 `danny-avila/LibreChat`（TS、Enhanced ChatGPT Clone with Agents/MCP/Skills、254★/day）+ rank 12 `addyosmani/agent-skills`（JS、Google Chrome team 「Production-grade engineering skills for AI coding agents」、307★/day）**——三個 domain 完全不重疊：macOS 桌面 / AI chat gateway / AI coding agent skill pack。再下一層 `pacifio/atlas`（rank 10、Rust、source control for coding agents）雖然 trending 排名更前，但選擇 `addyosmani/agent-skills` 是因 skill pack 跟主人 agent 主軸對位更緊密；atlas 移到 web search 備選。Tier-B web_search 當日 AI 開源新聞取到 **`huggingface/funes`**（Apache-2.0、420★、`v1.3.0` published 2026-09-01、Hermes Agent 也列入支援：`funes add hermes`、「durable memory for your AI coding agents」，明確對位主人 Hermes 主軸）+ **`MemTensor/MemOS`**（Apache-2.0、11,363★、`v2.0.33` published 2026-09-03、MemOS Hermes Local Plugin 已上架、「Self-evolving memory OS for LLM & AI Agents」）。注意：web search 結果中的 `hijzy/MemOS` 是 mirror/fork（2★、2026-08-24 後未 push），採用 canonical `MemTensor/MemOS`。

---

## Repo 摘要與 3W1H

### `addyosmani/agent-skills`

- **Repo 摘要：** Google Chrome 團隊 Addy Osmani 個人發起的「**給 AI coding agent 的 production-grade skill pack**」——把「資深工程師在 software lifecycle 會做的事」編碼成 25 個結構化 workflow skills（spec → plan → build → verify → review → ship），每個 skill 是 `SKILL.md` + steps + verification gates + anti-rationalization tables；agent 透過這些 skill 在 spec-driven development、TDD、incremental implementation、code review、adversarial debugging 等場景自動遵守紀律。94,957★、JavaScript、1.1 MB、MIT、`created` 2026-02-15、`pushed_at` 2026-09-12 03:15 UTC（4 天前）。裝法最簡單：`npx skills add addyosmani/agent-skills`（Vercel Labs 的 skills CLI 一次裝進 70+ 個 agent：Claude Code / Cursor / Codex / Copilot / Cline / Windsurf / OpenCode / Gemini / Kiro / Code Command / Antigravity）。**對主人的對位：跟昨日 `caura-ai/caura` MCP server 是同一條 curve——把「agent 需要的能力」打包成可重用的 SKILL.md 模組；horo-agent 若要把經驗沉澱給下游 agent，addy 的 25 個 skill 是最佳 reference implementation（per-agent frontmatter portable + metadata vendor-specific runtime controls 的分離 pattern 已 0.6.9 正式 codify）。**
- **3W1H：**
  - **What：** 一個 monorepo + 25 個 SKILL.md + 對 11 個 agent 的整合文件 + 1 個 meta-skill（`using-agent-skills` 自動 map 收到的 task 到正確 skill）。
  - **Why：** AI coding agent 真正 production 化的瓶頸不是模型，是「senior engineer 在 software lifecycle 的紀律——spec 先寫、test 先寫、diff 控制在 ~100 行、code review 要走五個 axis、debug 走五步 triage——這套紀律目前散落在每個人腦中」。Addy 把這套紀律 codify 成 SKILL.md，agent 讀了就能依樣執行，跨 session 跨專案可重用。
  - **Who：** 個人 AI 重度使用者（想要 production-grade workflow 而不是 chat-only）、engineering team 想統一 AI coding 紀律的 tech lead、做 agent benchmark 的人（25 個 skill 是 eval material 來源）、研究「如何讓 AI agent 自動遵守紀律」的研究者（anti-rationalization table 是 anti-bypass mechanism 的 reference）。
  - **How：** `npx skills add addyosmani/agent-skills` 一行裝全部 25 個 skill；或 `npx skills add addyosmani/agent-skills --skill code-review-and-quality` 單裝；Claude Code 用 `/plugin marketplace add addyosmani/agent-skills` + `/plugin install agent-skills@addy-agent-skills`；Codex 用 `codex plugin marketplace add addyosmani/agent-skills` + `codex plugin add agent-skills@agent-skills`；每個 skill 是 markdown，跨 agent port 的成本是寫一個 `metadata` 區放 vendor-specific runtime controls。
- **安裝方式：**
  - **npx（最快路徑，所有 agent 通用）：** `npx skills add addyosmani/agent-skills`（裝全部 25 個 skills）或 `npx skills add addyosmani/agent-skills --skill code-review-and-quality`（單裝一個）。Vercel Labs 的 skills CLI 對 70+ agent 自動整合。
  - **Claude Code：** `/plugin marketplace add addyosmani/agent-skills` + `/plugin install agent-skills@addy-agent-skills`；或本地開發 `claude --plugin-dir /path/to/agent-skills`。
  - **Codex（Codex CLI v0.122+）：** `codex plugin marketplace add addyosmani/agent-skills` + `codex plugin add agent-skills@agent-skills`。
  - **Antigravity CLI：** `agy plugin install https://github.com/addyosmani/agent-skills.git` 或本地 clone 後 `agy plugin install ./agent-skills`。
  - **Gemini CLI：** `gemini skills install https://github.com/addyosmani/agent-skills.git --path skills` 或本地 `gemini skills install ./agent-skills/skills/`。
  - **Cursor：** workflow skills 放 `.cursor/skills/` 從 `agent-skills/skills/` sync，short policies 寫 `.cursor/rules/*.mdc`。
  - **OpenCode：** skills copy 到 `.opencode/skills/` 或 `~/.config/opencode/skills/`，加 `.opencode/AGENTS.md`。
  - **Copilot：** `agents/` 下的 agent definitions 當 Copilot personas，skill content 寫進 `.github/copilot-instructions.md`；獨立 `copilot` CLI 走 plugin install。
  - **Command Code：** `cmd skills add addyosmani/agent-skills`（互動選）或 `cmd skills add addyosmani/agent-skills -s spec-driven-development`（單裝）。
  - **Source clone：** `git clone https://github.com/addyosmani/agent-skills.git` 直接用檔案系統。
  - **主人硬體可行性：** 純 markdown skill pack，無 wheel / platform dependency；任何 agent + 任何 OS 都可裝。
- **近期 release：** **0.6.9，published 2026-09-05 03:25 UTC（11 天前）**。0.6.9 是 docs + hardening + contributor-process release，**沒有新 skill**：(1) **Advanced per-agent configuration guide**（#316）——codify `SKILL.md` frontmatter portable + vendor-specific runtime controls（model / tools / turn limits / thinking level）放 `metadata` 或 per-agent adapter 的分離 pattern，讓同一 skill file 跨 Claude Code / Cursor / Gemini / Antigravity 通吃；(2) **Rejected skill-change ledger**（#544）——append-only record 拒絕過的 skill + description 變更連同 eval score，讓同個 idea 不會被新 PR 重提，pre-flight 步驟就檢查 ledger；(3) **security-and-hardening** skill hardening——destructive path operations 強制 symlink resolution + allowlisted root + minimum depth + ownership check（#547）、rate limiting 強制 shared store across instances（#536）；(4) **observability-and-instrumentation** skill hardening——多 entry point 寫同一 log stream 時要 stamp entry point 旁邊 correlation ID（#546）。

### `pacifio/atlas`

- **Repo 摘要：** 「**Source control for coding agents**」——把現有 git commit model 從「一句 model 寫的 commit message」升級到「**完整保留 prompt + tool call + reasoning + 對應 commit 的 checkpoint 紀錄**」；同個 codebase 上同時跑 Claude Code / Codex / Atlas's native agent / ACP registry 上的 Cursor / OpenCode / Kilo Code 等所有 agent，每個 agent 共用同一個 on-device semantic memory（Claude 的決定自動出現在 Codex 下個 prompt 中）。4,722★、Rust、26 MB、MIT、`created` 2026-05-14、`pushed_at` 2026-09-16 00:03 UTC（今天仍在 push）。設計哲學是「**nothing is locked in**」——notes 是 markdown、canvases 是 JSON、sessions 是 JSONL、editor 是 disk 上的 file，close Atlas 在 vim 裡也能接續。唯一進 SQLite 的部分是 checkpoint 記錄（`.atlas/sessions.db`，gitignored），因為要 query 而非 read。**對主人的對位：和 `zylos-ai/zylos-core`、`caura-ai/caura` 同條 agent infra 主軸——但 atlas 是「**以 commit/checkpoint 為中心**」的版本，把「為什麼這個 commit 存在」這條 audit trail 從 commit message 升級到完整 session record；horo-agent 若要對位「enterprise agent needs audit + provenance」這條，這是 canonical reference。**
- **3W1H：**
  - **What：** Tauri 包 Rust core + native CodeMirror editor 的 macOS desktop app（Linux/Windows build from source 但 untested），底層是 Claude Code / Codex ACP subprocess + Atlas 自己的 native agent（Codex engine 的 hard fork）+ on-device HNSW 語意索引 + session capture 寫進 `.atlas/sessions.db`。
  - **Why：** 三個沒人解決的問題——(1) agent session 起點永遠是 zero（昨天的 context 沒了）；(2) 換 agent = thread lost（Claude Code 看不到 Codex history）；(3) context 散在 10 個地方（KB、`CLAUDE.md`、`AGENTS.md`、各 agent 自己的 memory file）。Atlas 用 checkpoint（每個 commit 連回觸發它的 session：prompts + tool calls + patches）和 shared memory（任何 agent 寫、任何 agent 讀）一起解決這三件事。
  - **Who：** 個人 AI 重度使用者（想要 commit ↔ session 雙向追溯、不想 agent 換了 thread 就斷）、需要 audit / provenance 的工程 team（commit 為什麼這樣寫 → 拉出當時的 prompt + tool call + reasoning）、做 multi-agent infra 的 platform engineer、研究 session-compression / context-handoff 的學術團隊。
  - **How：** `brew install --cask pacifio/tap/atlas` 或從 [tryatlas.cc](https://www.tryatlas.cc/) 下載 `.dmg`；首次啟動會在 `.atlas/sessions.db` 建 local store；裝好後現有的 Claude Code / Codex subscription 從 Atlas 內 launch，session 自動 capture、commit 自動 link 到觸發它的 session。
- **安裝方式：**
  - **macOS DMG（主裝路徑）：** 從 [tryatlas.cc](https://www.tryatlas.cc/) 或 GitHub releases 頁抓最新 `.dmg`；拖進 Applications 即可。
  - **Homebrew cask（計劃中）：** README 註記 `#todo homebrew tap so this becomes brew install atlas`，目前還沒實際 tap。
  - **Build from source：** 需要 Bun + Rust stable（via rustup）+ Xcode Command Line Tools；Linux/Windows build from source 但 untested。
  - **未找到明確 pip/npm/crates 安裝方式**——desktop binary 為主 distribution；source build 是 fallback 路徑。
  - **主人硬體可行性：** Apple M4 Max VM 可裝 macOS DMG；但 atlas 鎖定 macOS 為 supported platform，Linux/Windows 路徑從 source build 但 untested。
- **近期 release：** **alpha-0.3.1 — "Atlas comms alpha"，published 2026-09-06 16:37 UTC（10 天前）**。alpha-0.3.1 是 alpha 系列第三個版本，主題是「**comm alpha**」——對應 Atlas 與 agent 之間的 ACP（Agent Client Protocol）通訊層；前一版 `exp-0.3.1` 是同期的 prerelease（2026-09-05），再前一版 `alpha-0.3.0` 是「**Atlas ACP + Timeline**」（2026-08-25）——把 ACP 接好 + 引入 commit graph timeline 視圖。release cadence 是「alpha + exp 雙軌，每 1-2 週一版」，符合 5 個月前從 0 開始的新專案節奏。

### `danny-avila/LibreChat`

- **Repo 摘要：** 「**Enhanced ChatGPT Clone**」——把 ChatGPT Web 的 UI/UX 翻成 open-source + 自帶 agent + MCP + Skills + 17 種 model provider 切換（OpenAI / Anthropic / AWS Bedrock / Google / Vertex AI / DeepSeek / Mistral / Groq / OpenRouter / Ollama / Apple MLX / koboldcpp / together.ai / Perplexity / ShuttleAI / Qwen 等）+ Code Interpreter（Python/Node/Go/C/C++/Java/PHP/Rust/Fortran sandboxed 執行）+ Artifacts（React/HTML/Mermaid 即時生成）+ Web Search + Image Generation（GPT-Image-1 / DALL-E / Stable Diffusion / Flux）+ MCP Servers + Subagents + 自訂 Agent Marketplace。43,953★、TypeScript、298 MB、MIT、`created` 2023-02-12（3.5 年歷史，`pushed_at` 2026-09-16 05:59 UTC，今天仍在 push）。**對主人的對位：這是 ChatGPT clone 賽道裡「最完整、最 agent-aware」的 open-source reference；horo-webui 若要做「agent + multi-model + self-host + MCP-native + WebUI」，LibreChat 是必看但不必 fork 的 reference——重點是他把 `Skills`、`Subagents`、`MCP Server`、`Code Interpreter`、`Image Generation` 這五個 2026 年 agent UI 的 standard component 都包好了；horo-webui 可以用「介面定義 + API contract」的方式對位而不是 vendor 程式碼。**
- **3W1H：**
  - **What：** Node.js + React monorepo；後端 Express/Node 提供 `/api/agents`、`/api/ask`、`/api/mcp`、`/api/skills`、`/api/files` 等 endpoint；前端是 React + Vite + Tailwind 的 ChatGPT-style 介面；one-click deploy 支援 Railway / Zeabur / Sealos。
  - **Why：** ChatGPT 訂閱的 UI/UX 好用但 locked-in；LibreChat 把同樣的介面 open-source 出來、加上 open-source 才有的「**可以接 Ollama / Apple MLX 本機模型**」、「**可以接 self-hosted OpenAI-compatible API**」、「**agent + Skills + MCP 全部 native**」這三層；特別是 Apple MLX（macOS 本機推理）和 Ollama（Linux 本機推理）是 self-host LLM 的兩條主路徑，LibreChat 是少數同時支援的 UI。
  - **Who：** 想 self-host ChatGPT-grade UI 的個人 / 團隊（不想把對話紀錄傳給 OpenAI）、需要 multi-model 切換的 power user（同一個 UI 比較 Claude Opus 5 跟 GPT-6 Astra）、做 AI agent SaaS 的 PM（看 baseline UX 怎麼做）、企業內部想提供 ChatGPT 替代品 + 控管 audit log 的 IT 部門。
  - **How：** `git clone https://github.com/danny-avila/LibreChat.git` → `cp .env.example .env` → `docker compose up -d`（或在 Railway / Zeabur / Sealos one-click deploy）；`librechat.yaml` 設定 AI endpoints 跟 agents；MCP server 在 UI settings → Tools 加。
- **安裝方式：**
  - **Docker（主裝路徑）：** `git clone https://github.com/danny-avila/LibreChat.git` → `cp .env.example .env` → `docker compose up -d`。
  - **One-click deploy：** Railway / Zeabur / Sealos 按鈕直接點（README 有 badge + 連結）；不需要在本機裝 Node。
  - **Node.js（傳統）：** `git clone` → `cd LibreChat && npm install` → `npm run backend` + `npm run frontend`（兩個 dev server 同時跑）。
  - **Configuration：** `librechat.yaml` 寫 AI endpoints / agents / presets；MCP server 在 UI settings 啟用。
  - **主人硬體可行性：** 純 Node.js + Docker，Apple M4 Max VM 可裝；backend memory footprint 較大（multimodal + agents 模組化），M-series 16 GB 可跑但需要 tune DB connection pool。
- **近期 release：** **v0.8.8-rc3，published 2026-09-15 02:34 UTC（昨天，prerelease）**。v0.8.8-rc3 是 release candidate，主要新功能——(1) **Agent Management API (beta)**：CRUD agents + manage agent files + Skills、deployment-bound OIDC authentication 給 machine clients；(2) **Attached workspaces（highly experimental）**：每個 managed / personal code worker 可指定 workspace，agent 能 inspect trees / read files / author changes / 跑 Bash（bounded timeouts）；(3) **Background tool controls**：可取消 ordinary background tools（包括 attached Bash），但 detached Subagent 執行獨立；(4) **Code approval controls**：Ask / Allow / Deny 三種政策 file writes 跟 command execution；(5) **Manual context compaction**：context window 滿之前 summarize-only turn；(6) **Context Usage**：inspect dialogue / retained tool traffic / Agent instructions / cache / cost / runway pressure；(7) **Unified attachments**：上傳一次 routing 到 model 或 extracted text；(8) **GPT-6 Astra** for OpenAI and Agents endpoints with Responses API routing。**release cadence** 是 rc 系列（rc1 / rc2 / rc3），3 個月內從 v0.8.7（2026-06-24）一路 release 出來，是成熟階段的「**每週兩個 rc**」節奏。

### `huggingface/funes`

- **Repo 摘要：** Hugging Face 官方出的「**durable memory for AI coding agents**」——把 Claude Code / Codex / pi / Hermes 等 agent 的過去 session 索引成本機 Lance dataset，recall 時融合 vector + BM25 + reranker + recency reweighting 撈出來；memory 本體是 Hugging Face Hub 上的 dataset，可以 publish / bind / 跨機器分享。420★、Rust、17.8 MB、Apache-2.0、`created` 2026-06-18（3 個月歷史，`pushed_at` 2026-09-09 06:31 UTC，一週前）。Hermes Agent 已是 first-class 支援（`funes add hermes`），README 明確寫「**Index Claude Code, Codex, pi, and Hermes into a single memory; recall spans all of them**」——**對主人 Hermes 主軸極度對位**。**和 `MemTensor/MemOS` 同樣是 agent memory 賽道但分工不同：funes 是「**把現有 agent 的 session 索引成 memory dataset**」（被動抓 trace），MemOS 是「**為 agent 設計 memory OS**」（主動管理 L1/L2/L3 層級）；funes 適合「不想改 agent 行為、只想 recall 過去」、MemOS 適合「agent 一開始就以 memory 為中心設計」。**
- **3W1H：**
  - **What：** Rust 寫的 CLI（`funes`）+ MCP server；Lance dataset 當 local store；prebuilt binary 透過 Hugging Face bucket 分發（Linux x86_64 / Linux aarch64 / macOS Apple Silicon）。
  - **Why：** AI coding agent 真正 production 化的瓶頸是「context 跨 session 流失」——今天 Claude Code 解了一個 bug、明天開新 session 還要重新解；現有解法（in-session memory file、`CLAUDE.md`、`AGENTS.md`）都是 agent 自己寫、沒人搜。funes 把所有 agent 的 session 變成可搜的 memory dataset，**「過去為什麼這樣做」**這條 audit trail 直接可 recall。
  - **Who：** AI coding agent 重度使用者（想要「上次為什麼選 streaming parser」這類 recall）、需要把 agent 經驗分享給 teammate 的工程 team（memory 是 HF Hub dataset，可 publish / bind）、做 agent eval 的人（memory 是 eval material 來源）、Hermes Agent 使用者（funes 已 first-class 支援）。
  - **How：** `curl -fsSL https://huggingface.co/buckets/huggingface/funes/resolve/install.sh | sh` 預設裝進 `~/.local/bin/`；`funes add claude`（或 `codex` / `pi` / `hermes`）一鍵 onboard；`funes status` 看 recall 是否讀到 own memory；`funes recall "..." --memory <user|org>/funes-memory` 讀別人 publish 的 memory；`funes ask claude "..." --memory huggingface/funes-memory` borrow 一個 agent 答 grounded question。
- **安裝方式：**
  - **curl|bash installer（主裝路徑）：** `curl -fsSL https://huggingface.co/buckets/huggingface/funes/resolve/install.sh | sh`，自動偵測 platform、下載對應 prebuilt binary、verify SHA256 checksum 跟 tagged release version、放到 `~/.local/bin/funes`。
  - **Prebuilt binary 直接抓：** Hugging Face bucket 有 Linux x86_64 / Linux aarch64 / macOS Apple Silicon 三個 binary + `SHA256SUMS` manifest。
  - **升級：** 已裝 `funes update` 一鍵升到最新版；`funes status` 會提示有新版；`funes update --force` 重裝當前版本。
  - **Build from source：** Rust toolchain + `protoc`（lance build script 需要 protobuf compiler，但完成後的 binary 不需要）。
  - **Onboard 到 agent：** `funes add claude` / `funes add codex` / `funes add pi` / `funes add hermes` —— 一鍵 onboard；agent 拿到 `recall` + `get` 兩個 tool、funes 自動 build 第一個 index、安裝 hook 每個 turn 增量索引、bind memory 後每個 session boundary 自動 publish。
  - **主人硬體可行性：** Apple M4 Max VM 直接走 prebuilt macOS Apple Silicon binary；`~/.local/bin` 加 PATH 即可。Hermes Agent 已支援（funes README 列為 first-class）。
- **近期 release：** **v1.3.0，published 2026-09-01 16:05 UTC（15 天前）**。v1.3.0 是 feature + fix 混和版：features 包括——(1) `funes status` report 改善；(2) `funes add` 對 remote memory setup 做 hardening；(3) `funes remove` 指令（#107）；(4) `funes status` 提示 scrub required（#118）；(5) session listing 跟 scanning 能力（#122）；(6) 移除 curation（#123）；(7) `funes sketch` 對 session 做無 query 的 digest（#124）；(8) verb descriptions cleanup（#125）；(9) Codex integration 改善（#126）；(10) Pi integration 改善（#128）。bug fixes 包括 inline base64 data URI payloads elision（#111）、Claude plugin uninstall 修 SSH scope（#112/114）、push → scrub → push 收斂（#113）、Codex tests assertions 更新（#129）、empty host onboarding 改善（#130）、push 單發 per remote（#131）。**release cadence** 是 3 週一版（v1.2.0 → v1.3.0），符合「prebuilt binary + HF bucket」分發的 Rust CLI 工具正常節奏。

### `MemTensor/MemOS`

- **Repo 摘要：** 「**Self-evolving memory OS for LLM & AI agents**」——把「memory」從「RAG + vector store」的單層儲存升級到完整的 OS（**L1 traces / L2 policies / L3 world models / crystallized Skills**四層、hybrid retrieval FTS5 + vector + RRF reranking、multi-modal 支援 text + image + tool traces + personas、MemScheduler 異步 ingestion、Memory Feedback 自然語言 refine）。11,363★、TypeScript、62.6 MB、Apache-2.0、`created` 2025-07-06（1.25 年歷史，`pushed_at` 2026-09-09 12:54 UTC，一週前）。**Hermes Agent 已是 first-class 整合（2026-04-10「MemOS Hermes Agent Local Plugin」官方 plugin 上線，hybrid retrieval FTS5 + vector、smart dedup、tiered skill evolution、multi-agent collaboration、100% local、zero cloud dependency）**——**對主人 Hermes 主軸極度對位**。**benchmark 表現**：LoCoMo 88.83、LongMemEval 89.20、PersonaMem v2 40.58、OmniMemEval 14 個 commercial memory product 中 lead、OpenClaw 整合後 agent task 從 36.63% → 50.87%（+14.24pp）。**和 `huggingface/funes` 對比**：MemOS 是「**memory 是 OS 的核心**」（agent 一開始就 memory-aware）、funes 是「**memory 是外部 index**」（不動 agent 行為）。
- **3W1H：**
  - **What：** TypeScript + Python 多語言 monorepo；Neo4j（graph DB）+ Qdrant（vector DB）+ Redis（optional cache）+ MemOS API（FastAPI）+ Cloud API（memos.memtensor.cn）+ MemOS Cloud Plugin + Local Plugin（npm package）。
  - **Why：** 2026 年 agent memory 賽道的 3 條真實痛點——(1) **單層 vector store 不夠**：production agent 需要 L1 對話 trace + L2 scenarios + L3 persona/world model 多層次抽象；(2) **black-box embedding store 不行**：memory 必須 inspectable 跟 editable，graph 結構讓使用者能 trace；(3) **cross-agent sharing 不存在**：不同 agent 各自記 memory，沒有共享層。MemOS 把這三條都解了。
  - **Who：** LLM app 想加 memory 的開發者（API key + REST 一行起）、需要 self-host memory infra 的企業（docker compose up Neo4j + Qdrant + MemOS）、OpenClaw / Hermes / DeepSeek Harness 用戶（plugin 形式 onboard）、研究 agent memory 的人（benchmark lead 是 paper material）。
  - **How：** 四個 entry point——(a) **Cloud API**（`mpg-...` API key、memos.memtensor.cn）；(b) **Self-Host**（`git clone` + `cp docker/.env.example .env` + `docker compose up`，起 Neo4j + Qdrant + MemOS）；(c) **Cloud Plugin for OpenClaw**（`openclaw plugins install`）；(d) **Local Plugin**（`npm install + agent-specific setup`）。
- **安裝方式：**
  - **Docker Compose（self-host 主裝路徑）：** `git clone https://github.com/MemTensor/MemOS.git` → `cd MemOS` → `cp docker/.env.example .env`（填 API keys）→ `cd docker && docker compose up`，起 Neo4j + Qdrant + MemOS API on `http://localhost:8000`。
  - **Uvicorn 直接跑（不用 Docker）：** `git clone` → `cp docker/.env.example .env` → 確保 Neo4j 跟 Qdrant 在跑 → `cd src && uvicorn memos.api.server_api:app --host 0.0.0.0 --port 8000 --workers 1`。
  - **Cloud API（最快路徑）：** 從 [MemOS dashboard](https://memos-dashboard.openmem.net/cn/quickstart/?source=landing) 拿 API key（`mpg-...` 開頭）→ Python 一行 `requests.post("https://memos.memtensor.cn/api/openmem/v1/add/message", headers={"Authorization": f"Token {API_KEY}"}, json={...})`。
  - **Local Plugin for Hermes / OpenClaw：** `npm install + agent-specific setup`，100% 本機 SQLite、hybrid FTS5 + vector search、task summarization + skill evolution、Memory Viewer dashboard。
  - **Cloud Plugin for OpenClaw：** `openclaw plugins install`，走 MemOS Cloud 後端、72% lower token usage、multi-agent memory sharing。
  - **主人硬體可行性：** Cloud API 路徑最輕（無 infra 維護）；Local Plugin 走 SQLite + 本機 embedding，Apple M4 Max VM 可跑；Self-Host Docker Compose 需要 Neo4j + Qdrant，記憶體壓力較大。**Hermes Agent 已有官方 local plugin 對位主人 Horobot stack。**
- **近期 release：** **v2.0.33 — "Release v2.0.33"，published 2026-09-03 11:29 UTC（13 天前）**。v2.0.33 是 patch + chore 混和版——(1) **ci**：local plugin release notes 對大型 release 變 resilient（#2311）；(2) **fix**：normalize numeric local-plugin source refs（#2312）；(3) **Preference memory fixes**（#2337）；(4) **chore**：version 改 dev-v2.0.33（#2341）；(5) **fix**：改善 SkillMemory 的 extraction link（#2342）。**v2.0.0 → v2.0.33 是 09 月的「2.0 重新對齊 SemVer」計劃的產物**——2.0 把過去 1.x 累積的 compatibility-breaking changes bundle 成單一 major，從此 SemVer 紀律；2026-08-17 還發布了 **MemOS 連 DeepSeek Harness（dsh）** 的 plugin。**release cadence** 是 high-frequency patch（每 1-2 天一版），符合 11k★ 成熟專案的運作節奏。

---

## 重點觀察

- **Agent 主軸佔 5/5：今天 5 個 repo 100% 與 AI agent 直接相關。** 5 個 pick 的 domain 分布——(1) **`addyosmani/agent-skills`** = agent skill pack（25 個 production-grade SKILL.md workflow）；(2) **`pacifio/atlas`** = agent-aware source control（commit ↔ session checkpoint）；(3) **`danny-avila/LibreChat`** = agent-capable chat UI（Agent + Skills + Subagents + MCP 全 native）；(4) **`huggingface/funes`** = agent memory dataset（session indexing + recall + HF Hub publish）；(5) **`MemTensor/MemOS`** = agent memory OS（L1/L2/L3 + hybrid retrieval + skill evolution）。**這個分布跟昨日 5 個 repo 跨 5 個 vertical 不同——昨天是「review、voice、simulation、runtime、memory」5 個不相關的 vertical，今天是「**agent 主軸的 5 個層次**」**——skill pack / source control / chat UI / memory dataset / memory OS——這個收斂訊號強烈：「agent 不再是附屬功能而是整個 software lifecycle 的中心」。**對主人 enterprise-lite + air-gapped downstream 對位：horo-agent / horo-webui 在 agent 主軸已經沒有「哪個 layer 沒人做」的 niche——每層都有 canonical open-source reference；差異化要靠「**整合多層 + air-gapped 自架 + 用主人獨特的 ecosystem 經驗**」而不是「做某個沒人做的 layer」**。

- **Hermes 主軸今天 2/5 強對位：`huggingface/funes` 把 Hermes 列為 first-class 支援（`funes add hermes`）+ `MemTensor/MemOS` 有官方 Hermes Agent Local Plugin（2026-04-10 上線）**。這是過去 30 天第一次「同一個 14:00 pick 出現兩個都把 Hermes 當 first-class」的巧合——funes 的 README 明確列出 topics: `agent-memory, ai-agents, claude-code, codex, hermes, lance, llm-agents, mcp, pi, rust`；MemOS 的 topics: `agent, agentic-ai, ai, ai-agents, chatgpt, claude, deepseek-harness, dsh-plugin, hermes, llm, long-term-memory, mcp, memory, memory-management, openclaw, rag, self-evolving, skills, token-savings`。**這意味著「Hermes Agent」已經從主人自己單獨使用的工具升級成「**open-source ecosystem 的 integration target**」**——任何想做 agent infra 的新專案（funes 3 個月前才 release、MemOS 1.25 年歷史）都把 Hermes 當 first-class，這對主人 Horobot stack 是強烈背書訊號。

- **Agent memory 賽道出現「funes 被動 index vs MemOS 主動 OS」分工：** 兩個 repo 都解決「agent 跨 session 失憶」的問題，但策略完全相反——funes 是「**不動 agent 行為、只把 agent session 索引成本機 Lance dataset**」（被動抓 trace、HF Hub publish、跨 agent 共享 memory）；MemOS 是「**memory 是 OS 的核心、agent 一開始就 memory-aware**」（L1/L2/L3 多層次、MemScheduler 異步 ingestion、Memory Feedback、Skill evolution）。funes 適合「**不想改 agent 行為**」、MemOS 適合「**memory 是 first-class**」。**這個分工剛好跟昨天主人 enterprise-lite 主軸對位——horo-agent 若要快速加 memory layer，funes 是 lower-friction path（curl|bash + funes add hermes 一鍵）；若要把 memory 當 core architecture 重寫，MemOS 是 reference**。

- **5 個 repo 全 permissive license：MIT × 3（`addyosmani/agent-skills` + `pacifio/atlas` + `danny-avila/LibreChat`）+ Apache-2.0 × 2（`huggingface/funes` + `MemTensor/MemOS`），0 個 AGPL/GPL/LGPL**。這是 2026-09 上半月 14:00 picks 中第一次 5/5 全 permissive license 的單日組合——前 7 天至少 2/5 是 AGPL-3.0 或 copyleft 變體。**對主人 enterprise-lite + air-gapped downstream：今天 5 個 pick 全部可商用 fork、不需保留 source、不需公開修改**——這是「**agent infra 主軸的 canonical reference 都用 permissive license**」的訊號，跟「desktop / simulation / TTS」那幾條 vertical 偶爾出現 AGPL-3.0 形成對比。horo-agent / horo-webui 若要在 agent 主軸 reference 任何上游，今天 5 個 pick 都可以直接 fork + 改 + ship。

- **Install path 多樣性延續：今天 5 個 install path 跨 5 種 distribution channel**——(1) **`addyosmani/agent-skills`** 是 **npx skills CLI（Vercel Labs skills registry）+ 11 個 agent 各自的 marketplace/plugin 整合**——純 markdown + npm registry，零 platform dependency；(2) **`pacifio/atlas`** 是 **macOS DMG + Homebrew cask（計劃中）+ source build with Bun/Rust/Xcode CLT**——desktop binary；(3) **`danny-avila/LibreChat`** 是 **Docker Compose + Railway/Zeabur/Sealos one-click deploy + Node.js dev server**——三層 deployment；(4) **`huggingface/funes`** 是 **curl|bash from HF bucket + prebuilt binary + Rust source build**——HF bucket 是少見的分發管道；(5) **`MemTensor/MemOS`** 是 **Docker Compose（Neo4j + Qdrant + MemOS）+ uvicorn + Cloud API + Local Plugin (npm)**——四種 deployment 同時存在。**「install path 5/5 不重複」這個 milestone 從 08-21 持續到今天（連續 8+ 個工作天）**——每個新 open-source 專案都會挑一個「**最適合自己 distribution 模型**」的 install path，而不是 fallback 到 npm/pip，這是 2026 下半年 open-source 工具鏈的成熟特徵。
