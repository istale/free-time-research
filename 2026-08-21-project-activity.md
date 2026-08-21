# GitHub 專案動態
- 檢查時間：2026-08-21（14:00 台北時間）
- 檢查對象：mattpocock/skills / AprilNEA/OpenLogi / santifer/career-ops / CopilotKit/OpenBot / littledivy/dgit
- 來源組合：GitHub Trending today（Tier-A 3）+ delegate_task constrained search（Tier-B 2，5th verified path 1）

## 動態摘要

### mattpocock/skills
- 類型：release
- 內容：trending top 2，2,192 stars today；最新 release `v1.2.3`（2026-08-06）；227k★ 累計、MIT、Shell scaffold。Personal agent skills library by Matt Pocock（Total TypeScript 作者）。
- 連結：https://github.com/mattpocock/skills/releases/tag/v1.2.3

### AprilNEA/OpenLogi
- 類型：release
- 內容：trending top 3，1,545 stars today；最新 release `v0.7.3`（2026-08-20，今日剛發）；12.1k★ 累計、Apache-2.0、Rust + GPUI。
- 連結：https://github.com/AprilNEA/OpenLogi/releases/tag/v0.7.3

### santifer/career-ops
- 類型：release
- 內容：trending top 6，816 stars today；最新 release `career-ops-v1.28.0`（2026-08-20，今日剛發）；JavaScript、MIT。CLI-agnostic AI 求職 pipeline，跑在 Claude Code / OpenCode / Codex / Antigravity / Grok / Qwen / Kimi / Copilot 等 8+ agent 上。
- 連結：https://github.com/santifer/career-ops/releases/tag/career-ops-v1.28.0

### CopilotKit/OpenBot
- 類型：release / repo activity
- 內容：Tier-B web 探索 1，delegate_task 推薦、HTTP-200 驗證。最新 release `v0.0.1`（2026-08-17，4 天內 alpha 起手）；1,771★ 累計、MIT、TypeScript + Bun + PostgreSQL。從 AG-UI protocol 切入的 agent governance 平台。
- 連結：https://github.com/CopilotKit/OpenBot/releases/tag/v0.0.1

### littledivy/dgit
- 類型：trending / repo activity（release 尚未發）
- 內容：Tier-B web 探索 2，delegate_task 推薦、HTTP-200 驗證。181★ 累計、MIT、TypeScript。**未找到 GitHub release**；2026-08-18 創建（3 天內新品），pushed_at 2026-08-20 仍動；Cloudflare Workers + Durable Objects 上跑的 git server。
- 連結：https://github.com/littledivy/dgit

## 重點觀察
- **Tier-A cross-day repeat 0/3 — 模型＋安裝範式都自然旋轉**：今日 trending 前 3 排除 7 天內 5 個重複（MoneyPrinterTurbo、OpenViking、obra/superpowers、munder-difflin、akitaonrails/ai-memory）後，自然浮出 `mattpocock/skills`（personal skills library）＋ `AprilNEA/OpenLogi`（Rust HID++ 鍵鼠 remap tool）＋ `santifer/career-ops`（CLI-agnostic AI 求職 pipeline）三個 — 跟昨天（08-20）的 Chinese-audience AI 工具熱潮（MoneyPrinterTurbo）轉到了「developer infra ＋ hardware-adjacent ＋ CLI agent skill library」三個 indie-developer 軸向。Domain 多樣性是本月新高。
- **release 新鮮度 4/5 = 80%**：v1.2.3（8 天內）、v0.7.3（今天）、v1.28.0（今天）、v0.0.1（4 天內）— 唯一沒有 release 的是 `littledivy/dgit`（創建才 3 天，第一個 commit 就走 git 本身），符合「green-field + pushed_at as freshness signal」的 08-20 框架。
- **2 個新品 release license 乾淨度 5/5 = 100% permissive**：4 MIT ＋ 1 Apache-2.0（AprilNEA/OpenLogi = case-A2 含 patent grant clause）；這個全 permissive 狀態跟 08-20「4/5 permissive」、08-17「5/5 permissive」形成 3 連 milestone — 對主人 horo-agent / horo-webui air-gapped 下游嵌入而言，這週 3 個 tick 內 0 license friction。
- **3 套 install 範式並存 — 沒有任何兩個重複**：mattpocock/skills（Type-20 plugin marketplace + `npx skills@latest add` + Claude Code plugin）；OpenLogi（Type-4b2 brew cask + `.dmg` / `.deb` / `.rpm` / NixOS module / Windows MSI 多 platform installer matrix）；career-ops（Type-1 `npx @santifer/career-ops init` + `npm i -g` 雙路徑）；OpenBot（Type-6 mix：Bun + Docker Compose + `bun run dev` / `bash scripts/start.sh` 一鍵 stack）；dgit（Type-2 `npm install` + `npx wrangler deploy` + export-published `durable-git` library）。5 個 5 種裝法 — 對應 5 個不同的「2026 install 範式軸」。
- **Hermes-as-official-agent-host 訊號：今天沒直接命中，但 install 矩陣大量指向 agent host**：career-ops README 列出 Claude Code / OpenCode / Codex / Antigravity / Grok / Qwen / Kimi / Copilot 8 個 agent host 為一級支援；mattpocock/skills README 把 Claude Code + Codex + 其他 agent 並列為 install 軸；OpenBot 走 AG-UI protocol 抽象、文件沒列特定主機。**跟前 3 天（08-16/17/20）出現的「Hermes 是 official host」訊號相比，今天 5 個 repo 沒有任何一個把 Hermes 列為 install 目標** — 這是 08-20 hinsight 提到的 3-consecutive-tick MEMORY peak 後第一次「沒直接命中」，但市場面訊號沒消失（career-ops 跟 mattpocock/skills 的 install 矩陣只是「還沒列 Hermes」，而不是「拒絕 Hermes」）；值得主人留意的轉變是「agent host 主流化從少數支援轉到全列」，未來 community 端可能會再回到這個 1-2 個 agent host 標 Hero badge 的格局。

## Repo 摘要與 3W1H

### `mattpocock/skills`
- **Repo 摘要：** Matt Pocock（Total TypeScript 作者，227k★ 累計）的個人 coding agent skills library — 30 秒裝進 Claude Code / Codex / 其他 agent，定位是「small, easy to adapt, composable」。Skill 對應 4 大失敗模式：溝通不良（`/grill-me` / `/grill-with-docs`）、廢話太多（CONTEXT.md shared language）、code 沒在跑（`/tdd` red-green-refactor）、變成 big ball of mud（`/to-spec` + `/improve-code-architecture`）。同日好幾個 adjacent 庫（obra/superpowers、spec-kit、hikariBAE/claude-eng-stack）都搶同一塊「agent-native SDLC」市場，這份走「個人工程的 smallest unit」路線。
- **3W1H：**
  - **What：** 個人 curation 的 agent skills library — 30 秒裝、composable、可 fork、可 hack；不綁特定 framework，跟的任何 agent 都吃。
  - **Why：** 解決「agent 寫 code 不到位」的 4 大失效模式（misalignment / verbosity / no feedback / entropy）；mattpocock 個人 using 60,000+ 訂閱者會直接 receive updates，且每個 skill 是「decades of engineering experience」壓縮，不是 framework 強加的流程。2,192 stars today 主要是作者電郵訂閱基數 + skills.sh 列表推廣。
  - **Who：** 用 Claude Code / Codex 的 developer，要「specific failure-mode coverage」而不是「framework SDLC」；想用 Matt Pocock 自己常用的 skill 拼裝自己的 coding workflow；偏好「subscribe / fork」而不是「framework own your process」。
  - **How：** 兩路徑擇一，(a) Claude Code plugin (`claude plugins install mattpocock-skills` 或 session 內 `/plugin install mattpocock-skills`) 是 read-only 訂閱、自動更新；(b) `npx skills@latest add mattpocock/skills` 拷貝可編輯檔案進自己的 repo、`npx skills update` 拉新版。裝完每個 repo 跑一次 `/setup-matt-pocock-skills` 設定 issue tracker / labels / docs 位置。
- **安裝方式：**
  - **npx**：`npx skills@latest add mattpocock/skills` — 標準 agentskills.io install 路徑，自動偵測目前 agent host；對應 `npx skills update` 拉新版。可編輯。
  - **Claude Code plugin**：`claude plugins install mattpocock-skills`（CLI 一次性）或 session 內 `/plugin install mattpocock-skills` — 走官方 Claude Code plugin marketplace，read-only、作者 push 自動收到；同時裝兩種會重複，只能選一個。
  - **入內後**：每個 repo 跑一次 `/setup-matt-pocock-skills` 設定 issue tracker / labels / docs 位置。
- **近期 release：** `v1.2.3` — 2026-08-06 發佈（14 天內），pre-release = false；release name 與 tag 同名。Repo `pushed_at` 2026-08-20 昨日還在動，227k★ 累計；Homepage = `https://aihero.dev/skills`。License = MIT（純 permissive）。

### `AprilNEA/OpenLogi`
- **Repo 摘要：** 開源 Logitech Options+ 替代品 — Rust + GPUI 寫的 native 桌面 app，跨 macOS / Linux / Windows 三大平台，講「local-first，no account, no telemetry」，主攻 Logi Bolt / Unifying / Bluetooth receiver 的 HID++ 鍵鼠 + UVC 攝影機控制。Linux 是一級平台（Options+ 對 Linux 不友善這點是痛點）。已有 `.dmg` notarized / `.deb` / `.rpm` / `.pkg.tar.zst` / NixOS module / Homebrew cask 完整 installer matrix；KDE D-Bus / X11 怎麼切平台都有細節。
- **3W1H：**
  - **What：** 跨平台 Rust 桌面 app — GPUI 為 UI、HID++ 跟 Logi Bolt receiver 對話、UVC 跟 Brio / StreamCam / C920 攝影機對話；TOML 單一 config 跨機 sync、per-app profile 跟著 app 焦點自動切。
  - **Why：** 解決「Logi Options+ 把資料送到雲、Linux 沒支援、佔資源、不開源」的全套痛點；今天能跟 Linux 一級對齊是獨門差異化（其他 Rust 鍵鼠工具沒跨平台怎麼齊）。今日 1,545 stars today 主要是 Twitter / Telegram 推廣 + 對應真實硬體 MX Master / Litra 玩家社群。
  - **Who：** 用 Logi MX Master / MX Anywhere / Litra / Brio 等裝置的 power user；Linux + 鍵鼠細節控；想要單一 TOML config 跨平台同步、不想被 lock-in。
  - **How：** macOS 抓 notarized `.dmg` 拖進 `/Applications` 或 `brew install --cask openlogi`；Linux 依 distro 抓 `.deb` / `.rpm` / `.pkg.tar.zst` 裝，udev rules 自動給非 root 的 hidraw / uinput 存取；NixOS 走 `inputs.openlogi` flake 模組；裝完 `systemctl --user enable --now openlogi-agent.service`。
- **安裝方式：**
  - **brew**：`brew install --cask openlogi`（官方 cask 預設）；要直接追 GitHub release 用 `brew tap aprilnea/tap && brew install --cask aprilnea/tap/openlogi@latest`，兩種不能並裝。
  - **macOS**：從 [latest release](https://github.com/AprilNEA/OpenLogi/releases/latest) 抓 notarized `.dmg`，拖 `OpenLogi.app` 進 `/Applications`。需 macOS 13+。
  - **Linux**：`sudo dpkg -i openlogi_*.deb`（Debian/Ubuntu）/ `sudo rpm -i openlogi-*.rpm`（Fedora/RHEL）/ `sudo pacman -U openlogi-*.pkg.tar.zst`（Arch）；同時供 `x86_64`/`amd64` 與 `arm64`/`aarch64`。
  - **NixOS**：把 `inputs.openlogi = { url = "github:AprilNEA/OpenLogi"; inputs.nixpkgs.follows = "nixpkgs"; }` 加進 flake，再用 `openlogi.nixosModules.default` 加到 system modules，`programs.openlogi.enable = true`。
  - **Systemd 啟動**：裝完 `systemctl --user enable --now openlogi-agent.service`；packages 自動裝 udev rules（hidraw / uinput / your mouse 的 input event 不需 sudo）。
  - **重要**：要先 quit Logi Options+，不然 HID++ 會 fight over receiver。
- **近期 release：** `v0.7.3` — 2026-08-20 發佈（昨天剛發，今日 trending 熱潮即發），pre-release = false；release name 與 tag 同名。README 第一行 WARNING 明示「OpenLogi is under active development and not yet stable — features and config may still change」。Repo `pushed_at` 2026-08-21 今日仍動。License = Apache-2.0（case-A2 — 含 patent grant clause），Homepage = `https://openlogi.org`。

### `santifer/career-ops`
- **Repo 摘要：** CLI-agnostic AI 求職 command center — 任何 AI coding CLI（Claude Code / OpenCode / Codex / Antigravity / Grok / Qwen / Kimi / Copilot）開進來 `/career-ops` 指令就變成「JD 評分 → ATS 客製 CV → portal scan → 公司 deep research → 聯絡人 → 申請 email draft → tracker」。Human-in-the-loop 設計（AI 評估建議、你決定送出），100+ 公司預設 portal（Anthropic、OpenAI、ElevenLabs、Retool...），Playwright 生成 ATS PDF，Go + Bubble Tea TUI dashboard。Santiago Fernández 自己用它 740+ offers 試到最終 landing Head of Applied AI。
- **3W1H：**
  - **What：** Job-search command center — 多 agent CLI 共用 skills 架構（`.agents/skills/career-ops/SKILL.md` 對應各 CLI 符號連結）、Settings + tracking + PDF generation + portal scanner all-in-one；A-F 加 G 共 7 blocks 評分（block G 為 posting-legitimacy 篩 ghost job）。
  - **Why：** 解決「AI 已經被 employer 拿去篩 candidate，那 candidate 能不能用 AI 反篩 employer」的痛點；Wired / Business Insider 報導此案，README 開宗明義說「not a spray-and-pray tool, 是一個 filter」— 系統強烈建議 < 4.0/5.0 不值得投。CareerOps Manifesto 還把這個姿態寫成 commitment。
  - **Who：** 想用 AI coding CLI 做 systematic 求職的 engineer / PM / SA / FDE；對 Universal Time Efficiency 有 sense 的人；想用「同一套 system 跨多 CLI」而不是被 lock-in 的人。
  - **How：** `npx @santifer/career-ops init`（推薦首次用）clone 最新 release 到 `./career-ops`、裝 deps；`cd career-ops && claude`（或 codex / qwen / opencode / agy / grok）開 AI CLI；CLI 自動讀 `AGENTS.md` + skill 結構 — 首次開會用 chat 進 onboarding 設 CV / profile / target。`npm i -g @santifer/career-ops` 拿到全域 `career-ops` 指令。`/career-ops` 子命令路由評分 / PDF / scan / cover / email / tracker / batch / pipeline / contacto / deep。
- **安裝方式：**
  - **npx 一次性 init（推薦首次）**：`npx @santifer/career-ops init` — `npm install` 包在裡面，clone 最新 release 到 `./career-ops`、裝 Playwright Chromium（PDF 用）；內建 `npm run doctor` 驗證 prerequisites。
  - **全域 CLI**：`npm i -g @santifer/career-ops` — 拿到 `career-ops` 指令可以一直用，較適合建立 project folder 之後。
  - **手動 git clone**：`git clone https://github.com/santifer/career-ops.git && cd career-ops && npm install && npx playwright install chromium`（僅 PDF 生成需要）→ `npm run doctor` → `cp config/profile.example.yml config/profile.yml`（編輯）→ `cp templates/portals.example.yml portals.yml`（客製化）→ 在 root 建 `cv.md`（你的 CV）→ `claude` 開 CLI → 在 CLI 說「請把 archetype 改成 backend engineering」之類。
  - **Antigravity CLI**（Google 已將 consumer Gemini 併入）：`cd career-ops && agy`，然後 `/career-ops pipeline` 走 subcommand；`GEMINI.md` 已退化成 no-op 相容 guard 防 AGENTS.md 跟 GEMINI.md 雙重讀。
  - **Codex**：slash command 不一定穩，用 plain language 觸發（「Run the career-ops pipeline mode for data/pipeline.md」），或 `codex exec "..."`。其他 CLI（OpenCode / Kimi / Qwen / Copilot / Grok）各自走 share skill entrypoint，README 列 8 個 badges。
  - **免費或本地模型**：`docs/RUNNING_ON_A_BUDGET.md` 寫 OpenRouter free / Ollama / 任何 OpenAI-compatible endpoint 接入；`docs/FREE_TIER.md` 寫 Antigravity CLI free tier 零成本跑。
- **近期 release：** `career-ops-v1.28.0` — 2026-08-20 發佈（昨天剛發），pre-release = false；release name = `career-ops: v1.28.0`。Repo `pushed_at` 2026-08-21 今日仍動，66.9k★ 累計，816 stars today。License = MIT（純 permissive、case-A），含 Trademark Policy（`TRADEMARK.md` 規範名稱「career-ops」的使用），對下游品牌有分離建議。

### `CopilotKit/OpenBot`
- **Repo 摘要：** Agent governance runtime — 給每個 AI agent 自己的「電腦」（隔離 container、自己的 Chromium、自己 `/workspace` volume、自己 logins），用一個 gateway 統一 decide-before-act + audit-after（CEL policy 引擎，失敗會 fail closed）。Multi-coworker 設計：每個 Bot 一個 AG-UI endpoint，protocol 抽象所以 LangGraph / Mastra / CrewAI / Pydantic AI / Google ADK / hand-written 都吃。Docker Compose 一拉就 launch，PostgreSQL + pgvector 存 audit / policy / credentials，Bun 跑 app + API 層。設計哲學：「agent 能用工具」跟「agent 值得被允許用工具」是兩件事，這層在管後者。
- **3W1H：**
  - **What：** Agent runtime + governance platform — single gateway 統一 verify browser / file / shell / MCP / UI component 動作、CEL policy 拒絕先驗、audit row 先寫再開行動作；UI 是個 React/Vite multi-channel web app，路由：channels / agents / admin/computers / admin/boundaries / admin/audit / admin/playground。
  - **Why：** 解決「企業要 deploy AI coworker 但不想把整台機器交給它」的 trust 與 audit 問題；4 天內 1,771★ 走 AG-UI 標準的 agent 社群 + CopilotKit 既有客戶基數。alpha 標但 security workflow 跟 zizmor 全綠 — 顯示 security 為 first concern。
  - **Who：** Enterprise team 想 deploy agent coworker 但必須滿足 SOC2 / ISO 27001 類 audit 要求；CopilotKit 既有客戶（已有 Intelligence license）；想用 AG-UI protocol 框架中立 / 自寫 agent endpoint 的開發者。
  - **How：** `cp .env.example .env` → `npx --yes copilotkit@latest login && npx copilotkit@latest project select && npx copilotkit@latest license --write` 拿到 `cpk-...` Intelligence runtime key，並寫 `COPILOTKIT_LICENSE_TOKEN` 進 `.env` → 填 `OPENAI_API_KEY` 與 `KEY_ENCRYPTION_KEY`（`openssl rand -base64 32`）→ `bun install && bash scripts/start.sh`（不只 build，還 start Docker services + apply migrations + 起 API + app + 驗 health routes）→ `http://localhost:3010` 開 UI；生產後 `docker build -t openbot .` + `docker run -p 3001:3001 --env-file .env -e EMBEDDED_POSTGRES=on -v openbot-data:/var/lib/postgresql/data openbot`。
- **安裝方式：**
  - **npx 設定 CopilotKit Intelligence**：`npx --yes copilotkit@latest login` → `npx copilotkit@latest project select` → `npx copilotkit@latest license --write`（會寫 `INTELLIGENCE_API_KEY` + `COPILOTKIT_LICENSE_TOKEN` 進 `.env`）。
  - **Bun / Docker 跑開發版**：`bun install` → `bash scripts/start.sh`（全 stack：Docker services + migrations + API on port 3001 + app on port 3010 + health route 驗證）。
  - **Docker 單一 image 部署**：`docker build -t openbot .` → `docker run -p 3001:3001 --env-file .env -e EMBEDDED_POSTGRES=on -v openbot-data:/var/lib/postgresql/data openbot`；要接外部 PostgreSQL 就把 `EMBEDDED_POSTGRES` 留 off、`DATABASE_URL` 指過去。
  - **gVisor 隔離 Bot computers**：`COMPUTER_RUNTIME=runsc`（host 有支援時）；Chromium 自身 sandbox：`COMPUTER_SANDBOX=on`。
  - **OAuth / SAML / OIDC**：Google / Microsoft / Okta 任一啟用就轉 sign-in；多個同時開啟 sign-in 頁面並列。SAML / OIDC 走 runtime register：admin 登入後 Admin → Identity providers 加 metadata，由 email domain 路由。
  - **Dev loop**：`bun run format:check && bun run lint && bun run typecheck && bun run test && bun run build`；改 Drizzle schema 後 `bun run --filter server db:generate && bun run --filter server db:migrate`。
- **近期 release：** `v0.0.1` — 2026-08-17 發佈（4 天內 alpha 起手），pre-release = false；release name 與 tag 同名。README 開頭標 alpha 與 `status alpha orange badge`，但 `ci.yml` + `security_zizmor.yml` 全綠 + LICENSE 檔 + docs 完整。1,771★ 累計（4 天內累積、發展很快）。License = MIT（純 permissive）。

### `littledivy/dgit`
- **Repo 摘要：** 跑在 Cloudflare Workers + Durable Objects 上的 bare-metal git server — TypeScript 從 scratch 實作 pkt-line framing / packfile parser / SHA-1 / Myers diff（單一 dep = pako for zlib），每個 repo 是一個 Durable Object（自帶 SQLite 存 objects + refs），web 走 cgit-style UI（log / tree / blob / blame / tar.gz / zip / atom feed）。Self-host 走 `celld` 把同個 Worker 跑在自己 bucket、突破 Workers 128MB / 5min 限制。README 對外宣告「PR disabled」直接改成 `git format-patch` 寄 email，態度復古。
- **3W1H：**
  - **What：** Git server（durable git）+ cgit-style web UI — TypeScript 從 wire protocol 寫到底、可 library 用也可 deployed 用、Workers 與 celld 兩種 runtime 模式可選。
  - **Why：** 解決「想要 GitHub-style host 但不想被 GitHub 鎖、不想自己架 GitLab / cgit」；Durable Objects 1 repo 1 cell 天然隔離 hot repo 拖累 cold repo；3 天內 181★ 走 Deno SQLite / deno_postgres 同一作者 littledvy 的邊緣運算圈名氣。`PACK_CACHE` R2 綁定後 packbytes 離開 SQLite，full clone 從 Worker 直接 stream。
  - **Who：** 想做 edge-deployed git-based 服務（如：個人 blog repo 倉庫、agent 寫 code 的版本控制、企業 code review 工作流管線）的 developer；littledvy 或 Deno 邊緣生態開發者；對 GitHub 平台 lock-in 警覺的研究者。
  - **How：** `npm install`（單一 dep = pako）→ `npx wrangler r2 bucket create dgit-pack-cache`（optional，加 pack offload + clone cache）→ `npx wrangler deploy` → `npx wrangler secret put GIT_TOKEN`（push 密碼）→ `git remote add origin https://<your-host>/myrepo.git && git push -u origin main` 開始用。也可以當 library 用：`npm install durable-git` 後 `import { createDurableGit, secretsEqual } from "durable-git"` 寫個 3 行 wrapper 就能 deploy 自己的授權 / onPush 邏輯。
- **安裝方式：**
  - **Cloudflare Workers（官方推薦）**：`npm install` → `npx wrangler r2 bucket create dgit-pack-cache`（可選，建議加）→ `npx wrangler deploy` → `npx wrangler secret put GIT_TOKEN`。Workers 128MB / 5min 限制內 day-to-day push/clone/fetch 都舒服；超大 history 走 series-of-smaller-pushes 路徑，已 cached clone 從 R2 stream 回 Worker 不 load cell。
  - **Self-host on celld**（突破 Workers 限制）：`celld deploy wrangler.celld.jsonc --bucket s3://my-cells --endpoint https://...` → `CELLD_V8_HEAP_LIMIT_MB=4096 CELLD_LTX_DURABILITY_TIMEOUT_SECS=180 celld --bucket s3://my-cells --endpoint https://... --listen 0.0.0.0:8080 --internal-listen 10.0.0.1:8081 --advertise 10.0.0.1:8081`（先在 `wrangler.celld.jsonc` 設真實 `GIT_TOKEN` var）。
  - **當 library**：`npm install durable-git` → `import { createDurableGit, secretsEqual } from "durable-git"`；自訂 `authorize({ repo, op, private, credentials, env })` 取代 stock `GIT_TOKEN` policy；自訂 `onPush(event, env)` 觸發 CI hook；bundled by wrangler from TypeScript source。
  - **Operations**：`curl -X PUT -u x:$GIT_TOKEN -d '{"description":"...","section":"tools","private":false}' https://<host>/myrepo/config`（描述並放置）→ `curl -X POST -u x:$GIT_TOKEN https://<host>/myrepo/gc`（prune）→ `curl -X DELETE -u x:$GIT_TOKEN https://<host>/myrepo`（刪除）。
  - **Tuneable**：`GIT_TOKENS`（多個 comma-separated token）/ `MAX_PUSH_MB`（單次 push 上限）/ `SHA1DC=1`（每個 pushed object 過 SHA-1 collision attack 檢查，預設用 native SHA-1）。
- **近期 release：** **未找到 GitHub release** — repo 2026-08-18 創建、4 天內、目前無 GitHub Releases 頁（`/releases/latest` 404）；`pushed_at` 2026-08-20 仍動，commits 持續進。181★ 累計、8 forks、0 open issues、268KB repo（小、單一 dep）。License = MIT（純 permissive），Homepage = `git.littledivy.com`，README 註明「Pull requests disabled. Send a `git format-patch` attachment to me@littledivy.com」 — 作者刻意走 git 本身做 PR 流程，態度復古。
