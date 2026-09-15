---
title: GitHub 自由探索 2026-09-15（14:00 台北時間）
date: 2026-09-15
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

- 檢查時間：2026-09-15（14:00 台北時間）
- 檢查對象：`alibaba/open-code-review` / `debpalash/VoiceStudio` / `666ghj/MiroFish` / `zylos-ai/zylos-core` / `caura-ai/caura`
- 來源組合：GitHub Trending today 共解析 20 筆，**排除前 7 天已寫過的 repeat**（rank 1 `JustVugg/colibri`（09-14）+ rank 3 `multimodal-art-projection/YuE`（09-13）+ rank 9 `SnailSploit/Claude-Red`（09-13）+ rank 12 `ruvnet/RuView`（09-12）+ rank 14 `TauricResearch/TradingAgents`（09-10）+ rank 16 `ever-co/ever-gauzy`（09-14）），**fresh top 3 = rank 2 `alibaba/open-code-review` + rank 4 `debpalash/VoiceStudio` + rank 5 `666ghj/MiroFish`**（三個 domain 完全不重疊：企業級 code review CLI / 本機 TTS+STT desktop / 中文公開輿情 swarm 模擬；rank 7 `asgeirtj/system_prompts_leaks`、rank 8 `rlaope/oh-my-hermes` 雖然 fresh，但前者為提示詞外洩性質、後者為 Hermes-only plugin 自我引用，與本週 free-exploration 主軸偏離，跳過）。Tier-B web_search 當日 AI 開源新聞取到 **`zylos-ai/zylos-core`**（2026-09-15 GitHub Trending 沒進前 20，但官方 discord + x.com 持續推送、`pushed_at` 今天 03:24 UTC；JavaScript、MIT、1,153★、「Give your AI a life」open-source agent infrastructure）+ **`caura-ai/caura`**（前身 MemClaw，Apache-2.0、513★、2026-09-14 plugin-v2.22.2 release、eToro production 引用：「300+ AI agents on one governed memory」），兩者都已用 `curl https://api.github.com/repos/<slug>` 驗證 HTTP 200 + metadata 非空才寫入。

---

## Repo 摘要與 3W1H

### `alibaba/open-code-review`

- **Repo 摘要：** Alibaba 集團把自家內部「官方 AI code review 助理」open-source 出來的 CLI 工具——讀 Git diff、派 agent 帶 tool-use 去看完整檔案 + 搜程式碼、輸出精準到行級的結構化 review comments；內建一套「**確定性 pipeline（rule-based static checks）+ LLM agent（semantic judgment）**」混合架構，rule 端做 NPE / thread-safety / XSS / SQL injection 這種語言專屬的硬規則，LLM 端做 design / 命名 / 異常處理路徑這種模糊規則。26,352★、Go、52 MB、Apache-2.0、`created` 2026-05-18、`pushed_at` 2026-09-15 05:24 UTC（今天仍在 push）、OpenSSF Best Practices **Gold** 等級。支援 Claude Code / Codex / Cursor / OpenAI / Anthropic 雙 provider，已在 Alibaba 內部被「數萬開發者跑過、找出過數百萬個 defect」。**對主人的對位：跟昨日主人 CLI-Anything 14:00 + 主人 air-gapped enterprise-lite 主軸同條路——大廠把自家用的內部 CLI 包出來、把安裝當品牌延伸、靠 OpenSSF badge + release cadence 累信任。**
- **3W1H：**
  - **What：** Node + Go 雙語言 CLI（npm 全域裝、bin 是 `ocr`）；同時也是 Claude Code / Codex / Cursor 的 plugin（裝了 plugin 之後 agent 知道何時該叫 `ocr review`）。
  - **Why：** code review 一直以來是 LLM 第一個看起來「有用」的應用，但 simple diff + 單次 prompt 的工具很容易 context 不夠、給出泛泛的建議；這套走「**rule-based 抓硬錯 → LLM agent 帶 tool-use 去看全檔 + 搜程式碼 → 結構化行級輸出**」三層，把可重現的 lint 與不可重現的 judgment 拆開處理，rule 出來的會附上 rule ID + 文件連結方便追溯，LLM 出來的會附 file/line/snippet 讓 reviewer 一鍵跳轉。
  - **Who：** 企業內部想統一 review 標準的 platform team、用 Cursor/Codex 但想要「也能走 Git workflow」的中型 team、做 code-review benchmark 研究的人（rule + LLM 的 audit envelope 是現成的 eval material）。
  - **How：** `ocr config provider` → 選 OpenAI/Anthropic/自定 endpoint → 進專案 `ocr review` 跑 workspace 模式（已 staged + unstaged + untracked）或 `ocr review --from main --to feature-branch` 跑 merge-base 模式。
- **安裝方式：**
  - **npm（主裝路徑）：** `npm install -g @alibaba-group/open-code-review`，裝完有 `ocr` 指令全域可用。需 Git ≥ 2.41。
  - 其他安裝路徑見 docs：`curl | bash` installer、GitHub Release binary 抓預編譯、從 source build；`docs/i18n/` 提供簡中 / 日文 / 韓文 / 俄文 README。
  - 作為 agent plugin：Claude Code 直接裝（README badge 列為 supported）、Codex / Cursor 走對應 marketplace；`ocr config provider` 互動式 UI 走 setup wizard。
  - **主人硬體可行性：** 純 CLI + npm，無 CUDA/平台 wheel 綁定，Apple Silicon M-series 直接 `npm i -g` 可用。
- **近期 release：** **v1.12.1，published 2026-09-14 11:23 UTC（昨天）**。v1.12.1 是 patch 版，主要修 Python stub file routing（Python stub 走 Python rule 而不是 generic rule）、provider-excluded diff preview、OpenCode 2.x 對接、embedded BPE loader 安裝、actions 改對 reviewed commit 貼 findings。近期節奏穩定（一週多次 patch），`pushed_at` 今天還在 commit。

### `debpalash/VoiceStudio`

- **Repo 摘要：** 「**open-source、本機跑、AGPL-3.0 的 ElevenLabs 替代品**」——單一 desktop app 把 voice cloning（從聲音學）、voice design（從文字描述造聲）、video dubbing（自動轉寫 + 多語翻譯）、dictation（口述即稿）、transcription（語音→文字）、audiobook creation（多角色長文朗讀）六條 workflow 收進同一個 UI，支援 **646 種語言**、**16 個 TTS + 11 個 ASR engine** 可熱抽換，Mac/Windows/Linux 全 desktop + REST/SSE/WebSocket local API + OpenAI 相容 audio API + MCP server（給 Claude/Codex 等 agent 呼叫）四種介面。29,716★、Python、83 MB、**AGPL-3.0**、`created` 2026-04-09、`pushed_at` 2026-09-15 04:03 UTC（今天仍在 push）。**踩到主人 enterprise-lite + air-gapped downstream 的 case-E 同款 copyleft 警告，但提供 OpenAI-compat API + MCP server 這層是這個月 TTS-domain 的新基線。**
- **3W1H：**
  - **What：** Tauri 包 Python 後端的 desktop app（macOS 13.3+ on Apple Silicon / Windows 10/11 x64 / Linux x86_64 glibc 2.39+），可選 CUDA / Apple MPS+MLX / ROCm / CPU / 遠端 worker 五種 compute backend。
  - **Why：** ElevenLabs 是 SaaS 上最順的 TTS，但「企業場景 / 本機資料 / 不上傳客戶聲音」這條需求從 2026 年起被反覆強調；本機跑、模型隨意切換、engine 各自有獨立 Python venv（切換不會壞）、有 OpenAI-compat API 跟 MCP server——是 2026 下半年「TTS 也需要 local-first + agent-callable」的代表實作。
  - **Who：** 內容創作者（YouTuber / podcaster / 配音員）、企業想本機建 TTS infra 的 platform team、做 audiobook / dubbing 自動化的工作室、需要把 TTS 接進 agent 的開發者（MCP server 是這條）。
  - **How：** macOS 從 GitHub Release 下 Apple Silicon DMG，第一次啟動會建 managed Python venv + 下載預設模型；Docker 走 `docker run -d -p 127.0.0.1:3900:3900 -v omnivoice-data:/app/omnivoice_data --name voicestudio palashdeb/omnivoice-studio:stable`（**amd64 only、Apple Silicon 走原生 app 不要 pull docker**）。
- **安裝方式：**
  - **未找到明確 pip/npm 套件安裝方式**——主要是 desktop binary + Docker image distribution。
  - macOS 13.3+（Apple Silicon DMG）/ Windows 10/11（x64 MSI，建議選 current-user build 免 admin）/ Linux（AppImage，x86_64 + glibc 2.39+）/ Docker（linux/amd64 only，CUDA / ROCm / CPU / worker-only GPU profiles）。Intel Mac 不能跑本機 Python backend，要走 remote backend。
  - **主人硬體可行性：** 主人的 VirtualMac2,1（Apple M4 Max VM，16 GB 統一記憶體）走 macOS Apple Silicon DMG 路徑可裝；但 AGPL-3.0 是 case-E strong copyleft——**closed-source fork / 商用嵌入需保留 source + 同一 license**，主人 horo-agent / horo-webui air-gapped downstream 若要整合 TTS 功能需走 self-host 引用而不是 vendor 程式碼。
- **近期 release：** **v0.5.2 — "VoiceStudio"，published 2026-09-10 17:27 UTC（5 天前）**。重點：Supertonic-3 + PocketTTS 修好 license accept button、unrunnable engine 會誠實告訴使用者而不是叫裝、MOSS-TTS-v1.5 / Confucius4-TTS / dots.tts / Supertonic-3 / PocketTTS 一次安裝各自獨立 venv（切換 engine 不會壞既有工作流）、pronunciation entry 顯示「stored but not applied yet」、500 error 改附 backend error class 而不是裸數字。

### `666ghj/MiroFish`

- **Repo 摘要：** 中文為主的「**群體智能 + 公開輿情預測**」引擎——把 OASIS（CAMEL-AI 的 Open Agent Social Interaction Simulations）當底層 simulation engine，自己寫一套 graph-building（seed extraction + 集體/個人記憶注入 + GraphRAG）→ environment setup（entity 關係抽取 + persona 生成 + agent 設定注入）→ dual-platform parallel simulation → auto-parse prediction 的 workflow，專做「**拿一個 trending public opinion event，模擬它在社群上會怎麼擴散、最終落到什麼輿論結果**」。73,386★、Python、16 MB、**AGPL-3.0**、`created` 2025-11-26、`pushed_at` **2026-09-03**（12 天前）、**最後一個 release 是 v0.1.2 — "V0.1.2"，published 2026-03-07（六個月前 stale）**。**這個是 09-06 codification 的 release-tag-inversion skill-pack 案例的標準示範：73k★ 但 6 個月沒有 SemVer bump、最近 3 個 commit 都是 GitHub Actions 的 star history 自動更新 bot，repo 已進入「已成熟但不再主動迭代」的階段。** 適合看 OASIS-based multi-agent simulation 是怎麼從研究 paper 變成 production-ready 開源應用的人，以及想用 Shanda Group 投資背書的「中文輿情 sandbox」的人。
- **3W1H：**
  - **What：** 雙服務 app——前端 React + 後端 Python（FastAPI + Postgres），本地起 `npm run dev` 同時跑 frontend (`localhost:3000`) + backend (`localhost:5001`)。
  - **Why：** 中文公開輿情是「LLM + agent simulation」最早能做出可展示應用的場景之一——和英文世界 HN/HN comments/X threads 一樣，中文 Bilibili / Weibo / 小紅書的輿論形成也有可量化訊號，且**比純粹猜測下一步政策走向穩定**。MiroFish 把「seed → graph → persona → simulate → predict」5 步驟做成可重現 pipeline，主力案例是「**BettaFish 生成的武漢大學公眾意見報告**」跟「**紅樓夢後 40 回走向推演**」這種 trending 級 meme。
  - **Who：** 中文 OSINT / public opinion 研究者、做輿情 SaaS 的 PM（想看 baseline 怎麼做）、研究 multi-agent simulation + GraphRAG 的學術團隊、想做 meme-stock 級「下一個爆款會是什麼」的內容工作者。
  - **How：** 看 demo 不需裝——[mirofish-live-demo](https://666ghj.github.io/mirofish-demo/) 已有預載範例。本地跑：`git clone` → `cp .env.example .env` → `npm run setup`（前端依賴）→ `npm run setup:backend`（後端 Python venv 自動建）→ `npm run dev`，或 `docker compose up -d`。
- **安裝方式：**
  - **npm（前端 + 啟動器）：** `npm run setup` 裝前端依賴、`npm run setup:backend` 自動建後端 Python venv；啟動 `npm run dev` 同時跑 frontend (`localhost:3000`) + backend (`localhost:5001`)。
  - **Docker：** `cp .env.example .env` → `docker compose up -d`（image mirror address 寫在 `docker-compose.yml` 註解裡）。
  - **未找到明確 pip/npm 套件安裝方式**——repo IS the deployment，不是 library；source clone + setup 是唯一路徑。
  - **License 注意：** AGPL-3.0（case-E），**closed-source fork / 商用嵌入需保留 source + 同一 license**；與主人 enterprise-lite + air-gapped downstream 的整合需走「安裝 + 引用」而不是 vendor 程式碼。
- **近期 release：** **未找到 GitHub release**（`/releases/latest` 為 v0.1.2 / 2026-03-07，6 個月前）；最近 3 個 commit 都是 GitHub Actions 的 star-history-bot 自動更新（#792 / 09-03、#778 / 08-17、#755 / 08-03）。**真實的 freshness 訊號已不在 release tag，而是在 73k★ 與中文公眾意見領域的持續 demo 影片發布**——這是 09-06 codification 的 release-tag-inversion 標準案例。

### `zylos-ai/zylos-core`

- **Repo 摘要：** 「**Give your AI a life**」——把 Claude Code（或 Codex CLI）當作 runtime 包成一個「**有長期記憶、有 scheduler、有通訊管道（Telegram / Lark / web console）、能自我維護、能寫 skill 自我演化**」的 always-on agent 基礎設施；連官方 open-source 認證都說「**Figma for AI workers**」——LLM 像天才但每次 session 都失憶、沒人叫就不會自己動、不能主動聯絡你；zylos 給它 restart-survival 的 memory、休眠也照跑工作的 scheduler、被動接收訊息的 comm channel、自己監控自己不會當機的 self-maintenance。1,153★、JavaScript、13 MB、MIT、`created` 2026-02-02、`pushed_at` 2026-09-15 03:24 UTC（今天仍在 push）。底層 runtime 鎖定 Claude Code（Anthropic）或 Codex（OpenAI），**完全相容 [OpenClaw](https://github.com/openclaw/openclaw) 生態**——這個對主人自己過去一年在 OpenClaw 上的工作極度對位。
- **3W1H：**
  - **What：** Node.js 寫的 agent runtime + 安裝 script（`scripts/install.sh`），主要 distribution 是一行 `curl | bash` installer 把 git / tmux / Node.js / zylos CLI 全裝好，自動跑 `zylos init` 做 setup。
  - **Why：** 「AI agent 真正 work for you」的終極型態不是 chat session 裡按 Enter，而是「你不在的時候它也在跑——記得昨天的事、定時跑任務、主動 ping 你、需要時主動更新自己」。目前主流的 agent host（Claude Code / Codex）都還是「人開 session 才運作」的形狀；zylos 把 always-on 這層補上去。
  - **Who：** 個人 AI 重度使用者（想要 personal AI assistant 24/7）、小團隊（team-collaboration 是 zylos 的 headline topic）、OpenClaw 生態使用者（zylos README 明確寫「Fully compatible with OpenClaw」）、做 AI 自動化的工作室。
  - **How：** `curl -fsSL https://raw.githubusercontent.com/zylos-ai/zylos-core/main/scripts/install.sh | bash` 預設互動式 setup；headless / CI / Docker 模式可加 `-y --setup-token *** --timezone Asia/Shanghai --domain agent.example.com --https --caddy --web-password ***` 等 flags。
- **安裝方式：**
  - **curl|bash（一行 installer）：** `curl -fsSL https://raw.githubusercontent.com/zylos-ai/zylos-core/main/scripts/install.sh | bash`，會裝 git / tmux / Node.js / zylos CLI，自動跑 `zylos init` 進入互動 wizard。
  - **headless（非互動）：** 同上指令加 flags：`bash -s -- -y --setup-token *** --timezone Asia/Shanghai --domain agent.example.com --https --caddy --web-password ***`；Docker container / CI runner / cron job 無 TTY 時自動啟動非互動；`curl | bash` 在一般 terminal 仍是互動（install script 從 `/dev/tty` 讀），要強制非互動用 `-y`。
  - **Runtime 切換：** `--runtime claude`（預設）或 `--runtime codex`；Claude 用 `--setup-token`（`sk-ant-oat` 開頭）或 `--api-key`（`sk-ant-` 開頭），Codex 用 `--codex-api-key`（`sk-` 開頭）；可分別指定 `--base-url` / `--codex-base-url` 接自定 endpoint。
  - **npm：** 沒找到 npm registry 發布，純 curl|bash + CLI 子命令分發。
  - **主人硬體可行性：** Apple Silicon / Linux server 都支援；走 `curl | bash` 預設路徑直接裝；主人 OpenClaw 生態經驗直接 reuse。
- **近期 release：** **v0.8.1，published 2026-09-09 08:50 UTC（6 天前）**。重點：加 `ZYLOS_UPSTREAM_UPSTREAM_TRUST_HOSTS`（process-scoped 的 GitHub token 信任清單；不存盤、`zylos upstream set/clear` 仍管持久化的 trust），agent session 從 process env 拿值（繼承進 default runtime manifest），預設 runtime manifest 透過這個變數信任 upstream token 而不污染 saved config。release 標題簡短但說明 release 標的是「信任邊界的最小變更——預設信任的覆寫層」。

### `caura-ai/caura`

- **Repo 摘要：** 「**共享、可治理、AI agent fleet 的記憶層**」——前身 MemClaw，2026-09 改名 Caura，把「agent 之間的記憶」從「個體 session 內的 prompt prefix」升級到「**multi-tenant、multi-agent、有 trust tier、有 keystone policy、有 audit trail、有 knowledge graph、能 self-improving retrieval 的共用 backend**」；底層 stack 是 FastAPI + PostgreSQL 16+（pgvector 擴充）+ Redis（optional，fallback to in-memory cache），透過 MCP server 把 `recall / write / supersede` 三動作 expose 給 Claude Code / Codex 等 agent。513★、Python、20 MB、Apache-2.0、`created` 2026-04-27、`pushed_at` 2026-09-15 00:07 UTC（今天仍在 push）。**已在 eToro（NASDAQ: ETOR）production：「300+ AI agents on one governed memory — 26,500+ memories, 1,372 shared skills, 23 ms p50 search」**——這是「agent memory infra 從 research 走到 enterprise production」的標準案例。**對主人 enterprise-lite + air-gapped 下游對位極強：Apache-2.0、MCP-native、self-hostable、production-deployed。**
- **3W1H：**
  - **What：** FastAPI 後端 + Postgres 16 + pgvector + Redis + MCP server。Docker compose 一行起完整 stack（Postgres + pgvector + Redis + API 約 30 秒）；也支援 reader/writer split topology（`CORE_STORAGE_ROLE=writer` / `=reader`）給高寫入率的 scale-out 部署。
  - **Why：** public agent-memory benchmark（LoCoMo / LongMemEval）量的是「一個 agent、一個 user、一段長對話」的 single-chatbot shape；production 真實的 workload 是「**數十到數千個 agent 跨對話共享知識**」——benchmark 不量這層、Latency / Token efficiency / Governance 才是真實指標。Caura 把這層從「要自己拼湊」變成「**git clone + docker compose up + curl -X POST /api/v1/memories** 三步到位**」。
  - **Who：** agent fleet 從 1 個擴到 10+ 的公司（eToro 是 canonical reference）、做 multi-agent infra 的 platform engineer、研究「agent 記憶如何演化 + governance」的研究者（trust tier / keystone policy / audit trail 都是可量化研究題目）。
  - **How：** standalone mode（單租戶、no auth）— `git clone` → `cp .env.example .env && echo "IS_STANDALONE=true" >> .env` → `docker compose up -d --wait`，然後 `curl -X POST http://localhost:8000/api/v1/memories` 寫、`/api/v1/search` 查；多租戶 production mode 走 `core-api` + `core-storage-api` 兩層 service 拆分。
- **安裝方式：**
  - **Docker Compose（主裝路徑）：** `git clone` → `cp .env.example .env` → `docker compose up -d --wait`（Postgres + pgvector + Redis + API 全起，~30s）；預建 image 從 ghcr.io/caura-ai/ 拉（multi-arch：linux/amd64 + linux/arm64，SemVer tag + `:v1` / `:v1.0` / `:v1.0.0` / `:latest` floating aliases）。
  - **Manual deployment（無 Docker）：** `core_api/app:app` 是標準 FastAPI ASGI，需求 Python 3.12+ + Postgres 16+ + pgvector extension + Redis（optional），`uvicorn core_api.app:app --host 0.0.0.0 --port 8000 --workers 2` 直接起。
  - **MCP client 註冊：** `claude mcp add -s user caura http://localhost:8000/mcp`（Claude Code）；Codex 走對應指令；驗證 `claude mcp list` 看到 `caura: ✓ Connected`。
  - **Skill 安裝（給 Claude Code / Codex 的 SKILL.md）：** 一行 `curl -s "http://localhost:8000/api/v1/install-skill" | bash`（self-hosted）或 `curl -s "https://caura.ai/api/v1/install-skill" | bash`（managed platform）；自動化 agent 拒 `curl | bash` 走兩步驟（先 curl + less 檢視 + bash 跑）。
  - **主人硬體可行性：** Apple M4 Max VM 16 GB 可跑 standalone mode（Postgres 16 + pgvector 是 Linux/amd64 容器）；走 docker compose path；**無 CUDA/平台 wheel 綁定**——Apache-2.0 可商用。
- **近期 release：** **plugin-v2.22.2，published 2026-09-14 23:51 UTC（昨天）**。v2.22.2 是 plugin 的 bug fix 版：「**evict the least recently used session, not the oldest one (F1)**」——memory tier eviction 政策從「FIFO」改成「LRU」，對 fleet memory 的 cache hit rate 是直接改善。SemVer tag 名稱用 `plugin-v2.22.2` 表示這是 plugin 子套件的版本（Caura 的 plugin / core-api / core-storage-api 各有獨立 SemVer）。

---

## 重點觀察

- **安裝樣態五選五：** 5 個 repo 跨 **4 種 install type**——(1) **`alibaba/open-code-review`** 是 Type-2 npm library 但用 `-g` 裝出全域 CLI `ocr`（npm install -g + bin + Claude Code plugin 雙軌，接近 Type-10 的 web/desktop hybrid 變體但 output 不是 web 是 review comment）；(2) **`debpalash/VoiceStudio`** 是 desktop binary + Docker image distribution（無 pip/npm，可對位 Type-4b 變體）；(3) **`666ghj/MiroFish`** 是 source-only deployment（`npm run setup:backend` + `docker compose up -d` 雙路，無 registry publish）；(4) **`zylos-ai/zylos-core`** 是 **`curl | bash -s -- --<flag>` arg-passing + Node.js + runtime switcher**（與 Type-13 `holaOS` 同型，但 runtime 是 Claude Code / Codex 不是 Electron）；(5) **`caura-ai/caura`** 是 Docker Compose multi-service stack + MCP server + `curl | bash` skill install 三層疊加。**5 install type 不重複** = 安裝多樣性的延續（08-21 5/5 不重複 milestone 持續）。

- **Release 新鮮度兩極：** **`open-code-review` v1.12.1（1 天）+ `caura` plugin-v2.22.2（1 天）+ `VoiceStudio` v0.5.2（5 天）+ `zylos-core` v0.8.1（6 天）= 4/5 有 fresh release（≤7 天）**；唯一例外是 **`MiroFish` v0.1.2 已 6 個月 stale、最近 3 個 commit 都是 star-history bot**——但仍有 73k★ 累積（中文 meme 級 demo 影片：紅樓夢後 40 回推演、武大公眾意見報告）。這是 09-06 codification 的 **release-tag-inversion for mature-starved projects** 標準示範：「**star velocity 不再等於 release cadence，73k★ 可以是 6 個月沒 SemVer bump 的成熟產物**」。

- **License 3 條腿混合：** 5 個 repo 的 license 分佈剛好覆蓋主人 enterprise-lite / air-gapped downstream 的三種對位——(1) **Apache-2.0 × 2**（`open-code-review` + `caura`）：closed-source fork / 商用嵌入可直接用，是 horo-agent / horo-webui downstream 的最安全底色；(2) **MIT × 1**（`zylos-core`）：permissive、無 patent grant，但對 closed-source SaaS 嵌入無障礙；(3) **AGPL-3.0 × 2**（`VoiceStudio` + `MiroFish`）：**case-E strong copyleft**——closed-source fork / 商用嵌入需保留 source + 同一 license，主人下游整合需走「self-host + 引用」而不是 vendor 程式碼。**對位主人的 horo-agent / horo-webui：可商用且適合 fork 的佔 60%（3/5），需審視 copyleft 的佔 40%（2/5）。**

- **Domain 多樣性橫切 5 個 vertical：** 這次 5 個 repo 跨 5 個完全不同的 vertical——(1) 企業 code review（`open-code-review`）/ (2) 本機 TTS+STT+audiobook desktop（`VoiceStudio`）/ (3) 中文公開輿情 swarm simulation（`MiroFish`）/ (4) AI runtime always-on agent infrastructure（`zylos-core`）/ (5) multi-agent governed memory backend（`caura`）。**「AI 工具鏈的每個垂直都有人在開源」這個趨勢在今天的 5 個 pick 同時出現——review、voice、simulation、runtime、memory 各有一個 canonical open-source reference**。對主人 enterprise-lite 的啟示：每一條都是「**把過去一年 SaaS-only 的能力打包成 open-source + self-host**」的具體實作，可作為 horo-agent / horo-webui air-gapped 下游的 reference architecture 來源。

- **MCP-native 成為 agent infra 新基線：** 5 個 repo 裡有 **2 個（`VoiceStudio` + `caura`）明確走 MCP server expose 給 Claude Code / Codex 等 agent**，佔 40%——這個比例在 1 個月前的 14:00 picks 是 0。MCP（Model Context Protocol）已從「Anthropic 提出的 spec」變成「**任何 agent-facing infra 都應該 expose MCP server 給 Claude Code / Codex 呼叫**」的 de facto 標準；對主人 enterprise-lite 主軸意味著：horo-agent / horo-webui 若要對位這個新基線，需要在 air-gapped runtime 內建 MCP server stub 而不是只做 web REST API——這是 09-06 + 09-12 + 09-15 三週累積觀察到的「agent 介面層 canonical 化」訊號。