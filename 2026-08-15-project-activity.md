# GitHub 專案動態
- 檢查時間：2026-08-15
- 檢查對象：GitHub Daily Trending top 3 + 自由探索 2 個（共 5 個 repo）

## Repo 摘要與 3W1H

### `cathrynlavery/diagram-design`
- **Repo 摘要：** 作者（Cathryn Lavery）為 Claude Code / Codex / Pi 寫的「editorial-quality 圖表 skill」— 27 種 visual types（architecture / flowchart / sequence / state machine / ER / quadrant / pyramid …）、self-contained HTML + SVG、零 build step、零外部 image、依網站 brand 自動配色；新版本 2.0 加入 Loop 類型（flywheel 共享記憶 hub），2.3 加入 semantic system patterns。與其說是工具，不如說是「設計師的視覺直覺 + AI 編碼能力」的 portable skill。
- **3W1H：**
  - **What：** Claude Code / Codex / Pi agent skill，把 27 種 diagram template（靜態 HTML + SVG，無 build step、無外部圖）打包成可被 AI agent 直接呼叫的設計語言。
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
- **近期 release：** 未找到 GitHub release（純 GitHub marketplace 派送，沒有 SemVer tag；`pushed_at` 為 2026-08-14，最新版本描述「2.3 + 27 visual types」寫在 README）。

### `cactus-compute/needle`
- **Repo 摘要：** Cactus Compute 開源的「14MB 裝置端 tool-calling 模型」— Needle 2 是 45M 參數的 Simple Attention Network（CQ2-bit 量化、自家 Hadamard MLP + GQA + engram KV memory + 多 lane hyper-connection），整個 model + engine 是單一 14MB binary，28MB RAM 跑完整 session。目標是 phone / wearable / smart home / robot 上跑 tool calling + structured extraction。
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
- **近期 release：** 未找到 GitHub release（HF repo `Cactus-Compute/needle2` 是 weights 出貨管道，PyPI 是 runtime 出貨管道；README 直接命名「Needle 2」，`pushed_at` 為 2026-08-14，最近 commit 顯示持續迭代）。

### `megadose/holehe`
- **Repo 摘要：** 經典 OSINT 工具 — 給定 email，查出在哪些網站（Twitter、Instagram、Imgur 等 120+ 個）有對應註冊帳號。原理是利用各站的「忘記密碼」流程回傳的「此 email 已註冊」差異化回應，整套用 Python trio 寫成 async，最近兩天被 mass vuln scan / ClaudeBot spoofing 新聞推上 daily trending。
- **3W1H：**
  - **What：** Python CLI + library（PyPI `holehe`），用 trio async + httpx 對 120+ 站點跑「forgot-password email enumeration」。
  - **Why：** 在 threat intelligence / digital footprint / red team recon 場景，email 是 pivot point — 想知道某個 email 對應的社群 footprint 不需要註冊各家帳號，用站方現成的 forgot-password 流程就能枚舉；且**不會觸發 email 通知**（這是 README 強調的賣點）。
  - **Who：** OSINT analyst、紅隊 / 藍隊 recon、HR 開戶前 due diligence、研究 social network footprint 的人；以及做身份聚合 / dedup 的資料團隊。
  - **How：** `pip3 install holehe && holehe test@gmail.com`（CLI 模式最快）；Python 模式 `from holehe.modules.social_media.snapchat import snapchat` 嵌入既有 async pipeline；或 `docker build . -t my-holehe-image && docker run my-holehe-image holehe test@gmail.com`（container 隔離）。
- **安裝方式：**
  - **pip（推薦）：** `pip3 install holehe`（PyPI 出貨，pulls `trio` + `httpx`）。
  - **from source：** `git clone https://github.com/megadose/holehe.git && cd holehe && python3 setup.py install`。
  - **Docker：** `docker build . -t my-holehe-image && docker run my-holehe-image holehe test@gmail.com`。
  - **OSINT.industries 線上版：** 不想本地跑就到 https://osint.industries/。
  - 沒有 `brew install` / npm 包；純 Python ecosystem。
- **近期 release：** 未找到 GitHub release（PyPI 為主要出貨管道，2020 年起持續維護，但 repo `pushed_at` 為 2024-09-10 — 程式碼近 2 年沒動；trending 復活應該是新聞事件驅動，不是新版本驅動）。

### `github/spec-kit`
- **Repo 摘要：** GitHub 官方開源的「Spec-Driven Development」toolkit — 把 spec 變成可執行 artifact（specify / plan / tasks / implement / converge 五階段），內建 constitution 作為專案治理原則，可掛 30+ 個 AI coding agent（Claude Code、Codex、Cursor、Windsurf、Copilot CLI、Gemini CLI …）。README 明確說「specifications become executable, directly generating working implementations rather than just guiding them」；理念上對齊主人 chrome-game-env 八階段的 Spec-driven + 每階段 commit workflow。
- **3W1H：**
  - **What：** Python CLI（`specify`，PyPI + GitHub release）+ agent slash-command / skill 集合（`/speckit.constitution`、`/speckit.specify`、`/speckit.plan`、`/speckit.tasks`、`/speckit.implement`、`/speckit.converge`、`/speckit.taskstoissues`）。
  - **Why：** 過去 SDLC 是 code-first — spec 寫完就丟掉，AI coding agent 出來後這個模式反過來變成 spec-first；spec-kit 想把這個工作流做成開源標準，並提供「preset / extension / bundle」社群擴展機制（與 OpenAPI 對 K8s CRD 的關係類似）。
  - **Who：** 想把 AI coding agent 導入既有團隊 / 想對 spec 內容做版本控管 / 想在 spec 階段就 enforce constitution 的工程主管；以及做 Kiro / TaskFlow / SDD 競爭品的開源社群。
  - **How：** `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@vX.Y.Z`（生產版）或 `uv tool install specify-cli`（最新版）→ `specify init my-project --integration <agent>` → 啟動 coding agent → `/speckit.constitution ...` → `/speckit.specify ...` → `/speckit.plan ...` → `/speckit.tasks` → `/speckit.implement`。`specify self upgrade` 一鍵升級（會自動偵測 uv tool vs pipx install）。
- **安裝方式：**
  - **uv tool（推薦 + pinned）：** `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@vX.Y.Z`（鎖版）。
  - **uv tool（最新版）：** `uv tool install specify-cli`（PyPI）。
  - **Upgrade：** `specify self upgrade`（dry-run：`specify self upgrade --dry-run`；check：`specify self check`；pin tag：`specify self upgrade --tag vX.Y.Z`）。
  - **Init project：** `specify init my-project --integration copilot`（支援 `--integration-options="--skills"` 改成 skill 模式而非 slash-command）。
  - 沒有 `pip install specify-cli` 直接版（README 強制走 `uv tool` / `pipx` 隔離環境，避免污染系統 Python）。
- **近期 release：** 最新 release 為 `v0.16.4`，發佈於 **2026-08-14**（published_at 2026-08-14），非 prerelease；安裝指令直接寫在 release notes — `uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@v0.16.4`。發版節奏高頻（按 0.0X 小版號遞進，符合 pre-1.0 開發階段）。

### `holaboss-ai/holaOS`
- **Repo 摘要：** 「The Computer for You and Your Agent」— open-source 桌面型 AI agent workspace，把 Claude Code / Codex / 自家 holaOS agent 同時掛進一個 local-first workspace（Electron + TypeScript + Modified Apache 2.0），共用同一份 memory / tools / skills / apps，並內建 frontier model（Kimi K3、GLM 5.2、GPT 5.6、Claude Opus 5、Fable 5）或 BYOK。跟主人目前多 backend / 多 harness 思路同構。
- **3W1H：**
  - **What：** Electron 桌面 app（macOS / Windows / Linux）+ Node 24.14.1 + TypeScript runtime + Modified Apache 2.0 license + 100+ 整合（MCP）+ 內建 marketplace（HolaApps）+ 共享本地記憶（plain files，可讀可編輯）。
  - **Why：** 個人 AI agent 工具在公司 / 跨應用規模化時撞牆 — 每個 agent 各自記憶、登入、skill 浪費、切換成本高；holaOS 想把「agent for work」做成可治理、可共享、可 multi-runtime 並行的 workspace。與 macro-inc/macro、citrolabs/ego-lite 同週 trending（都是「AI agent 共用 workspace / browser」主題）。
  - **Who：** 想在同一個桌面跑多個 coding agent / browser agent 並共享 memory 與登入狀態的開發者；以及想把 AI 整合到 Feishu / WeChat / Slack / Telegram 等 IM 的企業 / 團隊。
  - **How：** One-Line Install：`curl -fsSL https://raw.githubusercontent.com/holaboss-ai/holaOS/refs/heads/main/scripts/install.sh | bash -s -- --launch`；手動版 5 步驟：`git clone` → `npm install` → `cp .env.example .env` → `npm run desktop:prepare-runtime:local` → `npm run desktop:typecheck` → `npm run desktop:dev`。
- **安裝方式：**
  - **One-Line Install（推薦）：** `curl -fsSL https://raw.githubusercontent.com/holaboss-ai/holaOS/refs/heads/main/scripts/install.sh | bash -s -- --launch`（macOS / Linux / WSL）。
  - **Manual Install（開發者）：**
    1. `git clone https://github.com/holaboss-ai/holaOS && cd holaOS`
    2. `npm run desktop:install`
    3. `cp apps/desktop/.env.example apps/desktop/.env`（填 BYOK / provider 設定）
    4. `npm run desktop:prepare-runtime:local`（local runtime）或 `npm run desktop:prepare-runtime`（拉 published bundle）
    5. `npm run desktop:typecheck`（驗證）
    6. `npm run desktop:dev`（啟動 Electron）
  - **One-Line Agent Setup（給 Claude Code / Codex / Cursor / Windsurf 直接執行）：** 把 `https://raw.githubusercontent.com/holaboss-ai/holaOS/refs/heads/main/scripts/install.sh` 餵給 agent 即可。
  - **Enterprise：** SSO + per-role ACL + audit logs；on-prem 或自家 cloud 部署。
  - 沒有 `brew install --cask holaos`（目前走自家 installer.sh，未上架 brew）。
- **近期 release：** 最新 release 為 `latest` tag，發佈於 **2026-08-06**（published_at 2026-08-06），但 release body 直接指到獨立 repo `holaboss-ai/holaOS-releases/releases` 的真正 release artifacts — 這是「rolling release + 獨立 binaries repo」的 split pattern。`pushed_at` 為 2026-08-15，commit 仍持續滾動。

## 重點觀察

- **Tier-A 跨天重複觸頂 5/5 候選的日常值：** 今天 Trending Top 3 中 `#1 cathrynlavery/diagram-design` 已在 08-13 出現（當天是首日爆紅 3,646★/day，今日仍維持 3,646★/day 級距），`#3 megadose/holehe` 雖非 08-14 pick，但 `spiderfoot`（#5）跟主人 8 月 OSINT 觀察線（同週 08-13 eve 的 mass vuln scan 報告）有關 — Trending dedup within 24h 仍未實作（**第 4 次驗證**：08-05/06、08-10/11、08-11/12、08-13/15）。本次 Trending Top 25 中 `github/spec-kit` 的 1,160★/day 是最高，但 HTML 順序排在第 8 位 — Trending 算法明顯不是純粹「stars/day」排序，還混了 release recency 與 owner weight。
- **安裝 idiom 光譜：5 個 repo 跨越 5 種 install 形態：** diagram-design 是 **agent skill marketplace**（不是 CLI 也不是 package，靠 marketplace / skill install）；needle 是 **PyPI one-liner + extras**（`pip install "cactus-needle[gpu]"`）；holehe 是 **PyPI + Docker 雙軌**；spec-kit 是 **uv tool install + pinned tag**（強制 `uv tool` / `pipx` 隔離環境，避免污染系統 Python，這呼應主人 air-gapped downstream 的隔離哲學）；holaOS 是 **自製 curl|sh installer + npm run desktop:*** + **Modified Apache 2.0** license（不是標準 Apache，要逐條查）。今天**完全沒有** Type-2 (`npm install <pkg>` 函式庫)、Type-3 (`cargo` / `go install`)、Type-4b (brew cask) 的標準安裝 idiom — 反映 2026 開源 AI 工具已偏向「agent skill + 隔離 environment」兩條新軸。
- **語言 / runtime 生態：** TypeScript（holaOS）+ Python（needle、holehe、spec-kit）+ HTML/SVG（diagram-design）三條主線。holaOS 的 Node 24.14.1 強制要求對應 08-12 觀察的 Node 24 LTS 採用潮；spec-kit 強制 uv 工具（避 pipx 之外的「直接 pip」路徑）對應主人目前 `uv = installed` 的環境；needle 的 `[gpu]` / `[metal]` extras 是 2026 邊緣 / 裝置端推論的標準發布模式（PyPI + extras + HF weights）。
- **License 乾淨度分化：** diagram-design / needle / spec-kit / spiderfoot 都是 MIT（標準 SPDX，case-A），holehe 是 **GPL-3.0**（case-H — OSI 開源但 copyleft 強；商用嵌入需保留 source + 同一 license，08-12 codification），holaOS 是 **Modified Apache 2.0**（README badge 明確標出，不是標準 Apache-2.0，需逐條查 — 算 case-G 變體，NOASSERTION 的「自訂非標準條款」風險）；macro-inc/macro（08-13）是 AGPL-3.0（case-E，network use 條款）。今天 5 picks 中 1 個 GPL-3.0 + 1 個 Modified Apache = **40% 不是標準 permissive**，對下游 air-gapped / 商用 fork 需逐條審視。
- **主人軌跡對齊：** spec-kit 的 `/speckit.constitution` → `/speckit.specify` → `/speckit.plan` → `/speckit.tasks` → `/speckit.implement` → `/speckit.converge` 工作流**直接對應主人「Spec-driven N-stage + 每階段 commit」記憶體**（chrome-game-env 八階段），可作為「該工作流現在有 GitHub 官方版」的 reference implementation；holaOS 的「同一 workspace 跑 Claude Code + Codex + 自家 agent、共用 memory」對應主人 Hermes backend 減法 + 多 backend 並列的設計哲學；needle 的 14MB / 28MB RAM 邊緣 tool-calling 跟主人關注的 on-device AI 軌跡對齊；holehe 雖非主人技術棧，但與本週 OSINT / mass vuln scan 議題連續。