---
title: GitHub 自由探索 2026-09-11（14:00 台北時間）
date: 2026-09-11
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 7 天 repeat）
  - web_search 當日 AI 開源新聞（Tier-B web 探索 2）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md` + `LICENSE` + crates.io `/api/v1/crates/<pkg>` + npm `registry.npmjs.org/<pkg>` + PyPI `pypi.org/pypi/<pkg>/json`
---

# GitHub 專案動態

- 檢查時間：2026-09-11（14:00 台北時間）
- 檢查對象：`bilawalsidhu/gods-eye-view` / `alsk1992/CloddsBot` / `AlexsJones/llmfit` / `NVlabs/SoL-Pi` / `earthtojake/text-to-cad`
- 來源組合：GitHub Trending today 共解析 16 筆，**排除前 7 天已寫過的 repeat**（rank 1 `ayghri/i-have-adhd`（09-09）、rank 3 `obra/superpowers`（09-04、09-09）、rank 5 `Tencent/teamai-cli`（09-10）、rank 8 `cathrynlavery/diagram-design`（09-06/07/09）；另跳過 rank 7 `liquidslr/system-design-notes`、rank 9 `freestylefly/awesome-gpt-image-2` 兩個純筆記／提示詞庫，非可安裝軟體），**fresh top 3 = rank 2 `bilawalsidhu/gods-eye-view` + rank 4 `alsk1992/CloddsBot` + rank 6 `AlexsJones/llmfit`**（三個 domain 完全不重疊：地理空間情報 / 自主交易 agent / 本地模型選型工具）。Tier-B web_search 當日 AI 開源新聞取到 **`NVlabs/SoL-Pi`**（NVIDIA 2026-09-11 開源的 agent harness 效率擴充，HTX/marsbit 當日報導）與 **`earthtojake/text-to-cad`**（2026-09-10 登上 Trending 的 CAD/CAE/CAM agent skill 庫，AIToolly 當日報導），兩者都已用 `curl https://api.github.com/repos/<slug>` 驗證 HTTP 200 + metadata 非空才寫入。

---

## Repo 摘要與 3W1H

### `bilawalsidhu/gods-eye-view`

- **Repo 摘要：** 一個跑在瀏覽器裡的「間諜衛星模擬器」，但資料全部是真的——即時航班 transponder、船舶 AIS beacon、衛星軌道元素、地震儀、公開路況與 CCTV，全部疊在一顆 Cesium 光真實感 3D 地球上，還配了語音代理可以用講的控制鏡頭與畫註記。24,973★、JavaScript、84 MB、`created` 2026-06-22、`pushed_at` 2026-09-11 05:16 UTC（= 今天台北時間 13:16 仍在 push）。作者 Bilawal Sidhu 是 YouTube 上「God's Eye View」系列（5M+ 觀看）的創作者，2026 年 8 月曾拿下 GitHub Trending 日榜與週榜第一。適合想看「開放情報（OSINT）+ WebGL + 即時資料融合」怎麼做的人，以及想要一個不用金鑰就能開起來的地理視覺化 playground。
- **3W1H：**
  - **What：** 本地跑的 Vite + Cesium 前端應用（不是 library、不是 npm 套件），每一個資料圖層是一個獨立模組，可自行增減。
  - **Why：** 飛機、船、衛星、地震、火點這些公開訊號一直都在，但分散在十幾個 provider；這個專案把它們放進同一個空間座標系，讓人可以從「全球態勢」一路 zoom 到「某一架飛機的駕駛艙視角」。爆紅原因是 YouTube 影片 + Product Hunt #8 + Brendan Eich 轉推的社群外溢，而非開發者口碑。
  - **Who：** OSINT / 地理空間愛好者、WebGL / Cesium 開發者、想做即時資料儀表板的前端工程師、YouTube 觀眾轉來的一般使用者。
  - **How：** 兩條路徑——(1) 裝 Pinokio 8.2+，在 Pinokio 商店點 Install → Start，跨 Windows/macOS/Linux 一鍵起；(2) 終端機 `git clone` → `npm ci` → `npm run doctor` → `npm run dev`，開 `http://localhost:4173`。**完全免金鑰即可啟動**（Esri 衛星底圖 + keyless terrain），金鑰是「升級」不是前提，要照片級 3D 才需要 Cesium ion token 或 Google Maps key，而且是在 app 裡的 POWER UP 面板貼上、不用改檔案。
- **安裝方式：**
  - **未找到明確 pip/npm 套件安裝方式**（沒有發佈到 npm registry，`npm install` 只是裝本地依賴）。
  - 一鍵路徑：Pinokio 8.2 或更新版 →「God's Eye View」→ Install → Start。
  - 開發路徑：`git clone https://github.com/bilawalsidhu/gods-eye-view.git && cd gods-eye-view && npm ci && npm run doctor && npm run dev`。**Node 版本硬性要求 24.14.0+ 或 26.x**，doctor 會對 Node 25（已 EOL）發警告。
  - macOS 捷徑：`./scripts/dev-fresh.sh` 會清 Vite cache 並從 Keychain 撈金鑰（`security add-generic-password -U -s "google-maps-api" -a "api-key" -w` 之類）。
  - **License 注意（case-G 變體）：** GitHub API 顯示 `NOASSERTION`，實際 LICENSE 檔開頭是標準 MIT，但後面追加了一大段第三方資料排除條款——TeleGeography 海底電纜圖是 **CC BY-NC-SA 3.0（禁商用）**、OSM/Open Infrastructure Map 抽出的資料中心與水壩是 **ODbL 1.0（姓名標示 + 相同方式分享）**、`public/models/` 下的 3D 模型各有各的授權。商用前必須逐檔清掉或另外談授權。
- **近期 release：** **v0.1.1 — “v0.1.1 — Installation and live-data fixes”，published 2026-09-01 19:43 UTC（10 天前）**。注意 release tag 還停在 0.1.x，但今天仍在 push，**版本號完全跟不上實際開發節奏**。

### `alsk1992/CloddsBot`

- **Repo 摘要：** 自我定位「個人 AI 交易終端」——把 Claude 接上 10 個預測市場（Polymarket、Kalshi…）、7 個期貨交易所、Solana DEX（Jupiter / Pump.fun / Raydium / Orca）與 5 條 EVM 鏈，用自然語言下單、找套利、追鯨魚、跑 DCA 機器人，還能發代幣、在 Bittensor 挖 TAO。1,796★、TypeScript、12 MB、MIT、`created` 2026-01-26、`pushed_at` 2026-09-10 23:10 UTC。宣稱內建 118+ 交易策略、支援 21 種通訊平台當介面，為 Solana 的 Colosseum Agent Hackathon 在 12 天內做出來的。適合想看「agent commerce protocol（x402 機器對機器付款）+ 自主交易」長什麼樣的人——但**這是拿真錢去跑的自主 agent，風險屬性跟其他四個 repo 完全不同層級**。
- **3W1H：**
  - **What：** 自架的 Node.js CLI + gateway（`clodds` 全域指令 + `localhost:18789/webchat` 本地網頁聊天介面），不是 library。
  - **Why：** 把「prediction market / 加密永續 / 代幣發行」三塊本來各自有 SDK 的東西，收斂成一個 Claude 對話介面；再加一層 Agent Forum / Agent Marketplace / x402 付款 API，讓 agent 之間可以互相買賣訊號與策略——這是 2026 下半年「機器對機器商務」敘事的具體實作。
  - **Who：** 自營交易者、想試 agent 自動交易的加密使用者、研究 agent commerce protocol 的人。**不適合**不想把 API key 與錢包私鑰交給自主 agent 的人。
  - **How：** `npm install -g clodds` → `clodds onboard`，精靈帶你填 `ANTHROPIC_API_KEY`、選通訊頻道、啟 gateway；或 `git clone` + `npm install` + `cp .env.example .env` + `npm run build && npm start`；也有 `docker compose up --build`。
- **安裝方式：**
  - **npm（全域 CLI）：** `npm install -g clodds --loglevel=error` 然後 `clodds onboard`。`engines.node >= 22.0.0`，bin 是 `clodds -> dist/cli/index.js`，npm license 欄位 MIT。
  - 原始碼路徑：`git clone https://github.com/alsk1992/CloddsBot.git && cd CloddsBot && npm install && cp .env.example .env && npm run build && npm start`。
  - Docker：`docker compose up --build`。
  - **⚠️ 套件與原始碼嚴重脫節：** npm registry 上 `clodds` 的 `latest` 只有 **1.7.7，發佈時間 2026-02-13**，而 GitHub release 已經到 **v1.9.0（2026-08-31）**、最後一次 push 是昨天。也就是 **npm 套件落後原始碼約 7 個月**。README 主推的 `npm install -g clodds` 裝到的是舊版；要拿到 v1.9.0 的 venue audit / pump.fun rewrite，**必須走 git clone 路線**。
- **近期 release：** **v1.9.0 — “Clodds v1.9.0 - Venue audit + pump.fun rewrite”，published 2026-08-31 22:28 UTC（11 天前）**。

### `AlexsJones/llmfit`

- **Repo 摘要：** 一行指令回答「我這台機器到底跑得動哪些開源模型」。偵測 CPU 核心、系統 RAM、獨顯／內顯、VRAM、統一記憶體架構（NVIDIA CUDA / Apple Silicon / AMD ROCm / Intel OneAPI），再對照模型參數量、context 長度與量化格式（GGUF / AWQ / GPTQ / EXL2）推估記憶體佔用與 tok/s，最後給出 quality / speed / fit / context 四維評分表。35,850★、Rust、27 MB、MIT、`pushed_at` 2026-09-10 13:47 UTC。新功能是「跑真 benchmark 然後從 TUI 直接送 PR 回饋給專案」——你量到的真實 tok/s 會取代估計值，合併後隨下一版釋出，同硬體的人以後直接看到實測 `✓`。**五個 repo 裡最直接對主人這台 M4 Max VM（16 GB 統一記憶體）有用的一個。**
- **3W1H：**
  - **What：** Rust 寫的終端工具，預設是互動式 TUI，也有傳統 CLI 模式、Web Dashboard 與 REST API（`/api/v1/system`、`/api/v1/models`）。
  - **Why：** 本地跑模型最貴的成本是「下載 40 GB 才發現跑不動」。llmfit 把硬體偵測 + 量化選型 + 速度估算做成離線可查的表；再加上眾包 benchmark 回填，讓估計值逐步變成實測值。支援 Ollama、llama.cpp、MLX、Docker Model Runner、LM Studio 等 runtime provider。
  - **Who：** 本地跑 LLM 的個人使用者、要幫團隊選機器的工程師、做模型部署自動化的 SRE（REST API 就是為了塞進 orchestrator）。
  - **How：** 裝完直接跑 `llmfit` 進 TUI；自動化情境用 `llmfit recommend --use-case coding` 出 JSON 再用 `jq` 查。
- **安裝方式：**
  - **uv/pip：** `uv tool install -U llmfit`；免安裝試跑 `uvx llmfit`。（有趣之處：這是一個 **Rust 二進位包成 Python wheel 發佈**的跨生態案例。）
  - **brew：** `brew install AlexsJones/llmfit/llmfit`（預編譯 bottle，建議）或 `brew install llmfit`（homebrew-core，無 bottle 的 macOS 版本會從源碼編）。
  - 其他：Windows `scoop install llmfit`；MacPorts `port install llmfit`；`curl -fsSL https://llmfit.axjns.dev/install.sh | sh`（加 `-s -- --local` 裝到 `~/.local/bin` 免 sudo）；Docker `docker run -it --rm ghcr.io/alexsjones/llmfit --tui`；源碼 `git clone` + `cargo build --release`。
  - crates.io 上 `llmfit` 最新 1.1.15（2026-09-10 上架，累計 17,524 次下載）。二進位有 SignPath 簽章。
  - **主人硬體可行性：** macOS Apple Silicon 是第一級支援目標（統一記憶體偵測是賣點之一），`brew` 或 `uv tool install` 兩條路都可直接用，無 CUDA/平台 wheel 綁定。
- **近期 release：** **v1.1.15，published 2026-09-10 06:05 UTC（昨天）**，crates.io 同日同版本上架。近期節奏極密：1.1.11（08-25）→ 1.1.12（08-28）→ 1.1.13/1.1.14（09-03 同日兩版）→ 1.1.15（09-10）。

### `NVlabs/SoL-Pi`

- **Repo 摘要：** NVIDIA Labs 在 2026-09-02 開的新 repo，今天（09-11）被科技媒體集中報導。它是 Pi coding agent 的獨立擴充，打包了四個「透過大規模 auto-research 迴圈自動找出來」的 harness 效率機制：Action Fusion（把編輯後必跑的驗證指令併進同一次 tool call）、ObservationPack（重複出現的大型 tool 結果變成可分頁精準召回的 handle）、Evidence-Preserving Reducer（長日誌壓成收據，但每一句保留的引文都必須與封存原文逐字比對通過才生效）、Online Context Compact（已完成的計畫步驟成為壓縮候選點，且要通過經濟性與視窗壓力雙重檢查）。557★、TypeScript、26 MB、MIT、`pushed_at` 2026-09-11 05:18 UTC。**對做 agent runtime 的人來說，這是今天五個裡技術密度最高的一個。**
- **3W1H：**
  - **What：** 一個 Pi 的 plugin/extension（不 patch Pi 源碼、不 vendor Pi source tree，只用 Pi 的 public extension API），四個機制全部預設關閉、需明確 opt-in。
  - **Why：** 長跑的 coding agent 會累積重複工作——編輯後必跟一個可預測的驗證指令、大型 tool 結果在第一次用完後被反覆重放、已完成的子任務賴在 context 不走、前沿模型花整整一次請求去讀一份只有幾行有用的日誌。媒體報導的數字：相較 base Pi 省 45–49% token、API 成本降 50–54%、任務分數保留約 94%；相較 Codex / Claude Code 等其他 harness 省 35–64%。**這些是專案自報數字，尚未見第三方複現，引用時應標註 provenance。**
  - **Who：** 自己在改 agent harness 的人、要壓 token 成本的 agent 平台維運者、研究「agent 能不能先優化自己的 harness 再談 scaling」的研究者。
  - **How：** 裝好指定版本 Pi → `pi install git:github.com/NVlabs/SoL-Pi` → 在 `.pi/sol-pi.json` 或 `~/.pi/agent/sol-pi.json` 寫設定（兩層不合併，專案層覆蓋使用者層）。README 建議的保守起步只開兩個「不額外呼叫模型、不中斷執行」的本地機制：`actionFusion: true` + `observationPack: true`，另外兩個留 false。
- **安裝方式：**
  - **npm（前置依賴）：** `npm install --global @earendil-works/pi-coding-agent@0.84.2`（README 指定的已測試版本；需 Node.js 22.19+）。
  - **host plugin install（主安裝路徑）：** `pi install git:github.com/NVlabs/SoL-Pi`；只裝給當前專案則 `pi install git:github.com/NVlabs/SoL-Pi --local --approve`。
  - 開發：`npm ci --ignore-scripts` → `npm run check` → `npm audit --audit-level=high` → `node scripts/check-pi-compat.mjs`。
  - 本身**沒有發到 npm registry**，走 git 直裝；設定驗證腳本 `scripts/check-sol-pi-config.mjs --require-all-enabled`。
- **近期 release：** **未找到 GitHub release**（`/releases/latest` 回 404）。repo 建立於 2026-09-02，至今 9 天，仍在每日 push（最後一次是今天 05:18 UTC），屬於「剛開源、還沒開始打 tag」的階段；版本錨點目前掛在它相依的 Pi 版本 `0.84.2` 上。

### `earthtojake/text-to-cad`

- **Repo 摘要：** 一個給 agent 用的 CAD / CAE / CAM 技能庫——共 11 個 skill，涵蓋用自然語言或圖片生成與編輯 CAD 模型（主輸出 STEP，另可出 STL / 3MF / GLB）、本地瀏覽器預覽、找現成的螺絲軸承馬達 STEP 零件、產 2D DXF 圖、寫機器人描述檔（URDF / SRDF for MoveIt2 / SDF）、上傳 SendCutSend 前的檔案檢查、列印性分析（壁厚、懸空、支撐體積、擺放方向）、用真實切片器 CLI 切出帶印表機 profile 的 `.gcode`，一直到謹慎啟動本地 Bambu Lab 列印工作。15,325★、Python、245 MB（含 LFS 夾具語料）、MIT、`pushed_at` 2026-09-11 05:00 UTC。**這是「agent skill」從寫程式／查資料往實體工程延伸的代表案例——一條從一句話到列印機的完整鏈路。**
- **3W1H：**
  - **What：** skill 庫（每個 skill 是一份 `SKILL.md` + 自己的 `requirements.txt`），不是應用程式、不是 library；底層靠 PyPI 上的 `cadgen` 套件（最新 0.5.1，2026-09-08 上架，共 30 版，`requires_python >= 3.11`）。
  - **Why：** text-to-code 與生成式圖像跑得很快，但生成式「實體工程」一直落後——檔案格式封閉、軟體生態複雜、出錯成本是真實的材料與時間。這個專案不做「一句話生 mesh」那種視覺玩具，而是往下游可用的工程產物走（STEP 給 CAD、URDF 給模擬、gcode 給印表機），所以 skill 之間可以串成 pipeline。
  - **Who：** 機械／機器人工程師、做 3D 列印與小批量製造的 maker、想讓 agent 碰實體工作流的 AI 開發者。
  - **How：** 用 Skills CLI 裝進你的 agent host，之後在對話裡直接叫用；每個 skill 的 `requirements.txt` 鎖住它發佈時對應的 `cadgen` 版本。
- **安裝方式：**
  - **npx（官方首選）：** `npx skills add earthtojake/text-to-cad`。更新也用同一個指令——README 特別警告 `npx skills update` 只會刷新 lockfile 裡已有的 skill，**新版新增的 skill 會被靜默漏掉**，而這個專案的 release 確實會加 skill；移除用 `npx skills remove <skill>`。
  - **各 agent host 原生 plugin：** Codex `codex plugin marketplace add earthtojake/text-to-cad` + `codex plugin add cad@text-to-cad`（**需 Codex 0.142.0 以上，舊版會靜默跳過、`codex plugin list` 裡根本不出現**）；Claude Code `claude plugin marketplace add …` + `claude plugin install cad@text-to-cad`；Grok Build `grok plugin install earthtojake/text-to-cad --trust` + `grok plugin enable cad`（沿用 `.claude-plugin/marketplace.json`，沒有獨立 manifest）。
  - **pip（間接）：** 各 skill 的 `requirements.txt` 會拉 PyPI 上的 `cadgen`；`models/` 那份夾具語料只是 LFS 指標，用 skill 不需要它。
- **近期 release：** **v0.5.1，published 2026-09-08 18:32 UTC（3 天前）**，PyPI 上的 `cadgen` 0.5.1 同日（18:32:15）同步上架——**GitHub release 與 PyPI 完全同步，是今天五個裡版本治理最乾淨的一個。**

---

## 重點觀察

- **Release 新鮮度兩極，且「tag 跟不上 push」是普遍現象。** 5 個裡 4 個有 GH release：llmfit v1.1.15（昨天）、text-to-cad v0.5.1（3 天前）、CloddsBot v1.9.0（11 天前）、gods-eye-view v0.1.1（10 天前）；SoL-Pi 0 release（9 天新 repo，還沒開始打 tag）。但五個 repo 全部在今天或昨天還有 push——**gods-eye-view 尤其誇張，tag 還停在 0.1.x 卻已 24,973★ 且每天在推**，判斷活躍度只看 release 日期會嚴重失真，要同時看 `pushed_at`。
- **套件化程度差距巨大，而且「有 registry」不代表「registry 可信」。** llmfit 是極端正面案例：crates.io + uv tool + uvx + brew + scoop + MacPorts + curl|sh + ghcr Docker + cargo build，一個 Rust 專案同時發到 Python 生態（`uv tool install -U llmfit`），而且 crates.io 與 GH release 同版同日。CloddsBot 是極端反面案例：README 主推 `npm install -g clodds`，但 npm `latest` 停在 **1.7.7 / 2026-02-13**，GH release 已到 v1.9.0 / 08-31——**套件落後源碼約 7 個月，照 README 裝到的是舊版**。查 registry 的發佈時間戳，是這類 repo 的必要動作。
- **Agent skill 的安裝路徑正式脫離傳統 registry。** text-to-cad 走 `npx skills add` + Codex / Claude Code / Grok Build 三家原生 plugin marketplace；SoL-Pi 走 `pi install git:github.com/…`，兩者都不進 `node_modules`、都由 host 負責載入。兩個 repo 同時出現兩個新坑：Codex 低於 0.142.0 會**靜默跳過** plugin、`npx skills update` 會**靜默漏掉**新增的 skill——這類安裝方式的失敗模式全是「沒有錯誤訊息」，比裝失敗更難察覺。
- **語言生態：TS/JS 3、Rust 1、Python 1，但語言與發佈生態已經解耦。** llmfit 是 Rust 寫的卻用 `uv tool` / `uvx` 當主推安裝路徑；SoL-Pi 是 TypeScript 卻不發 npm，走 git 直裝；text-to-cad 是 Python 卻用 `npx` 當官方安裝指令。**看語言猜安裝方式，今天五個有三個會猜錯。**
- **License：4/5 純 MIT，但 gods-eye-view 的 `NOASSERTION` 是今天最需要注意的一筆。** 它的 LICENSE 檔前半是標準 MIT，後半追加了第三方資料排除條款——TeleGeography 海底電纜圖是 **CC BY-NC-SA 3.0 禁商用**、OSM 抽出的資料中心與水壩是 **ODbL 1.0 share-alike**、`public/models/` 的 3D 模型各自有授權。程式碼可以自由用，**資料不行**；商用前要逐檔清。另外 5/5 的 README 都 **0 次提及 Hermes**（09-10 `teamai-cli` 的回升沒有延續），只有 llmfit 有一份 `docs/openclaw.md`。
- **對主人的實用度排序：** llmfit 直接可裝可用（`brew install AlexsJones/llmfit/llmfit` 或 `uv tool install -U llmfit`，Apple Silicon 統一記憶體是它的一級支援場景，正好回答 16 GB M4 Max VM 能跑哪些模型）；SoL-Pi 的四個機制（Action Fusion / ObservationPack / 引文逐字比對的 Reducer / 帶經濟性檢查的 Compact）是可直接借鑑到 harness 設計的 primitive，但其省 45–49% token 的數字是**專案自報、尚無第三方複現**；gods-eye-view 需 Node 24.14+/26.x；CloddsBot 屬真錢自主交易，性質上不建議在主機上跑。
