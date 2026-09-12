---
title: GitHub 自由探索 2026-09-12（14:00 台北時間）
date: 2026-09-12
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 7 天 repeat）
  - web_search 當日 AI 開源新聞（Tier-B web 探索 2）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md` + `LICENSE` + `docs/install.md`
---

# GitHub 專案動態

- 檢查時間：2026-09-12（14:00 台北時間）
- 檢查對象：`nab138/iloader` / `melgarafael/DeskcommCRM` / `p1neappleXpress/OpenFlux` / `use-agent-os/agent-os` / `virgiliojr94/book-to-skill`
- 來源組合：GitHub Trending today 共解析 16 筆，**排除前 7 天已寫過的 repeat**（rank 1 `ayghri/i-have-adhd`（09-09）、rank 2 `bilawalsidhu/gods-eye-view`（09-11）、rank 7 `alsk1992/CloddsBot`（09-11）、rank 9 `obra/superpowers`（09-04 + 09-09）、rank 11 `jihe520/MathModelAgent`（09-08）、rank 5 `vastsa/PI-Desktop`（09-05）、rank 15 `github/spec-kit`（08-15）、rank 16 `pascalorg/editor`（09-08）；跳過 rank 6 `armory3d/armorpaint`（3D 繪圖工具，與 AI agent 路線無交集）與 rank 10 `Sonarr/Sonarr`（經典專案重上 Trending 而非當週新案）），**fresh top 3 = rank 3 `nab138/iloader` + rank 4 `melgarafael/DeskcommCRM` + rank 12 `p1neappleXpress/OpenFlux`**（iOS 跨平台 sideload / WhatsApp 自架 AI CRM / Go pluggable-transport TCP tunnel，三個 domain 完全不重疊）。Tier-B web_search 當日 AI 開源新聞取到 **`use-agent-os/agent-os`**（Local-First Agent OS，Apache-2.0；今天在 HeroHermes 系列文出現，且內建 `agentos migrate hermes --apply` 直接從 `~/.hermes` 搬資料）與 **`virgiliojr94/book-to-skill`**（PDF/文件夾 → agent skill 轉換器，30k★、MIT、今天仍在 push；README 與 `docs/install.md` 明列 Hermes Agent 為一等支援 host，並提供 `${HERMES_HOME:-$HOME/.hermes}/skills/<category>/book-to-skill` 的 host-private 安裝路徑），兩者都已用 `curl https://api.github.com/repos/<slug>` 驗證 HTTP 200 + metadata 非空才寫入。

## Repo 摘要與 3W1H

### `nab138/iloader`

- **Repo 摘要：** Tauri 寫的 iOS sideloader 桌面 GUI，把 SideStore / LiveContainer + 配對檔（`.p12`、lockdown pairing）整套流程做成「插上 iPhone → 登 Apple ID → 選動作 → 自動跑完」。2,947★、TypeScript、1.1 MB、MIT、`created` 2025-11-02、`pushed_at` 2026-09-10 20:49 UTC。底下用 jkcoxson 的 Rust `idevice` crate 跟 Apple 私有 developer endpoint 對接，再用 `apple-codesign-quick` 處理 codesign/entitlements——Sideloader（Dadoum 寫的）原本只支援 macOS，iloader 把它跨平台化並把錯誤訊息變成「可讀的英文」。**注意：開發者已在 release note 宣告 v2.3.3 可能是 v2.0 最後一版**，精力轉移至 iloader-next（會有新 UI、web 版、更完整的 2FA）。
- **3W1H：**
  - **What：** Tauri (TS frontend + Rust backend) 桌面應用，發行 `.dmg` / `.exe` / `.deb` / `.rpm` + AppImage + AUR + COPR + brew cask + Nix flake——是一個會說「用什麼平台就抓什麼」的 sideloader，不是 library。
  - **Why：** iOS sideload 對一般使用者來說就是「裝 AltStore → 過七天要重簽 → 配對檔掉了要手動撈」三件煩事；iloader 把第一件壓到「插上手機、點三下」。它不是第一個 sideloader（Impactor、AltStore、SideStore 都在），**它的差異化在「跨平台 + 友善的錯誤訊息 + 配對檔管理一站化」**。
  - **Who：** 想在 iPhone / iPad 上裝非 App Store IPA 的一般使用者、jailbreak / 測試社群、SideStore / LiveContainer 使用者。**不適用於**：想給企業做大規模自動派送的人（要看 MDM 方案）、只想在 Mac 上 sideload 的人（Impactor 還比較快）。
  - **How：** GUI 為主——從 GitHub releases 抓對應平台安裝檔，安裝 `usbmuxd`（macOS 內建、Linux 看套件庫、Windows 裝 iTunes），插 iPhone，開 app，登 Apple ID，選「Install SideStore」之類的動作就會跑完。Dev 模式是 `bun i` + `bun tauri dev`（或 Node + Rust 走 `npm run tauri dev`）。每個語系的翻譯是 PR `src/i18next.ts` + `src/locales/<lang>.json`，已支援 13 種語系（含繁中／簡中／粵語）。
- **安裝方式：**
  - **未找到明確 pip/npm 套件安裝方式**（沒有發佈到 npm registry，README 也明示「此 repo 與 iloader.app 為唯二官方下載點」，其他來源都不要裝）。
  - 一鍵路徑：到 <https://github.com/nab138/iloader/releases> 抓對應平台安裝檔（macOS 走 `.dmg` / Homebrew Cask，Windows 走 `.exe`，Linux 走 `.deb` / `.rpm` / AppImage）。
  - 套件管理：macOS `brew install --cask iloader`、Arch `yay -S iloader-bin`（AUR）、Fedora `dnf copr enable anudeepd/iloader && dnf install iloader`、NixOS 用 flake `github:nab138/iloader`。
  - 開發路徑：需 bun（或 Node.js） + Rust，`git clone` → `bun i` → `bun tauri dev` 熱重載；production `bun tauri build`。
  - **License：** MIT（標準 SPDX，LICENSE 檔案頭乾淨）。
- **近期 release：** **v2.3.3 — "Final v2 update — iloader-next incoming"，published 2026-09-10 20:49 UTC（2 天前）**。release note 明示：「這很可能會是 iloader v2.0.0 的最後一版更新，除非冒出其他重大問題；開發時間已轉移到 iloader-next，會帶來新 UI、web 版、改良的 2FA」。換言之 **v2.x 線已收尾**，下一個值得看的會是 iloader-next。

### `melgarafael/DeskcommCRM`

- **Repo 摘要：** 葡語社群主導、以「WhatsApp-first AI sales OS」自我定位的自架 CRM。提供 docker-compose 一鍵安裝、內建 WAHA（WhatsApp HTTP API）、native AI agent、RAG、multi-tenant、LGPD（巴西個資法）合規、MCP-ready，README 自詡為 Kommo / Octadesk / Intercom 的開源替代品。1,452★、TypeScript、125 MB、MIT、`created` 2026-04-28、`pushed_at` 2026-09-12 00:08 UTC（= 今天台北 08:08 仍在 push）。今天登上 Trending 是因為 release v1.19.0 推出「WhatsApp 語音通話」（預設關閉、有真正的關閉按鈕）。
- **3W1H：**
  - **What：** Next.js + Supabase + WAHA + 多 docker container（app / worker / scheduler）的 self-host CRM SaaS 原始碼，不是 library、不是單機 app。
  - **Why：** 巴西（與葡語圈）中小企業「用 WhatsApp 做生意」是常態，但 Kommo / Octadesk / Intercom 是月費 SaaS，且對 AI agent 與 self-host 幾乎沒支援。DeskcommCRM 把整套做成 docker-compose + 一支 install.sh 安裝腳本，搭配原生 AI agent、RAG 知識庫、WhatsApp 訊息／語音／bot 三種接觸管道，讓一家小店能在 4 GB RAM 的 VPS 上跑出「Sales OS」。
  - **Who：** 葡語圈中小型電商、WhatsApp-first 客服團隊、想自架 AI CRM 且需要 LGPD 合規的開發者、研究 self-host AI CRM 架構的人。**不適用於**：英文-only 場景（雖然 README 同時存在但內容主軸葡語）、需要大量自訂欄位的複雜 CRM 場景（這種通常還是走 Salesforce / HubSpot 商業版）。
  - **How：** 兩條路徑——(1) end-user：VPS（HostGator 巴西合作方案或任一 4 GB+ Docker VPS）→ `bash -c "$(curl ...)"` 跑 install.sh，自動建 WAHA / Supabase / RAG pipeline / 三條 Docker image（app / worker / scheduler）；(2) developer：`npm install -g pnpm && pnpm install && docker compose up -d`（啟 WAHA 在本機）→ `pnpm dev`。測試分四層：verify / build-and-size / invariants / e2e / imagens-ok，CI 卡 PR 合併。
- **安裝方式：**
  - **未找到明確 pip/npm 套件安裝方式**（沒有發佈到 npm registry，整包是 self-host Docker image）。
  - end-user 主路徑（README 的「## ⚡ Instalar na sua VPS」）：`bash -c "$(curl -fsSL https://raw.githubusercontent.com/melgarafael/DeskcommCRM/main/install.sh)"`（README 給的具體指令碼前綴，install.sh 內含 `docker compose up`、`supabase db push`、建 schema baseline、注入 seed）。腳本會自動偵測缺 Docker、缺 git 並問要不要裝。
  - developer：`git clone` → `npm install -g pnpm && pnpm install` → `docker compose up -d`（啟 WAHA）→ `pnpm dev`。測試：`pnpm test:db`（需 Docker，invariants job）、`pnpm imagens-ok`（建三條 image 的必跑檢查）。
  - 自架底層是 Supabase（用 baseline.sql 而非 migrations——README 解釋 migrations 0001-0009 / 0013 是 stubs，schema 都在 baseline）。**注意**：新 Supabase 專案要先啟用 schema 用到的 extension，否則 baseline 會停在 `type public.vector does not exist`。
  - **License：** MIT（標準 SPDX，LICENSE 檔案頭乾淨）。但要 self-host 商業使用，仍須遵守 WAHA、Supabase 自身條款。
- **近期 release：** **v1.19.0 — "Chamada de voz pelo WhatsApp — desligada por padrão, e com botão de desligar de verdade"（WhatsApp 語音通話 — 預設關閉，且有真正的關閉按鈕），published 2026-09-11 22:51 UTC（約 15 小時前）**。release note 第一條直接強調「更新不會自動啟用語音通話，沒有任何號碼被連上任何新服務」，這是一個明確的「你裝了不等於被開通」設計——把 opt-in 留給使用者，避免被平台誤判為自動外呼機器人。

### `p1neappleXpress/OpenFlux`

- **Repo 摘要：** Go 寫的 pluggable-transport TCP tunnel，client 是 SOCKS5 proxy，把封包塞進「合法應用層 protocol」（Yandex Docs cursor messages、Max Messenger 的 WebRTC DataChannel）當 cover，exit node 解封包轉送。1,207★、Go、30 MB、**GPL-3.0**、`created` 2026-06-21、`pushed_at` 2026-09-11 22:35 UTC。配套有獨立的 Android APK（OpenFluxAndroid）與 iOS SwiftUI + Network Extension VPN app（TestFlight 公開 beta），作者已寫滿 disclaimer——「**不鼓勵用於繞過平台限制；非商業、無付費功能、無訂閱**」。0.0.1 是**昨天（09-10）才發的第一個 release**。
- **3W1H：**
  - **What：** 一個 library + 兩種 binary（desktop CLI + 行動 app）的網路研究專案，主 binary 是 universal-bypass-tool（同時是 client 與 exit node），不是 SaaS。
  - **Why：** pluggable transport 是 Tor 圈子用了十年的概念（將封包偽裝成其他 protocol 來對抗 DPI），但 Tor 的 obfs4 / meek / snowflake 都鎖定在特定應用層協定的歷史版本。OpenFlux 走一條「**用 2026 年還活著的雲端 SaaS 協定當 cover**」的路（Yandex Docs、MAX Messenger），並把 client 端做成可嵌入 Android / iOS 的 library（iOS 端甚至有完整 SwiftUI app + VPN extension）。
  - **Who：** 研究 pluggable transport / 對抗式網路協定的工程師、tor / v2ray / sing-box 生態的開發者。**不適用於**：想找「乾淨的 reverse tunnel / ngrok 替代」的人（這專案的設計目標不同）；想拿來商用繞過 GFW 的團隊——除了 GPL-3.0 條款不允許 closed-source fork，README 的 disclaimer 也明示作者不鼓勵。
  - **How：** 桌面 client：`go mod tidy && go build -o universal-bypass-tool .` 編出同時是 client / exit node 的 binary；iOS 走 `build_ios.sh`（產 `liboflux.a`）+ `build_ios_app.sh`（archive + export IPA）+ Xcode 26.6+；Android 走 `build_android.sh` + Android NDK 27.0.12077973+。需要一條外部 VPS 跑 exit node。**整個 stack 沒有 npm / pip / brew / docker image 的發行，是純 source build**。
- **安裝方式：**
  - **未找到明確 pip/npm 套件安裝方式**（沒有任何 registry 發行，無 docker image、無 brew formula、無預編 binary）。
  - desktop / exit-node binary：`git clone` → `go mod tidy` → `go build -o universal-bypass-tool .`，需 Go 1.26.3+（要求很新，比 Go 1.23 / 1.24 都晚）。
  - Android client：設 `ANDROID_NDK_HOME` → `./build_android.sh`，需 Android NDK 27.0.12077973+。
  - iOS client：設 `XCODE_PATH`（可選，預設 `/Applications/Xcode.app`）→ `./build_ios.sh` → `./build_ios_app.sh`，需 Xcode 26.6+。
  - **License：GPL-3.0（case-H）**——**OSI 開源但 copyleft 強**，對主人 `horo-agent` / `horo-webui` air-gapped 下游（含 closed-source fork / 商用嵌入）會是 **structural prohibition**：GPL-3.0 程式碼本身可商用、閉源 fork 也可以使用原版 binary，但**一旦修改並發佈，就必須整個衍生作品以 GPL-3.0-or-later 重發**。要商用嵌入必須通盤評估。
- **近期 release：** **0.0.1 — "rel 0.0.1"（首版 release，無 body），published 2026-09-10 14:04 UTC（2 天前）**。tag 是 `0.0.1`、name 也是 `rel 0.0.1`、body 為空——這是 fresh release，**1,207★ 的關注度是來自於 pluggable-transport 路線的網路社群而非 SemVer 成熟度**。

### `use-agent-os/agent-os`

- **Repo 摘要：** Local-First Agent OS——一個想當「agent 的作業系統」的自架框架。Pilot Router（ONNX Runtime on-device）自動選最便宜模型、20+ provider（OpenRouter / Bankr LLM Gateway / OpenCAP / Surplus Intelligence / OpenAI / Anthropic / Ollama / DeepSeek / Gemini / DashScope / Moonshot / Mistral / Groq / Zhipu / SiliconFlow / vLLM / LM Studio…）、本地持久記憶、多層 sandbox、內建 web search、on-device embeddings。53★、Python、26 MB、**Apache-2.0**、`created` 2026-07-13（2 個月新專案）、commits 1,056（驚人活躍）、`pushed_at` 2026-09-11 06:33 UTC。今天登上 Trending 周榜（4,005 stars this week / 上週榜單）部分原因是**官方有 `agentos migrate hermes` 指令——直接從 `~/.hermes` 搬設定與記憶**。
- **3W1H：**
  - **What：** Python wheel + Windows portable zip + source checkout 三種發行方式的 agent runtime（`agentos` CLI + `<http://127.0.0.1:18791/control/>` Web UI），不是 library、不是 SaaS。
  - **Why：** 想讓「一個 agent 框架」跟 OS 一樣平常——任何 provider、任何 channel（CLI / Web UI / Telegram / Discord…）共用同一個 core loop、同一份 log、同一份 token budget。Pilot Router 的具體設計是「先用便宜的模型處理、能做就不升級」，這是 2026 下半年 token economics 主旋律的具體實作。**對主人特別相關**：`agentos migrate openclaw,hermes --apply` 直接吃 `~/.openclaw` 與 `~/.hermes` 兩個目錄——**官方把 Hermes Agent 視為相容 runtime，而不是對手**。
  - **Who：** 想要「自架、token-efficient、provider-agnostic agent OS」的進階使用者、研究 agent runtime 的人、想從 Hermes Agent / OpenClaw 跳槽但又不想丟資料的人。**不適用於**：只想做「一次性的對話 UI」的人（這種直接上 Open WebUI / Dify 比較快）、需要企業 SSO + RBAC 完整方案的團隊（這個專案定位是 OS 不是 enterprise platform）。
  - **How：** 主推四種 install 路徑，依「要不要 Python / 要不要改 source」分流——(1) Windows portable（zip 自帶 Python）、(2) Quick terminal（`uv tool install`，所有 OS 統一）、(3) Install from source（git clone 但不編輯）、(4) Develop from source（contributor）。`agentos onboard` 走 wizard；`agentos migrate hermes --apply` 從 Hermes Agent 搬資料。`agentos_router.strategy = "pilot-v1"`（預設，本地 ONNX 模型評估）vs `"llm_judge"`（用小型 LLM call 判斷，無本地模型檔）。
- **安裝方式：**
  - **pip/uv（官方主推，跨 OS 統一）：** `uv tool install --python 3.12 "use-agent-os[recommended]"`——安裝 PyPI 上的最新 release，自動拉 ONNX Runtime / NumPy / tokenizers 等 Pilot Router 依賴。**`uv` 是必備前置**：Linux / macOS 走 `curl -LsSf https://astral.sh/uv/install.sh | sh`；Windows PowerShell 走 `irm https://astral.sh/uv/install.ps1 | iex`。要關閉 Pilot Router 安裝但保留 CLI：設 `AGENTOS_INSTALL_PROFILE=core` 或加 `--router disabled` flag。
  - **PyPI wheel（手動）：** 直接從 <https://github.com/use-agent-os/agent-os/releases/latest/download/use_agent_os-2026.9.x-py3-none-any.whl> 下載 wheel 檔用 `uv` 安裝。
  - **Windows portable（無 Python）：** 下載 <https://github.com/use-agent-os/agent-os/releases/latest/download/AgentOS-windows-x64-portable.zip> → 解壓 → 右鍵 `Start AgentOS.cmd` → 用管理員身分執行（preview build 沒簽章，會被 SmartScreen 擋，要點「More info → Run anyway」）→ 開 <http://127.0.0.1:18791/control/>。**注意**：portable launcher 不會把 `agentos` 指令裝進 global PATH。
  - **源碼：** `git clone` → `git lfs pull`（沒有 LFS bundle Pilot Router 會降級為「所有 turn 都路由到 c1 tier」而非 crash）→ `uv sync` → `uv run agentos`。
  - **License：Apache-2.0（case-A2）**——標準 SPDX，LICENSE 檔案頭乾淨，且 Apache-2.0 自帶專利授權條款，對主人 horo-agent / horo-webui air-gapped downstream 是**比 MIT 更優的 permissive license**。
- **近期 release：** **v2026.9.11 — "AgentOS 2026.9.11"，published 2026-09-11 03:44 UTC（約 34 小時前）**。release note 標題是「contributor-fix release」，合併 18 個社群 PR + 一個小型 web UI 介面調整，**主軸是「消失的回覆 / tool call / transcript / wake signal」——所有環節都回報 fine 但實際上沒送出去**的 silent-loss 修復。前 5 個 release 的日期：2026.9.11（昨日）/ 9.10（09-09）/ 9.9（09-08）/ 9.7（09-07）/ 9.6（09-06）——**幾乎每天一版**，是 5 個 repo 裡 release cadence 最密集的（且每次都對應到真實的 silent-loss 修復）。

### `virgiliojr94/book-to-skill`

- **Repo 摘要：** 把任何技術書／文件夾／PDF 轉成「agent skill」的轉換器。執行 `/book-to-skill your-book.pdf` 後會在 user-level cross-agent skills 目錄（`~/.agents/skills/<slug>/`）生成一份 `SKILL.md` + 章節索引 + 萃取後的全文 Markdown，**讓 Claude Code / GitHub Copilot CLI / Amp / Codex / Hermes Agent 都能讀同一份 skill**。30,128★（註：3,128 forks、今天仍在 push；GitHub Weekly Trending 列出 5,223 stars this week）、Python、2.5 MB、MIT、`created` 2026-05-01、`pushed_at` 2026-09-12 03:00 UTC（= 今天台北 11:00 仍在 push）。`docs/install.md` 對 Hermes Agent 寫得特別細，列出 `${HERMES_HOME:-$HOME/.hermes}/skills/<category>/book-to-skill` 的 host-private 安裝路徑與 `hermes skills trust` 的專案信任流程。
- **3W1H：**
  - **What：** 一個 agent skill（SKILL.md + `scripts/extract.py` + `tools/`）+ 一個 standalone CLI（`pip install book-to-skill` 取得純文字萃取引擎，但**不會**註冊 agent skill）。
  - **Why：** agent skill 的 SHIP-ready 內容永遠比 prompt 設計更稀缺——LLM context window 再大，也沒人能記住《Designing Data-Intensive Applications》或《Crafting Interpreters》整本。book-to-skill 把 PDF / Markdown / reStructuredText / AsciiDoc / 程式碼 / 掃描檔（用 Docling）→ 章節化、可 grep、可注入 context 的 skill 庫。
  - **Who：** 任何需要把「反覆翻的書／RFC／spec／內部 design doc」變成 agent 可查閱知識庫的人、agent host 開發者、研究 SKILL.md 標準在多 host 互通的人。**不適用於**：只想做「摘要一段長文」的人（這種直接 LLM 摘要就好，不需要做 skill 化）。
  - **How：** 走 `npx skills add virgiliojr94/book-to-skill`（跨 host 統一 CLI）自動偵測所有 host 的 skills 目錄並寫入；或對單一 host 用 `git clone` 到該 host 的 skills 目錄。Hermes 多了 host-private 路徑：`git clone https://github.com/virgiliojr94/book-to-skill.git "${HERMES_HOME:-$HOME/.hermes}/skills/productivity/book-to-skill"`，且專案層級需要 `hermes skills trust /path/to/project` 才會載入。轉換後的 skill 預設放 `~/.agents/skills/<slug>/`，生成後 `/your-book-slug replication` 指令就會在對話中讀該書對應章節作答。
- **安裝方式：**
  - **npx（官方主推，跨 host 一鍵）：** `npx skills add virgiliojr94/book-to-skill`——`skills` CLI 自動偵測 host、寫入對應 skills 目錄。
  - **Hermes Agent（host-private）：** `git clone https://github.com/virgiliojr94/book-to-skill.git "${HERMES_HOME:-$HOME/.hermes}/skills/productivity/book-to-skill"`。`HERMES_HOME` 是 profile-aware，預設 `~/.hermes`。轉換後生成的「書本 skill」要放在對應 subject 的 category，不要自動放在 `productivity`。
  - **Hermes Agent（專案層級）：** `git clone` 到 `.hermes/skills/<category>/book-to-skill` + `hermes skills trust /path/to/project` + `hermes skills list` 確認。**Hermes 不會載入專案層級 skill 直到那個專案被 trust 過**。
  - **GitHub Copilot CLI：** `git clone https://github.com/virgiliojr94/book-to-skill.git ~/.copilot/skills/book-to-skill`（或走 cross-agent path `~/.agents/skills/book-to-skill`）→ 在 copilot session 內 `/skills reload` + `/skills info book-to-skill`。
  - **OpenAI Codex：** 讀 `~/.agents/skills` 並 follow symlink，可直接 `git clone` 或 `ln -s /path/to/book-to-skill ~/.agents/skills/book-to-skill`。
  - **Claude Code：** 在 session 內貼上 `Install book-to-skill: https://raw.githubusercontent.com/virgiliojr94/book-to-skill/master/...`（`docs/install.md` 給完整 prompt）。
  - **standalone CLI（純文字萃取，無 agent 註冊）：** `pip install book-to-skill`（從 repo install）→ `book-to-skill --help`。**重要：這只裝文字萃取引擎，不會自動註冊成任何 agent 的 slash command**。
  - **License：MIT（標準 SPDX）**。
- **近期 release：** **v1.4.0 — "Extraction reliability release: six fixes for silent content loss or silent wrong answers, plus the first changelog generated from commit messages."，published 2026-08-10 19:06 UTC（33 天前）**。注意 GH release tag 停在 33 天前，但 `pushed_at` 是今天、weekly stars 5,223、commits 持續在推——這是典型的「**tag 跟不上 push + 內容更新主要落在 `src/` 與 `docs/` 而非版本號**」的 agent-skill 專案模式（09-11 `gods-eye-view` 也有同樣 pattern）。release 內容是 6 個 silent content loss / silent wrong answer 修復，例如「掃描版 PDF 以前會跑完整 Docling technical mode，現在會 fail-fast」。

---

## 重點觀察

- **Hermes-as-official-agent-host 訊號今天在 Tier-B 兩個 repo 同時點燃——這是本週最強的 MEMORY signal。** `use-agent-os/agent-os` README 提供 `agentos migrate hermes --apply`（吃 `~/.hermes`）+ `agentos migrate --source openclaw,hermes --apply`（Hermes + OpenClaw 同時搬）+ 在「## 任何 OS 的純粹 OS-style 體驗」一段把 [Hermes Agent](https://github.com/NousResearch/hermes-agent) 列為相關連結；`virgiliojr94/book-to-skill` 不只在 README 與 `docs/install.md` 把 Hermes Agent 列為一等支援 host，還針對 Hermes 寫出 host-private 路徑 `${HERMES_HOME:-$HOME/.hermes}/skills/<category>/book-to-skill` + `hermes skills trust` 的專案信任流程——是今日 5 個 repo 裡**對 Hermes 相容性寫得最細的一個**。Tier-A 三個（iloader / DeskcommCRM / OpenFlux）都 0 Hermes mention。**2/5 MEMORY hit-rate 重新把今日拉成「permissive + Hermes-friendly」結構**，但這兩個 MEMORY-hit 都在 Tier-B、來自 web_search 撈到的 2026 下半年新興 agent runtime 敘事，而非 Trending 上的社群共識。
- **Tier-A 跨日 repeat：fresh 3/3（與 09-11 的 0/3 形成鏡像）。** 今天 fresh top 3 完全沒有前 7 天寫過的——`iloader` / `DeskcommCRM` / `OpenFlux` 三個 domain 完全不重疊（iOS 桌面 sideload / WhatsApp self-host CRM / Go pluggable-transport TCP tunnel）。**這是 09-11 hit 0/3 之後的連續 rotation**，符合 09-06 開始建立的「0/3 = natural floor when yesterday's viral tail decays」模式；但今天不是「0/3 因為昨天沒選」，而是**「3/3 fresh 因為今天 Trending 完全換血」**——`ayghri/i-have-adhd` 與 `gods-eye-view` 都還在 top 10 內但被刻意跳過。換言之這是刻意維持多樣性的 forced rotation，跟被動 decay 不同；報告讀者應該看到的是「今天 Tier-A 主動選擇了三個完全不同主題」，不是「今天 Trending 自然衰退」。
- **Release 新鮮度與 push cadence 脫鉤，5/5 都在 9 月內 push 但只有 4/5 有 GH release。** `agent-os` 是 5 個裡 release cadence 最密的（過去 11 天內發 5 版，幾乎每天一版），`iloader` v2.3.3（2 天前）明確宣告**v2.x 線收尾**，`DeskcommCRM` v1.19.0（約 15 小時前）剛推出 WhatsApp 語音通話，`OpenFlux` 0.0.1 是**2 天前才發的首版 release**，`book-to-skill` v1.4.0 已 33 天前、但 `pushed_at` 是今天。**兩個極端對比**：`agent-os` 的 release 日期與 push 日期完美同步（甚至 release 更頻繁）；`book-to-skill` 的 release 與 push 落差 33 天，且 release 內容主要在「silent content loss / silent wrong answer」這類**功能正確性**而非**新功能**——讀者要看 `pushed_at` 而不是 release tag 才能抓到最新動態。
- **License 與語言生態跨日分歧縮小，但「case-H 出現一次」。** License 4/5 標準 permissive（MIT × 3 + Apache-2.0 × 1：`agent-os` 是 case-A2，含專利授權條款，比 MIT 對下游更友善），唯一非 permissive 是 `p1neappleXpress/OpenFlux` 的 **GPL-3.0（case-H）**——**OSI 開源但 copyleft 強**，對主人 `horo-agent` / `horo-webui` air-gapped downstream 是 structural warning（closed-source fork 必須整個衍生作品以 GPL-3.0 重發）。語言分布 TypeScript 2（iloader / DeskcommCRM）+ Python 2（agent-os / book-to-skill）+ Go 1（OpenFlux），**沒有 Rust**——這是 09-11 llmfit + open-source-trending 路線轉向的訊號，但**單樣本不足以確認**，可能只是巧合。
- **對主人的實用度排序：** `agent-os` 最直接可借鑑（**官方有 Hermes migration 指令，等於承認 `~/.hermes` 為一等公民**——對主人 horo-agent / horo-webui downstream 的「另一個競爭 runtime 但願意互通」訊號，比市場上一堆「我要取代你的 agent host」敘事有實質意義）；`book-to-skill` 是 Hermes skill 生態擴充的標準入徑（轉換器的 SKILL.md 標準對所有 host 通吃，產出的 skill 也會進 `${HERMES_HOME}/skills/<category>/`，**等於主人 SOUL/skills 路徑的延伸**）；`DeskcommCRM` 對主人 horo-agent 下游無直接幫助，但**它的「WhatsApp 語音通話預設關閉」設計值得所有 agent runtime 抄**——把 opt-in 留給使用者，避免被平台誤判為自動外呼機器人；`iloader` 對 iOS 開發者有用但對主人無關；`OpenFlux` 結構上 GPL-3.0 + 設計目標是 pluggable transport 而非 reverse tunnel，不建議主人直接採用，但其「**用 2026 年還活著的雲端 SaaS 協定當 cover**」的 pluggable transport 思路在主人 air-gapped 環境下的 egress 規劃仍有參考價值。
