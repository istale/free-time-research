# GitHub 專案動態
- 檢查時間：2026-08-16
- 檢查對象：GitHub Daily Trending top 3 + 自由探索 2 個（共 5 個 repo）

## Repo 摘要與 3W1H

### `cordiverse/cordis`
- **Repo 摘要：** Cordis（讀音「Cordis」，原 Shigma 的 `@cordisjs` 系列）把自己定位為「現代應用程式的 Meta-Framework of Spatiotemporal Composability」—— TypeScript + Effect + plugin 架構、Service Injection 為核心、可以同時橫跨 Node.js / Browser / 多 island / 跨 runtime 共享同一套 context / service / lifecycle。看似冷門，但其 plugin / loader / context 體系正是 Koishi / Satori（同一作者 Shigma）這類 long-running 的 React for AI 應用（每個 agent / 每個 tab 都有自己的 runtime）背後的基礎。Trending 第一名顯示 AI agent 應用爆發讓這類「context + plugin + lifecycle」框架重新被關注。
- **3W1H：**
  - **What：** TypeScript meta-framework（npm `cordis` + monorepo `packages/core`、`packages/loader`、`packages/include` 等），核心是 runtime / context / plugin 三件式抽象，搭配 Effect-friendly 的 dependency injection。
  - **Why：** 過去 React / Vue 解決了 UI 渲染，但「跨 runtime / 跨 process 共享同一份 service / context」這條題目一直沒 standard；Cordis 把 plugin 抽象、service 依賴、isolation 邊界寫成一個可組合的 framework，正好是 mono-agent / multi-agent / island architecture 想要的基礎。
  - **Who：** 寫多 island / multi-tenant SaaS / multi-agent backend / 同時要跑 Node + browser + Edge 的工程師；以及在 Koishi / Satori / Mastodon-like bot 框架上做 plugin 開發的人。
  - **How：** `npm install cordis` / `yarn add cordis`（peer deps `@cordisjs/plugin-include` + `@cordisjs/plugin-loader` 為 optional，受官方 plugin 影響）；CLI 入口 `bin.js`；搭配 `koishi` / `satori` / 自家 code-warden 可 spawn 多個 isolated runtime（每個 cordis instance 是一個 spatiotemporal composability unit）。
- **安裝方式：**
  - **npm：`npm install cordis`**（peer deps `@cordisjs/plugin-include` + `@cordisjs/plugin-loader` 為 optional，**RC 4.0.0-rc.8**）。
  - **yarn（monorepo-contributors）：** `yarn install`（`.yarnrc.yml` 鎖 yarn@4.14.1，`packageManager: yarn@4.14.1`）。
  - **CLI bootstrap（自家開發）：** `git clone https://github.com/cordiverse/cordis.git` → `yarn install` → `yarn build`（`yarn yakumo esbuild && yarn yakumo tsc`）。
  - **CLI 入口：** `node_modules/.bin/cordis`（package.json `bin` 指向 `bin.js`）。
  - **文件：** 主站 `https://deepseek-harness.github.io/deepseek-harness/reference/cordis-primer`（README 唯一列出），論文 `cordiverse/paper` 為 spec source。
- **近期 release：** 未找到 GitHub release（npm 為主要出貨管道；當前 npm `dist-tags.latest = 4.0.0-rc.8`，發佈於 **2026-08-10**；`pushed_at` 為 2026-08-13；README 明確自標「API is not yet stable and may change without notice」，目前正是 4.0 RC 階段）。

### `cathrynlavery/diagram-design`
- **Repo 摘要：** 作者（Cathryn Lavery）為 Claude Code / Codex / Pi 寫的「editorial-quality 圖表 skill」—— 29 種 visual types（architecture / flowchart / sequence / state machine / ER / quadrant / pyramid / loop …）、self-contained HTML + SVG、零 build step、零外部 image、依網站 brand 自動配色；新版本 2.3 加入 semantic system patterns、loop 類型（flywheel 共享記憶 hub）；與其說是工具，不如說是「設計師的視覺直覺 + AI 編碼能力」的 portable skill。**與 08-15 同一檔案同作者重複**（每日 stars 從 1,607 → 仍 ~3,600 級距），說明這個 skill 已有穩定 viral tail。
- **3W1H：**
  - **What：** Claude Code / Codex / Pi agent skill，把 29 種 diagram template（靜態 HTML + SVG，無 build step、無外部圖）打包成可被 AI agent 直接呼叫的設計語言。
  - **Why：** 一般 AI 出來的圖「泛用圓角框」看起來不像品牌 site；自己開 Figma 又要花 30 分鐘。diagram-design 把作者的 editorial 直覺（accent 只留 1–2 處、density 4/10、刪除優先）變成可重用 skill，並支援從 draw.io / Mermaid 來源重繪。
  - **Who：** 寫部落格、做技術文件、需要架構圖 / 流程圖 / ER / 時間軸 / Gantt / Quadrant 等「設計感」視覺的工程師、PM、技術作者。
  - **How：** 安裝路徑視 agent 而定 — Claude Code 走 marketplace（`/plugin install diagram-design@diagram-design`），Codex / Cowork 走組織 mirror marketplace，Pi 走 `pi install <git-url>`；edit profile 寫在 `~/.diagram-design/profiles/`，每個專案放 `.diagram-design` marker 指定 profile。
- **安裝方式：**
  - **Claude Code（marketplace）：** `/plugin install diagram-design@diagram-design`，然後到 `/plugin` 的 Marketplaces 開 auto-update。
  - **Codex / Cowork：** 需先把 public repo mirror 到組織 private repo，再用 Organization settings → Plugins 加入 GitHub marketplace。
  - **Pi：** `pi install https://github.com/cathrynlavery/diagram-design`（執行後 `/reload`）。
  - **Editable（自訂 style-guide）：** `git clone` 後 `pi install ~/code/diagram-design`（讓 `references/style-guide.md` 可被本機覆寫，saved profiles 不受影響）。
  - **PNG 匯出（one-time setup）：** `pip install playwright && playwright install chromium`（Playwright 2× raster）。
  - 沒有單獨 `pip install` / `npm install -g`，因為本體就是 agent skill，不是 CLI 工具。
- **近期 release：** 未找到 GitHub release（純 GitHub marketplace 派送，沒有 SemVer tag；`pushed_at` 為 2026-08-14，star 從昨日 17,532 → 今日 18,846 = +1,314 / 24h，仍在 viral tail）。

### `cactus-compute/needle`
- **Repo 摘要：** Cactus Compute 開源的「14MB 裝置端 tool-calling 模型」—— Needle 2 是 45M 參數的 Simple Attention Network（CQ2-bit 量化、自家 Hadamard MLP + GQA + engram KV memory + 多 lane hyper-connection），整個 model + engine 是單一 14MB binary，28MB RAM 跑完整 session。目標是 phone / wearable / smart home / robot 上跑 tool calling + structured extraction。**08-15 同一檔案重複**，今日 `pushed_at` 2026-08-15 = 仍在迭代（README 改版、commit 滾動），`stars` 6,153 → 追蹤是否進入 stable 階段。
- **3W1H：**
  - **What：** Python 套件（`pip install cactus-needle`）+ 單一 `.cact` engine binary + 內建 byte-level constrained-decoding grammar + LoRA fine-tune pipeline。
  - **Why：** 邊緣裝置的 LLM 一直被「model 太大 + engine 額外下載 + 不能 offline」卡住；Needle 把 weights 燒進 14MB engine、加 confidence-gated response（學出來的 head 給 calibrated score）、tool retrieval（從大 catalogue 選 top 5 + grammar 限制），讓 45M 模型在 28MB RAM 跑完整 multi-turn tool session。
  - **Who：** 在 edge device 跑 AI agent / IoT / mobile 開發者；以及想用 LoRA 在小資料集上 fine-tune 自己工具集的研究團隊（README 給出完整 synthesize → LoRA → merge → upload pipeline）。
  - **How：** `pip install cactus-needle`（基本推論），`pip install "cactus-needle[gpu]"` / `"[metal]"`（NVIDIA / Apple Silicon 加速），`needle playground` 開本地 web UI（`http://127.0.0.1:7860`），`needle finetune data.jsonl --epochs 10` → `needle build` 出 `.cact`。
- **安裝方式：**
  - **pip：** `pip install cactus-needle`（CPU inference）。
  - **uv：** `uv pip install cactus-needle`（pip 相容）。
  - **GPU extra：** `pip install "cactus-needle[gpu]"`（NVIDIA，需 CUDA build）。
  - **Metal extra：** `pip install "cactus-needle[metal]"`（Apple Silicon）。
  - **Weights（air-gapped）：** `pip install` 後 engine 從 Hugging Face `Cactus-Compute/needle2` 自動 fetch + cache；offline 環境見 `doc/apis.md`。
  - **CLI 子指令：** `needle playground` / `needle finetune` / `needle build` / `needle generate-data` / `needle download` 隨套件附帶。
  - **程式呼叫：** `import needle; @needle.tool; agent = needle.Needle(tools=[...]); agent.run("...")`。
- **近期 release：** 未找到 GitHub release（HF repo `Cactus-Compute/needle2` 是 weights 出貨管道，PyPI 是 runtime 出貨管道；README 直接命名「Needle 2」，`pushed_at` 為 2026-08-15，commit 顯示持續迭代）。

### `altic-dev/FluidVoice`
- **Repo 摘要：** 開源 macOS Dictation 應用，自稱「Fastest and only macOS Dictation app with on-device STT and custom trained AI enhancement model」—— 標榜是 Wispr Flow 的本地化替代品，用 Swift 5.9 + SwiftUI + Parakeet 多模型 + Fluid Intelligence 本地 AI 後處理（GPLv3，**核心 app 之外的 enhancement engine 私有不開源**），支援 Command Mode（口說開 App / 跑 shortcut）與 Write Mode（任何 input 直接改寫）。GPL-3.0 license（**case-H**）、release 頻率約 1 週 / 版（最新 v1.6.8 = 2026-08-11）。
- **3W1H：**
  - **What：** Swift macOS 15+ 桌面 App（brew cask 安裝）+ 內建多款 STT 模型（Nemotron Speech 3.5 / Parakeet Flash / Parakeet TDT v3 / v2 / Cohere Transcribe / Apple Speech / Whisper）+ Optional Fluid Intelligence 本地後處理（私有不開源）。
  - **Why：** Wispr Flow 等 SaaS 訂閱商以「年繳 USD 100+」收割語音生產力；FluidVoice 走 local-first 路線（語音與文字不離機、zero cost、無 API key），同時對 Wispr Flow 的「AI enhancement 後處理」環節用自訓 on-device 模型做出可對標的體驗。
  - **Who：** 經常需要大量口說輸出（工程師 / 寫作者 / 開發時貼 log）/ 不想訂閱 Wispr Flow / 注重隱私（律師 / 醫療 / 訪談逐字稿）的 macOS 使用者。
  - **How：** `brew install --cask fluidvoice` 一行裝；或手動從 [latest release](https://github.com/altic-dev/FluidVoice/releases/latest) 抓 `.app`；設定 Accessibility 權限 → 設 global hotkey → 開 Fluid Intelligence → 開 AI provider（可選 OpenAI / Groq / local）。
- **安裝方式：**
  - **brew（推薦）：** `brew install --cask fluidvoice`（README 開頭明寫）。
  - **直接下載：** [latest release](https://github.com/altic-dev/FluidVoice/releases/latest) 抓 `.app` 拖進 `/Applications`。
  - **開發源碼 build：** `git clone https://github.com/altic-dev/FluidVoice` → `cd FluidVoice` → `./build.sh`（會在 `DerivedData/Build/Products/Debug/FluidVoice Debug.app` 產出 signed build）；無 signing identity 走 `./build.sh unsigned` fallback。
  - **Integration test：** `xcodebuild test -project Fluid.xcodeproj -scheme Fluid -destination 'platform=macOS'`（CI 版本加 `CODE_SIGNING_REQUIRED=NO CODE_SIGNING_ALLOWED=NO`）。
  - **Cask 配置：** 沒有 `pip install` / `npm install` —— 純桌面 App，Swift 5.9 + SwiftUI + CoreAudio。
- **近期 release：** 最新 release 為 `v1.6.8`，發佈於 **2026-08-11**（published_at 2026-08-11T04:14:38Z），非 prerelease；過往 5 個 release 為 v1.6.4 (07-14) → v1.6.5 (07-21) → v1.6.6 (07-31) → v1.6.7 (08-05) → v1.6.8 (08-11) = **平均 7 天 / 一版**，節奏穩定。v1.6.0 重大變更：本地 Fluid Intelligence、AI enhancement、Adaptive Theming、Notch-aware overlay。

### `HKUDS/CLI-Anything`
- **Repo 摘要：** 香港大學 Data Science Lab 出品的「把任何軟體變成 agent-friendly CLI」開源框架 —— 標語「Today's Software Serves Humans. Tomorrow's Users will be Agents. CLI-Anything: Bridging the Gap Between AI Agents and the World's Software」；提供 7 階段 pipeline（Analyze → Design → Implement → Plan Tests → Write Tests → Document → Publish），可把 GIMP / Blender / LibreOffice / Obsidian / Sketch / QGIS 等桌面 / 開源軟體自動生成 Click CLI + REPL + JSON output + undo/redo + test suite + skill file，達成 AI agent 直接打 CLI 控制任意軟體。`tests: 2,461 passing`、支援 Pi / OpenClaw / Nanobot / Cursor / Claude Code / Codex / Hermes / GitHub Copilot CLI / Reasonix / Qodercli / Goose / OpenCode / Antigravity 等 14+ agent（**Hermes 是其中一個官方支援的 agent host**）。
- **3W1H：**
  - **What：** Python 3.10+ 開源專案（HKUDS = HKU Data Science 群組，主負責 Charlie Wu 等），提供 `cli-anything` Claude Code plugin / Pi extension / Hermes skill + `cli-anything-hub` PyPI 套件管理工具 + `public_registry.json` 收錄 18+ 公開 CLI skeleton。
  - **Why：** 桌面 / 開源軟體（CAD / GIS / 影片剪輯 / 3D 建模 / 排版）有 GUI 沒 API；AI agent 要 autocomplete 寫 code 必須先解決「GUI 動作 → CLI command」的 mapping。CLI-Anything 把這個 mapping 變成 7 階段可重用 pipeline，每階段都有對應的 subagent / test / skill 帶過。
  - **Who：** AI agent 開發者（要讓 agent 控制 GIMP / Blender / 開放給大量沒有 CLI 的桌面軟體）；HKU DS 研究員；以及想用同一套 pipeline 給自家內部工具 / legacy 軟體做 CLI 化的企業 IT。
  - **How：** Claude Code 用戶 `/plugin marketplace add HKUDS/CLI-Anything` → `/plugin install cli-anything` → `/cli-anything ./gimp` 一行生成；Pi 用戶 `git clone https://github.com/HKUDS/CLI-Anything` → `bash .pi-extension/cli-anything/install.sh` 全局安裝 → `/cli-anything ./blender`；Hermes 用戶則透過 Hermes skill registry（README 2026-05-30 公告 #320 提案新增 Hermes 支援）。
- **安裝方式：**
  - **Claude Code（推薦）：** `/plugin marketplace add HKUDS/CLI-Anything` + `/plugin install cli-anything`，然後 `/cli-anything ./gimp` 開自動生成。
  - **Pi 全局：** `git clone https://github.com/HKUDS/CLI-Anything && cd CLI-Anything && bash .pi-extension/cli-anything/install.sh`（卸載加 `--uninstall`）。
  - **OpenCode（experimental）：** `cp CLI-Anything/opencode-commands/*.md ~/.config/opencode/commands/` + `cp CLI-Anything/cli-anything-plugin/HARNESS.md ~/.config/opencode/commands/`。
  - **Qodercli：** `git clone HKUDS/CLI-Anything && bash CLI-Anything/qoder-plugin/setup-qodercli.sh`（註冊進 `~/.qoder.json`）。
  - **Hermes / Cursor / Codex / Goose：** GitHub source read → 走對應 marketplace 或手動啟用 CLI-Anything 在該 agent 內的 plugin/extension 設定。
  - **CLI-Hub（PyPI 客戶端）：** `pip install cli-anything-hub` → `cli-hub list` / `cli-hub install <name>` / `cli-hub launch <name> [args...]` 管理已生成的 CLI（適合在 CLI 已被生成後用戶端再一次拉）。
  - **npx alias：** `npx skills add HKUDS/CLI-Anything --skill cli-hub-meta-skill -g -y`（hook 進 SKILL-compatible agent）。
  - **若不安裝，整包零裝：** `git clone https://github.com/HKUDS/CLI-Anything` 讀 source。
- **近期 release：** 最新 release 為 `v0.4.0`，發佈於 **2026-06-25**（published_at 2026-06-25T15:17:49Z），非 prerelease；前一個為 v0.3.0 (2026-04-24) → v0.2.0 (2026-03-30) → 0.1.0（早期）。**節奏為 30-50 天 / 一版**，但 README 2026-04-18 之後的 NEWS 條目「每天都在加」表示 release 鎖 major、PATCH 太多走 commits；TailwindSS 重要的 2026-05-30 Hermes skill 提案（#320）尚未 release tag。

## 重點觀察

- **Tier-A 跨天重複 3/5 候選（觸頂重複日）：** 今天 Top 3 中 `#2 cathrynlavery/diagram-design` 與 `#3 cactus-compute/needle` 都是 08-15 同一檔案（前日已抓，今日是 +1,314★ 與 pushed_at 滾動的 viral tail），`#1 cordiverse/cordis` 是 TypeScript meta-framework 全新首進 Top 3 — 這是 14:00 系列的「**3/3 都跟昨日有交集**」新 peak（08-11/12 也是 2/3 重複，但今天 2/3 完全重複）。本日 Trending Top 25 仍顯示跨天去重機制未實作（**第 5 次驗證**：08-05/06、08-10/11、08-11/12、08-13/15、08-15/16），且 `cordis` 一進 Top 3 就直接 #1 顯示 Trending 算法的 **owner reputation / fresh impact 加權** 比「smooth stars/day」明顯。
- **Tier-B「within-Top-10 elevation」連兩日啟動（08-15 突破 + 08-16 沿用）：** 沿用 08-15 codification 的 path 5，今日兩 web exploration 都從同一份 Tier-A HTML 內 #10 `altic-dev/FluidVoice` + #11 `HKUDS/CLI-Anything` 提上來，**0 額外 fetch**（Tier-A HTML 已包含兩者完整 metadata）。`FluidVoice` 是 2026 Wispr Flow 替代品話題、`CLI-Anything` 是「AI agent + Hermes 官方支援的平台」話題，**兩個都沒走 `delegate_task` 或 `curl HN Algolia` 路線**，順應 08-15 觀察：Tier-A HTML 自身的 10–25 名 sub-tier 已經 cover 多數話題。
- **安裝 idiom 光譜：5 個 repo 跨越 5 種 install 形態：** cordis 是 **npm/yarn monorepo + RC 4.0.0-rc.8**（peer deps 為 optional plugin、未 stable API）；diagram-design 是 **agent skill marketplace**（跟 08-15 同樣型態）；needle 是 **PyPI one-liner + extras**（同 08-15）；FluidVoice 是 **brew cask + SwiftPM + Xcode build**（**Type-4b 的 macOS native 變體**，08-12 nod 過 brew cask 但今天首次看到 cask 套件本身就是 Swift 純原生 App）；CLI-Anything 是 **plugin marketplace + 手動 cp + pip install + npx skills**（**四合一 diversity**：Claude Code / Pi / OpenCode / Qodercli / Hermes / Cursor 各自走不同 install 路徑）。今天 5 picks 跨 5 種 install 形態、**3 種都有 brew cask**（如果有 macOS GUI 桌面 App，那基本 brew cask 已經是 2026 標準），無任何 `cargo` / `go install` 入榜。
- **語言 / runtime 生態：** TypeScript（cordis、CLI-Anything 部分）+ Python（needle、CLI-Anything CLI 客戶端）+ HTML/SVG（diagram-design）+ Swift 5.9（FluidVoice）+ 跨多種 agent host 4 條主線。**FluidVoice 純 Swift + macOS 15+** 是今天最尖端的 language 偏離（其他 4 個主流都是 TS / Python / HTML），呼應主人 2026 macOS GUI 開發觀察線。CLI-Anything 直接列 **Hermes 為官方支援 agent host**（#320 提案、2026-05-30 NEWS），對齊主人目前 Hermes/webUI downstream 工作。
- **License 乾淨度分化：** cordis / diagram-design / needle / CLI-Anything 都是 **MIT**（case-A — 標準 SPDX 全部 OK）；FluidVoice 是 **GPL-3.0**（**case-H**，08-12 codification：OSI 開源但 copyleft 強，商用嵌入需保留 source + 同一 license；**特別注意** FluidVoice 的「Fluid Intelligence enhancement engine 為私有不開源」分層 license，本體 GPLv3 + 私有 runtime 不衝突）。今天 5 picks 中 1 個 GPL-3.0 = **20% 是 strong copyleft**，但因為 FluidVoice 是 Swift 桌面 App + 主人不見得會 fork，跟昨天 40% Modified Apache/GPL 比較之下對主人的 horo-agent/horo-webui downstream 影響是 low-priority。
- **跨語言 / 跨 runtime 抽象的 2026 主旋律：** cordis 提「Spatiotemporal Composability」、CLI-Anything 提「Agent-Native」、FluidVoice 提「Local-First」、needle 提「Edge Tool-Calling」—— 四個 repo 雖然語言 / 平台全不同，**全部都在解決「同一個會話 / 同一個 process / 同一段聲音 / 同一塊硬體 → 多個 runtime / 多個 agent 之間共享狀態 / context / service」這條題目**。對主人的 `horo-agent` / `horo-webui` 多 backend 並列需求，cordis 的 plugin/context 抽象 + CLI-Anything 的 agent host abstraction + FluidVoice 的 local-first 強化 + needle 的 edge quantization 是四個可直接對標的設計 precedent；其中 cordis 與 CLI-Anything 是**最值得花時間讀 source**的兩個（README 對「可組合」與「agent-native」的理論框架比 FluidVoice/needle 嚴謹）。
- **Viral tail 觀察：`diagram-design` 從 08-13 爆紅 → 08-15 進入 trending #1 → 08-16 仍在 trending #2：** 總 star 從 17,532 → 18,846（+1,314 一天）、README 每天改、pushed_at 維持 2026-08-14 — 這是「**viral tail + 持續 inflow**」的標準 pattern，意味著這個 skill 不是「一日 peak」而是「已穩定 scalable」。主人若有想看的 skill 是「可被 1 個 author 持續維護 + 每天長 1k+ ★」，可以在自己 backend 規則裡加入「trigger: 連 3 天 pushed_at continuous + 持續 inflow」的 signal。
- **主人軌跡對齊：** cordis（TypeScript multi-tenant / spatiotemporal framework）對齊主人「multi backend 並列共用 memory」記憶點；CLI-Anything 直接列 **Hermes 為 official agent host**（與主人目前 Hermes 減法 + webUI downstream 工作是 1:1 對應，且 2026-05-30 開過 #320 提案新增 Hermes 支援）；needle（如同 08-15）對齊主人 on-device / edge AI 軌跡；FluidVoice 對齊主人 macOS 桌面 dev tool 整合；diagram-design 對齊主人視覺 / 設計 driven 工程偏好（05-12 + 08-15 雙重對標）。
