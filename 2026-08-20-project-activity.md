# GitHub 專案動態
- 檢查時間：2026-08-20
- 檢查對象：MoneyPrinterTurbo / OpenViking / Munder Difflin / obra-superpowers / Anthropic-Cybersecurity-Skills

## 動態摘要

### harry0703/MoneyPrinterTurbo
- 類型：release
- 內容：最新 release `v1.3.4`（2026-08-12）；repo `pushed_at` 2026-08-20 = 今日還在動；trending top 1，2,221 stars today。111.5k★ 累計。
- 連結：https://github.com/harry0703/MoneyPrinterTurbo/releases/tag/v1.3.4

### volcengine/OpenViking
- 類型：release
- 內容：最新 release `v0.4.15`（2026-08-18，2 天前）；pip `openviking` 同日發佈。AGPL-3.0，30k★，Python。
- 連結：https://github.com/volcengine/OpenViking/releases/tag/v0.4.15

### chaitanyagiri/munder-difflin
- 類型：release
- 內容：最新 release `v0.4.4`（2026-08-18，2 天前）；TypeScript Electron 桌面 app，包 9 個 CLI agent 成一隊；MIT。
- 連結：https://github.com/chaitanyagiri/munder-difflin/releases/tag/v0.4.4

### obra/superpowers
- 類型：release
- 內容：最新 release `v6.3.0`（2026-08-12），**新增 Hermes Agent plugin install 指令 `hermes plugins install obra/superpowers --enable`**。274k★，MIT，Shell。
- 連結：https://github.com/obra/superpowers/releases/tag/v6.3.0

### mukul975/Anthropic-Cybersecurity-Skills
- 類型：trending / repo activity（release 較舊）
- 內容：trending top 4，766 stars today；30k★，Apache-2.0。最後 release `v1.3.0`（2026-06-22），但 README 內含明確 `Hermes_Agent-compatible` badge，並覆蓋 MITRE ATT&CK / NIST CSF 2.0 / MITRE ATLAS / D3FEND / NIST AI RMF / MITRE F3 六大 framework。
- 連結：https://github.com/mukul975/Anthropic-Cybersecurity-Skills

## 重點觀察
- **3 天連發 Hermes-as-official-agent-host MEMORY peak** — CLI-Anything（08-16）+ `unsloth start hermes`（08-17）+ 今天的 `obra/superpowers` 主動把 Hermes 列為官方支援 agent host 與 `mukul975/Anthropic-Cybersecurity-Skills` 徽章列出 Hermes-compatible，是本週第 3 個 consecutive MEMORY match。
- **release 新鮮度 4/5 = 80%** — 5 個 repo 裡只有 Cybersecurity-Skills 沒 7 日內 release，但 trending 流量與 README 更新撐起來；其餘 4 都 fresh release (≤8 天)；trend 顯示 80% peak 是 AI 開源生態重視 release versioning 的訊號（與上月多 60% baseline 不同）。
- **3 種 install 範式** — MoneyPrinterTurbo（`uv sync --frozen` + `sh webui.sh` Type-6 變體，Streamlit WebUI）；OpenViking（`pip install openviking` + CLI server，Type-1 + server）；Munder Difflin（`git clone` + `npm install` + `npm run dev` Electron dev，Type-13 變體）。
- **語言生態 Python 5/5 主流** — 5 個 repo 裡 4 個 Python 主導 + 1 TypeScript（munder-difflin 但內含多語言 multi-CLI 整合）+ 1 Shell（superpowers scaffold）；LLM-native AI tools 持續 Python-化。
- **license 干擾低** — 4/5 是 permissive (MIT × 2 + Apache-2.0 × 1)，唯一 AGPL-3.0 是 OpenViking 但官方明示「open-source edition is not crippled」+ no feature gate + 提供 enterprise hosted SaaS 雙軌制，跟主人 horo-agent / horo-webui air-gapped 整合時 case-E 警告要明示。

## Repo 摘要與 3W1H

### `harry0703/MoneyPrinterTurbo`
- **Repo 摘要：** 一站式 AI 短影音生成工具 — 主題／關鍵詞進、HD 短影音出，內建大模型撰稿 + 素材搜尋 + 字幕 + 配樂 + 合成全鏈；WebUI（Streamlit）+ API + CLI 三種介面；2026-08-12 發 v1.3.4，累計 111.5k★，由 Kimi（火山引擎）贊助。適合自媒體 / 行銷短帶內容短影音量產。
- **3W1H：**
  - **What：** AI 短影音 pipeline — 從文案撰寫 → 素材匹配 → 字幕 / 配樂 → 1080p 渲染全流程整合的桌面 + Docker + CLI 多模 webapp，採用 uv 管理依賴。
  - **Why：** 解決「想做短影音但寫文案、剪輯、配音都卡關」痛點；近期受關注因 Kimi K3（火山引擎，3T 級開源模型）驅動腳本 + 素材匹配精度暴增，+2,221 stars/day 直接衝進 trending top 1。
  - **Who：** 自媒體創作者 / 行銷團隊 / 想量產短影音但沒剪輯底子的 PM；給 AI agent 用的 SKILL section 也在 README，可被 coding agent 直接呼叫。
  - **How：** `git clone` → `uv sync --frozen` → `sh webui.sh`（macOS / Linux）或 `webui.bat`（Windows）；Docker 用 `docker compose -f docker-compose.release.yml up`；CLI 用 `uv run python cli.py --video-subject "..."`；設定自動從 `config.example.toml` 複製成 `config.toml`。
- **安裝方式：**
  - **uv**：`git clone https://github.com/harry0703/MoneyPrinterTurbo.git` → `uv sync --frozen`（`pyproject.toml` 是主依賴，`uv.lock` 鎖版本）
  - **Docker**：`docker compose -f docker-compose.release.yml up`（拉 `ghcr.io/harry0703/moneyprinterturbo:latest`，無需本機 Python）
  - **WebUI 啟動**：`sh webui.sh`（macOS / Linux，自動偵測 `.venv` / `uv`，預設綁 `127.0.0.1:8501`；要區網可達用 `MPT_WEBUI_HOST=0.0.0.0 sh webui.sh`）
  - **API server**：`uv run python main.py`
  - **CLI 純命令列**：`uv run python cli.py --video-subject "..."`（不需要瀏覽器、適合無頭環境）
- **近期 release：** `v1.3.4` — 2026-08-12 發佈（8 天內），pre-release = false，release 標題與 tag 同名；變更細節需看 GitHub release notes。Repo `pushed_at` 2026-08-20 今日仍活，主線持續迭代。

### `volcengine/OpenViking`
- **Repo 摘要：** 「AI Agents 的 Context Database」— 統一記憶體 / 知識 RAG / Skills 三類 context 進單一虛擬檔案系統（`viking://` protocol），每筆 entry 自動三層（L0 abstract / L1 overview / L2 details）按需載入；release `v0.4.15`（2026-08-18），由火山引擎出品，AGPL-3.0 但官方明示「open-source edition is not crippled」。適合多 agent / 多 session 場景要做 unified context 的開發者。
- **3W1H：**
  - **What：** 開源 context database — 提供 Python pip 套件 `openviking` + server `openviking-server` + client CLI `ov`，支援 `viking://` URI 瀏覽、tiered retrieval、directory recursive browse、session-long-term memory extraction。
  - **Why：** 解決「每個 agent / 每個框架有自己的記憶體 schema、RAG、skill 庫，要整合超痛」的 2026 痛點；用「filesystem 為 source of truth」抽象把 memory + RAG + skills 一鍋燴；timeline 「先 retrieve directory 再 dive down layer」可觀察、可重播。
  - **Who：** AI agent 開發者 / RAG 工程師 / 想用同一個 context layer 餵 LangGraph / Claude Code / 自家 framework 的平台團隊；企業場景有官方 hosted SaaS。
  - **How：** `pip install openviking --upgrade` → `openviking-server init`（互動 wizard 選 provider + 寫 `~/.openviking/ov.conf`；支援 Volcengine / OpenAI / Codex OAuth / Kimi / GLM / Ollama 本地） → `openviking-server doctor`（驗 config + provider 連通 + 磁碟） → `openviking-server`（啟動）；client 用 `ov add-resource <url>` / `ov ls viking://resources/` / `ov find "<query>"` / `ov grep "<pat>"`。
- **安裝方式：**
  - **pip**：`pip install openviking --upgrade`（Python 3.10+，含 `ov` client CLI + server CLI）
  - **Server init**：`openviking-server init`（互動式 provider 設定；可重跑）
  - **Server start**：`openviking-server`（前景）或 `nohup openviking-server > openviking.log 2>&1 &`（背景）
  - **可選 extras**：`pip install "openviking[bot]"`（bot / agent 整合）；客戶端 CLI 另有 npm / cargo standalone 版本
  - **Docker**：見 docs.guides.03-deployment
- **近期 release：** `v0.4.15` — 2026-08-18（2 天內）剛發，pre-release = false；tag 與 release name 同名。Repo `pushed_at` 2026-08-20 今日仍動，30k★、804 stars today。license = AGPL-3.0（case-E），主人若要做 horo-agent / horo-webui air-gapped 嵌入，這是「OSI 開源但 strong copyleft + network clause」警告。

### `chaitanyagiri/munder-difflin`
- **Repo 摘要：** 「Agent 桌機版 office」— Electron + Pixi.js + xterm.js + node-pty，把你機器上既有的 9 個 terminal-agent CLI（Claude Code / Antigravity / Codex / Grok / Kimi / Qwen / OpenCode / pi / Copilot）整合到一張 2D office floor 的 avatar，每個 agent 有自己的 mailbox + long-term memory + desk，「你的分身 Michael」當工頭路由工作。release `v0.4.4`（2026-08-18），prototype stage，MIT。適合想同時跑多個 coding agent 又要看見它們在幹嘛的 heavy user。
- **3W1H：**
  - **What：** 桌面 multi-agent harness（Electron app） — 用 Pixi.js 開一張 2D 辦公室，每個 agent 是座上的 avatar，所有 agent 跑在你本機已訂閱的 CLI 帳號的 hourly limit 內，自己訂的 rate limit，不燒 API key。
  - **Why：** 解決「同時開 N 個 coding agent 但分屬不同終端 / 不同 rate limit，看不見彼此在做啥、訊息沒互通、memory 不共享」的痛點；focus 是「fastest memory layer in the world」讓每個 agent 都有 persistent memory 且 recall instant。
  - **Who：** Power user / 個人 knowledge worker 想 run 一個小型 AI 團隊、coding agent 重度依賴者、想要可觀察 agent swarm 的人；不是企業版（prototype + ~2.8k★）。
  - **How：** `git clone https://github.com/chaitanyagiri/munder-difflin.git` → `cd munder-difflin` → `npm install`（postinstall 自動 rebuild node-pty for Electron ABI） → `npm run dev`（dev + HMR）；首次啟動走 onboarding wizard；`npm run build` 生產打包；`npm run typecheck` 驗 main + preload + renderer 三組。
- **安裝方式：**
  - **npm**：`git clone https://github.com/chaitanyagiri/munder-difflin.git` → `cd munder-difflin` → `npm install`（自動 rebuild `node-pty` for Electron ABI） → `npm run dev`
  - **其他 scripts**：`npm run build`（production build via electron-vite）/ `npm run preview`（preview production build）/ `npm run typecheck`（main + preload + renderer 三組）
  - **Prereq**：macOS / Windows / Linux、Node.js 18+、C/C++ toolchain（macOS 需 `xcode-select --install`）；至少一個支援 CLI agent 在 PATH（`claude` / `agy` / `codex` / `grok` / `kimi` / `qwen` / `opencode` / `crush` / `pi` / `copilot`）— 缺哪個會自動跑 installer
  - **可選**：Ollama / LM Studio / vLLM API key 在 Settings → AI Engines；semantic memory index（無 index 仍可用 markdown memory）
  - **常見 gotcha**：`node-pty` 在 Electron 升級後失敗就重跑 `npm install`，postinstall 會 `electron-rebuild` 對齊當前 ABI
- **近期 release：** `v0.4.4` — 2026-08-18（2 天內）剛發，pre-release = false；tag / release / version badge 全數都是 `0.4.4`，status badge 明示「working prototype」。Repo `pushed_at` 2026-08-19，28k★ 累計，795 stars today。license = NOASSERTION（badges 寫 MIT，LICENSE 檔案存在）。

### `obra/superpowers`
- **Repo 摘要：** Coding agent 的「完整 software development methodology」+ 可組合 skills library — 啟動 agent 後先帶你 brainstorm 拿 spec、寫實作 plan、red/green TDD、subagent-driven-development 自動跑。**v6.3.0（2026-08-12）新支援 Hermes Agent plugin install**，13+ agent host 全清單包含 Claude Code / Codex / Cursor / Devin / Gemini CLI / OpenCode / Pi / **Hermes Agent**。274k★、MIT、Shell scaffold。適合「做 agent-native SDLC」或「統一多 agent 開發習慣」的所有人。
- **3W1H：**
  - **What：** Agent-agnostic skills + methodology framework — 一個 repo 裝到哪個 host 就給哪個 host 帶來同樣的 brainstorming → spec → plan → TDD → subagent-driven-development workflow；skills 是 composable（每段 skill 可單獨 trigger）。
  - **Why：** 解決「每個 coding agent 行為不一致、不會先確認 spec 就衝去寫 code、繞過測試、scope creep」的一致性痛點；把 2025-10 起的 fsck / obra 系列作法產品化；主人 MEMORY 直接命中（3rd consecutive Hermes host tick）。
  - **Who：** 用 Claude Code / Codex / Cursor / Devin / Hermes Agent / Pi / OpenCode / Gemini CLI 等 CLI agent 的 developer；SDLC pipeline 想標準化的團隊。
  - **How：** 各 host 對應一個 install 指令，全部寫在 README；Claude Code 用 [官方 plugin marketplace](https://claude.com/plugins/superpowers)；Hermes Agent 用 **`hermes plugins install obra/superpowers --enable`**（主人 MEMORY 直接對應）；其他 Pi 用 `pi install`；升級跟著官方 release tag。
- **安裝方式：**
  - **Hermes Agent**（主人 MEMORY 直接命中）：`hermes plugins install obra/superpowers --enable`，重啟 active sessions；README 註：Hermes 無 post-compaction hook，極長 session 把 first-turn bootstrap 壓掉會失靈，需開新 session
  - **Claude Code**：透過 [官方 Claude plugin marketplace](https://claude.com/plugins/superpowers)
  - **Cursor / Devin CLI / Codex CLI / Codex App / GitHub Copilot CLI / Factory Droid / Gemini CLI / Grok Build CLI / Kimi Code / OpenCode / Pi / Antigravity**：各 host 一節單獨指令，README 目錄列齊
  - **共通**：每一 host 都要單獨裝（無法跨 host 共用）；updating 走 superpowers 的 update flow；無論裝在哪都共用同一份 skills 庫與方法論
- **近期 release：** `v6.3.0` — 2026-08-12（8 天內）剛發，pre-release = false；新增 Devin CLI + **Hermes Agent** 支援、brainstorming 三路徑路由、274k★ 累計 +557 stars today。release notes 連結：https://primeradiant.com/superpowers/。license = MIT（純 permissive），主人 horo-agent / horo-webui downstream 可安心引用。

### `mukul975/Anthropic-Cybersecurity-Skills`
- **Repo 摘要：** 「Maximum-coverage cybersecurity skills library for AI agents」— 817 個結構化 cybersecurity skills，對應 MITRE ATT&CK v19.1 / NIST CSF 2.0 / MITRE ATLAS / D3FEND / NIST AI RMF / MITRE F3 v1.1 六大 framework，涵蓋 29 個資安領域。**README 內含明確 `Hermes_Agent-compatible` badge**。Apache-2.0、community project（非 Anthropic 官方）、30k★、766 stars today。適合 SOC analyst / 滲透測試 / Digital Forensics / Threat Intel / Cloud Security / 紅隊 / 金融詐欺偵測的 AI agent 化。
- **3W1H：**
  - **What：** 結構化 cybersecurity skills library（agentskills.io 標準相容）— 每一個 skill 是 markdown ＋ frontmatter（含 framework mapping）+ 範例 workflow，讓任意 AI agent 透過 npx 或 `git clone` 立馬拿到「資深分析師等級的指引」。
  - **Why：** 解決「AI agent 做 security 工作時缺資深分析師本能 — 不知該用 Volatility3 哪個 plugin、哪些 Sigma rule 抓 Kerberoasting、怎麼 scope 多雲 breach」的痛點；自稱同類 max-coverage 開源 lib，817 skills 對應 6 framework mapping（ATT&CK 805 / CSF 804 / D3FEND 139 / AI RMF 97 / F3 94 / ATLAS 93）。
  - **Who：** 企業 SOC / MSSP / Red team / 滲透測試公司 / 想做 AI security 工具的研究者；不適合未授權測試 — README 開宗明義警告「Authorized & lawful use only」。
  - **How：** `npx skills add mukul975/Anthropic-Cybersecurity-Skills`（推薦）或 `git clone` 後把目錄指向 agent；支援 Claude Code / GitHub Copilot / Codex CLI / Cursor / Gemini CLI 等 26+ agentskills.io 相容平台（含 Hermes）。
- **安裝方式：**
  - **npx（推薦）**：`npx skills add mukul975/Anthropic-Cybersecurity-Skills` — 自動註冊到當前 agent host
  - **Git clone**：`git clone https://github.com/mukul975/Anthropic-Cybersecurity-Skills.git` → `cd Anthropic-Cybersecurity-Skills` — 把目錄塞給 agent host 即可
  - **支援平台**：26+ agent hosts（含 Claude Code、GitHub Copilot、OpenAI Codex CLI、Cursor、Gemini CLI、**Hermes Agent**），遵循 [agentskills.io](https://agentskills.io) 開放標準
- **近期 release：** **未找到 GitHub release in last 7 days** — 最新 release 為 `v1.3.0`（2026-06-22，距今近 2 月）。Repo `pushed_at` 2026-08-08（12 天內仍動）；trending 766 stars today 撐起熱度。README 與 docs 仍在持續更新（含 MITRE F3 v1.1、2026-04 新 framework 與 GARS-2026 survey）。License = Apache-2.0（case-A2：含 patent grant clause，對 horo-agent / horo-webui downstream 是 strict subset of MIT 風險 + bonus patent 保護）。
