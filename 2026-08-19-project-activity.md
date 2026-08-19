# GitHub 專案動態
- 檢查時間：2026-08-19
- 檢查對象：MoneyPrinterTurbo、munder-difflin、ai-memory、last30days-skill、pipeshub-ai 等 5 個 trending / 自由探索挑選的專案

## Repo 摘要與 3W1H

### harry0703/MoneyPrinterTurbo
- **Repo 摘要：** 一站式 AI 短影片自動生成工具，從主題關鍵字出發，由 LLM 撰寫腳本、抓素材、生成字幕與背景音樂，自動合成直 / 橫式高清短影片並提供 WebUI + API。108k★ + 16.5k forks 已長期霸榜 Trending，今日因短影片創作熱潮再次回到 daily #1。對想自動化量產抖音 / Reels / TikTok / YouTube Shorts 內容的創作者與小團隊最實用；對 AI 工程師社群則是「LLM + FFmpeg + TTS pipeline」的完整參考實作。
- **3W1H：**
  - **What：** Python 寫的 AI 短影片自動生成 pipeline（WebUI + REST API）。
  - **Why：** 把「給主題 → 拿到一支可上傳的短片」整段自動化，省下腳本、素材匹配、字幕合成、剪輯的人力；目前 README 與 release notes 明示與 Moonshot Kimi K3 深度整合（生成腳本 + 素材關鍵字 + 畫面決策）。
  - **Who：** 短影片創作者、行銷 / 自媒體團隊、想研究 LLM × 影音自動化的工程師。
  - **How：** `git clone` → Docker Compose 跑預構建映像（推薦）；或 `uv` 建 Python 3.11+ 環境 + `pip install -r requirements.txt`；Windows 用戶可下載 GitHub Releases 的一鍵啟動包；首次啟動 WebUI 自動生成 `config.toml` 設定 LLM Provider / 素材來源 / API Key。
- **安裝方式：**
  - **Docker（推薦）：** `git clone https://github.com/harry0703/MoneyPrinterTurbo.git` → `cd MoneyPrinterTurbo` → `docker compose -f docker-compose.release.yml up`（拉 `ghcr.io/harry0703/moneyprinterturbo:latest`，WebUI 開 `http://127.0.0.1:8501`、API 文件 `http://127.0.0.1:8080/docs`）
  - **pip/uv（手動部署）：** Python 3.11+ → `uv venv` → `uv pip install -r requirements.txt` → 啟動 WebUI / API
  - **Windows 一鍵啟動包：** 從 [GitHub Releases](https://github.com/harry0703/MoneyPrinterTurbo/releases/latest) 下載 → 解壓後雙擊 `update.bat` 再雙擊 `start.bat`（路徑不可含中文 / 特殊字元 / 空格）
  - **Google Colab：** README 提供 `MoneyPrinterTurbo.ipynb` 免設定體驗
- **近期 release：**
  - **v1.3.4** — 發布日期：**2026-08-12**（published_at: `2026-08-12T02:51:41Z`）
  - 重點：WebUI 新增「影片生成後是否自動開啟資料夾」偏好；下載檔名改用影片主題命名 + 多影片任務自動編號；Whisper `initial_prompt` 可設定以提升字幕品質。

### chaitanyagiri/munder-difflin
- **Repo 摘要：** 本機多 agent 協作 harness，把 Claude Code / Antigravity (Gemini) / OpenAI Codex / xAI Grok / Kimi / Qwen / OpenCode / Crush / pi.dev / GitHub Copilot CLI 等十餘款 coding agent 全部包成「辦公室」 — 由使用者的 clone (Michael) 統一協調、訊息路由、跨 session 記憶，UI 採 Electron 桌面 app 視覺化為 avatar 群。2.2k★ 仍在 prototype 階段但 0.4.x 系列已連續每日迭代，README 明確標註 MIT 但 GitHub API 顯示 `NOASSERTION`（case-G，需手動驗 LICENSE）。對同時訂閱多款 coding agent、希望「無人值守多 agent 編輯」的研究型開發者最相關。
- **3W1H：**
  - **What：** Electron + React + TypeScript + Pixi.js 寫的本機多 agent 編排桌面 app（自稱 MIT license、API 回 NOASSERTION）。
  - **Why：** 各家 coding CLI 各自有額度與時段限制，harness 把多個 CLI 組成本機「輪班辦公室」，靠訊息路由 + 跨 session 記憶 + 自動回復 agent 額度，讓「整夜跑任務」成真。
  - **Who：** 重度 agent 訂閱者、研究 harness-engineering 的工程師、想體驗「自己跟一支 agent clone 對話」的開發者。
  - **How：** `git clone` → `npm install`（會跑 `electron-rebuild` 對 `node-pty` 重新編譯 native addon）→ `npm run dev` 啟動 Electron app；生產用可從 GitHub Releases 下載 `.dmg` / `.AppImage` / `.deb` 預構建版本（autoupdate 背景下載新版）。Prereq：Node 18+、Xcode CLT（macOS）、至少一款 agent CLI（缺什麼 Michael 會問要不要自動裝）。
- **安裝方式：**
  - **npm/Node（開發模式）：** `git clone https://github.com/chaitanyagiri/munder-difflin.git` → `cd munder-difflin` → `npm install` → `npm run dev`（Electron + HMR）
  - **預構建桌面 app（end user）：** 從 [GitHub Releases](https://github.com/chaitanyagiri/munder-difflin/releases/latest) 下載對應平台的 `.dmg` (macOS) / `.AppImage` (Linux) / `.deb` (Debian) — app 內建 autoupdate
  - **build 工具鏈：** `npm run build` (electron-vite production) / `npm run preview` / `npm run typecheck`
  - **license 注意（case-G）：** README 自稱 `MIT`，但 GitHub API 回傳 `license.spdx_id = "NOASSERTION"` — 商用 / 嵌入前需手動讀 `LICENSE` 檔確認條款
- **近期 release：**
  - **v0.4.4** — 發布日期：**2026-08-18**（published_at: `2026-08-18T02:38:24Z`）
  - 重點：新增 Skills 設定頁（瀏覽 227 個 catalog、搜尋 / 過濾 / 安裝 / 卸載）、Prerequisites 設定頁（顯示 uv / git / Node / MemPalace / 各 agent CLI 是否已裝、缺什麼可請 Michael 自動裝）、release notes 從單純版本字串升級為「自帶設計的 release 頁」；**0.3.8 用戶強烈建議升級**，因為該版的 usage-limit guard 從未釋放它持有的 agents。

### akitaonrails/ai-memory
- **Repo 摘要：** Rust 寫的長期記憶層，給 coding agent CLI（Claude Code / Codex / Command Code / Kiro / Grok / Zero / Swival / Crush + **Hermes Agent (Community)**）用 — 用 lifecycle hooks + MCP 自動 capture 工作 session、支援跨 vendor handoff（「在 Claude Code 做到一半，切到 OpenAI Codex 接手不用重述」）。2.9k★ 已是 Rust agent 工具鏈的成熟選項，2026-08-18 才發 v1.28.1（daily active）。支援 Linux (Docker / AUR)、macOS 原生、Apple Silicon 二進位、Windows-WSL2 與原生 Windows（實驗性），主筆 akitaonrails 是巴西資深 Ruby/Rust 工程師。對同時使用多款 coding agent、希望 session 跨工具延續的開發者直接命中。
- **3W1H：**
  - **What：** Rust 寫的 long-term memory server + lifecycle hook 框架，給 AI coding agent 跨 vendor session handoff 用。
  - **Why：** 解決「中途切換 coding agent 就得重述架構 / 失敗嘗試 / 開放問題」的痛點；自動 capture + 跨 agent handoff 讓多 agent 接力變得實用；不綁特定 LLM / agent 廠商。
  - **Who：** 多 agent 訂閱者（Claude Code + Codex + Kiro + Grok + Hermes）、需要可審計 session 歷史的團隊、想 self-host 記憶層的企業。
  - **How：** 從 AUR / Docker / GitHub Releases 三條路徑裝 CLI，啟動 server（loopback-only 預設無認證），對每個 agent 跑 `ai-memory install-mcp --client <name> --apply` + `ai-memory install-hooks --agent <name> --apply` 自動掛 capture / handoff；目前 `Hermes Agent` 列為 Community（透過第三方 [`MrLuciano/ai-memory-hermes-plugin`](https://github.com/MrLuciano/ai-memory-hermes-plugin)）。
- **安裝方式：**
  - **Arch Linux AUR：** `yay -S ai-memory-bin`（預構 Linux x86_64/aarch64）+ `yay -S ai-memory`（從 source 編譯）— 自動裝 `/usr/bin/ai-memory` + system / user systemd units
  - **Docker（跨平台推薦）：** 從 [Releases](https://github.com/akitaonrails/ai-memory/releases) 下載 `ai-memory-wrapper` shell script（SHA256 校驗）→ `install -m 0755 ai-memory-wrapper ~/.local/bin/ai-memory` → `docker run -d --name ai-memory -p 127.0.0.1:49374:49374 -v ai-memory-data:/data akitaonrails/ai-memory`（loopback-only 預設）
  - **macOS 原生：** 從 Releases 下載 `ai-memory-macos-aarch64.tar.gz`（Apple Silicon 推薦）或 `ai-memory-macos-x86_64.tar.gz`（見 `docs/macos.md`）
  - **Windows：** 從 Releases 下載 `ai-memory-windows-x86_64.zip`（實驗性，含 `ai-memory.exe` + Docker Desktop wrapper + 源碼 build）
  - **Hermes Agent 整合：** `pip install` 不適用（Rust binary 為主），需裝 `MrLuciano/ai-memory-hermes-plugin` 第三方 plugin 並手動接 hooks
  - **首次註冊到 coding agent：** `ai-memory install-mcp --client claude-code --apply` + `ai-memory install-hooks --agent claude-code --apply`（其他 agent 把 `claude-code` 換成 `codex` / `kiro` / `grok` / `zero` / `swival` 等）
- **近期 release：**
  - **v1.28.1** — 發布日期：**2026-08-18**（published_at: `2026-08-18T16:27:40Z`）
  - 重點：checksums-only release artifact（`ai-memory-hooks.tar.gz` / `ai-memory-install-hooks` / `ai-memory-linux-aarch64.tar.gz` 帶 SHA256）；README 內含跨 10+ agent 的詳細 Support Matrix。

### mvanhorn/last30days-skill
- **Repo 摘要：** AI agent skill 形式的「跨平台社群搜尋引擎」 — 同時搜 Reddit 評論、X 推文、YouTube 影片、HN 討論、Polymarket 押注、Bluesky、arXiv 等多源訊號，由 agent judge 綜合成「依 upvote / like / 真金白銀押注加權」的單一研究摘要。58k★ 在 skill 類 repo 中屬超大體量，2026-08-18 才發 v3.21.1。完整支援 50+ agent host（Claude Code / Codex / Cursor / Copilot / Gemini CLI / OpenClaw / Claude.ai web / Claude Desktop），README 自稱 GitHub Trending #1 Repository Of The Day。對研究員 / 投資人 / 內容創作者「一次看完 30 天內社群最在意什麼」直接命中。
- **3W1H：**
  - **What：** Python 寫的 agent skill（Type-11 安裝） — AI agent-led search engine 跨 Reddit / X / YouTube / HN / Polymarket 平行搜尋。
  - **Why：** 取代「編輯篩選」的傳統搜尋；用平台原生 engagement 訊號（upvote / like / 真實押注金額）當 relevance 排序依據；零設定、setup wizard 30 秒解鎖付費源。
  - **Who：** 研究員（看 Reddit + arXiv + HN）、交易員（看 Polymarket + X 情緒）、內容創作者（看 TikTok / YouTube engagement）、AI agent 使用者。
  - **How：** 對 Claude Code 跑 `/plugin marketplace add mvanhorn/last30days-skill` + `/plugin install last30days`；對 Codex / Cursor / Copilot / Gemini CLI 跑 `npx skills add mvanhorn/last30days-skill -g`；對 OpenClaw 跑 `clawhub install last30days-official`；對 claude.ai web 下載 `last30days.skill` 從 Customize > Skills 上傳。
- **安裝方式：**
  - **npx/npx skills（50+ host 通用）：** `npx skills add mvanhorn/last30days-skill -g`（`-g` 全域、跨 project；移除 `-g` 變 per-project）→ 後續更新 `npx skills update last30days -g`
  - **Claude Code plugin marketplace（推薦）：** `/plugin marketplace add mvanhorn/last30days-skill` → `/plugin install last30days` → 自動更新 / 手動 `claude plugin update last30days@last30days-skill`
  - **xAI Grok Build CLI：** `grok plugin marketplace add mvanhorn/last30days-skill` → `grok plugin install last30days` → `grok plugin update last30days`
  - **OpenClaw：** `clawhub install last30days-official` → `clawhub update last30days-official`
  - **claude.ai web：** 從 [Releases](https://github.com/mvanhorn/last30days-skill/releases/latest/download/last30days.skill) 下載 `.skill` 檔 → 上傳到 claude.ai > Customize > Skills > + > Create skill > Upload a skill
  - **Claude Desktop：** 從 [Releases](https://github.com/mvanhorn/last30days-skill/releases/latest) 下載 `.mcpb` 套件 → 拖到 Settings > Extensions
- **近期 release：**
  - **v3.21.1** — 發布日期：**2026-08-18**（published_at: `2026-08-18T17:15:41Z`）
  - 重點：Sonar (Perplexity) 整合從舊版 API 遷移到新 Agent API（PR #1019）、3.21.1 version bump（PR #1022）。

### pipeshub-ai/pipeshub-ai
- **Repo 摘要：** 開源 Workplace AI 平台 — 把企業內部資料（Drive / Gmail / Slack / 程式碼倉等）統一成可解釋的 enterprise search 與 agentic workflow，提供 BYO LLM、自架 MCP server、Python / TS / Go 三套 SDK。3.4k★ + Apache-2.0 + 互動式 Docker Compose 安裝是企業自架 AI 平台的現代主流模式。對不願把企業資料送上 SaaS、想要 self-host enterprise search + agent 編排的中大型團隊直接命中。
- **3W1H：**
  - **What：** Python + Node SDK + 多 Docker service 組成的開源 Workplace AI / enterprise search 平台。
  - **Why：** 取代 Notion AI / Glean / Slack AI 等 SaaS，把所有企業資料接到自架 LLM（BYO model in your VPC）— 資料不出基礎設施、可審計、可解釋 citations。
  - **Who：** 重視資料主權的中大型企業 IT / DevOps 團隊、要自架 agent 編排層的開發者、研究 enterprise RAG 架構的研究員。
  - **How：** `git clone` → `cd deployment/docker-compose` → `./install.sh`（互動式安裝腳本處理 secrets / graph DB / broker / image tag 選擇，自動生成 `.env`）；預設開 `http://localhost:3000`，可加 `--yes` 跳過互動、`--version TAG` 鎖版、`--print-env-only` 只生成 env 不啟動。SDK 三選一：Python / TypeScript / Go（各自獨立 repo）。
- **安裝方式：**
  - **Docker Compose（推薦 self-host）：** `git clone https://github.com/pipeshub-ai/pipeshub-ai.git` → `cd pipeshub-ai/deployment/docker-compose` → `./install.sh` → 開 `http://localhost:3000`（互動式 installer 處理 slim / full 部署、隨機 secrets、健康檢查）
  - **npm SDK：** `@pipeshub-ai/sdk` (Node) + `@pipeshub-ai/mcp` (MCP) 從 npm registry 安裝
  - **語言 SDK（獨立 repo）：** Python `pipeshub-ai/pipeshub-sdk-python` / TypeScript `pipeshub-ai/pipeshub-sdk-typescript` / Go `pipeshub-ai/pipeshub-sdk-go`
  - **MCP server（獨立 repo）：** `pipeshub-ai/mcp-server`
  - **雲端部署注意：** README 明確提醒瀏覽器會擋純 HTTP — 雲端 server 必須先掛 HTTPS（Cloudflare / Nginx / Traefik），否則會出現白屏
  - **installer flags：** `-y` / `--yes`（CI 模式跳過互動） / `--version TAG`（pin 版） / `--reconfigure`（重跑 wizard） / `--print-env-only`（只生 .env 不啟動）
- **近期 release：**
  - **0.6.0** — 發布日期：**2026-08-10**（published_at: `2026-08-10T16:51:55Z`）
  - 重點：修 deep agent stale tasks（PR #2656）、修 invite user mail error logging（PR #2658）、`build(deps-dev)` 把 `js-yaml` 從 4.1.1 bump 到 4.3.0；release 距今 9 天，但仍持續有 daily commits（如「Add prompt-injection guard to sub-agent execution rules」PR #2987）。

## 重點觀察
- **安裝類型本週 5/5 全部不同：每個 repo 對應不同 install idiom** — MoneyPrinterTurbo (Type-6 Docker Compose + uv 手動)、munder-difflin (Type-13 Electron `npm install` + GitHub Releases .dmg/.AppImage/.deb)、ai-memory (Type-13 variant AUR + Docker wrapper + 多平台 binary + Hermes 第三方 plugin)、last30days-skill (Type-11 agent skill marketplace，橫跨 `/plugin` + `npx skills` + `clawhub` + `.skill` 上傳四種 path)、pipeshub-ai (Type-6 互動式 `./install.sh` + 三語言 SDK + 獨立 MCP repo)。本週 5 個 install type 完全不重疊，是觀察以來最豐富的一組。
- **Release 新鮮度 4/5 = 80% 在 7 天內發版**：MoneyPrinterTurbo v1.3.4 (08-12, 7d)、munder-difflin v0.4.4 (08-18, 1d)、ai-memory v1.28.1 (08-18, 1d)、last30days-skill v3.21.1 (08-18, 1d) 都是最近一週內 release；只有 pipeshub-ai v0.6.0 (08-10, 9d) 略舊但 daily commits 仍活躍（PR #2987 當天 push）。**比 08-17 的 60% baseline 略高**，呼應 08-14 + 08-15 的 80% peak 級距，活動訊號強。
- **License 4/5 permissive + 1 case-G**：4 個 repo 是 MIT（MoneyPrinterTurbo、ai-memory、last30days-skill）或 Apache-2.0（pipeshub-ai，case-A2 帶 patent grant 對 horo-agent 下游最友善）；唯一灰色是 munder-difflin（README 自稱 MIT、API 回 `NOASSERTION` + license file 需要手動驗 — case-G），對 horo-agent / horo-webui air-gapped downstream 需逐一讀 LICENSE 檔確認商用條款。
- **Hermes-as-official-agent-host signal 持續第 3 個 14:00 tick 出現**：今天 `akitaonrails/ai-memory` 支援矩陣明列 `Hermes Agent` 為 Community（透過第三方 plugin），呼應前兩日（08-16 `HKUDS/CLI-Anything` 14 個 host 含 Hermes + 08-17 `unslothai/unsloth` `unsloth start hermes`）。MEMORY 中主人「目前大量使用 NousResearch/hermes-agent 與 nesquena/hermes-webui」的 direct match 已連續 3 天在 14:00 GitHub 自由探索 series 中命中 — 是 14:00 series 自開始以來最強且最長的 MEMORY 訊號，趨勢可推斷「第三方 agent framework 從 early adopter 階段進入 production-ready 整合階段」。
- **5 個 repo 中 4 個聚焦 agent / LLM 基礎設施**：MoneyPrinterTurbo（LLM × 影音自動化）、munder-difflin（多 agent harness）、ai-memory（agent 長期記憶 + 跨 vendor handoff）、last30days-skill（agent 社群搜尋 skill）、pipeshub-ai（enterprise agent platform）。**只有 MoneyPrinterTurbo 是消費端短影片工具**；其餘 4 個都圍繞「AI agent 編排 / 工具鏈」 — 反映 2026-08 GitHub Trending 已從「模型本體 / 推理加速」轉向「agent 編排 / 工具整合 / 跨平台抽象」的明顯位移。對主人 horo-agent 下游 / air-gapped lite 設計：把記憶層（ai-memory）、多 agent harness（munder-difflin）、skill marketplace（last30days-skill）視為「horo-agent 未來可選 plug-in 路徑」是合理的方向，無需從零自建。
