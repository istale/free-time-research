# GitHub 專案動態
- 檢查時間：2026-08-13
- 檢查對象：GitHub Daily Trending top 3 + 自由探索 2 個（共 5 個 repo）

## Repo 摘要與 3W1H

### `cathrynlavery/diagram-design`
- **Repo 摘要：** 一個給 Claude Code / Codex / Pi 共用的「編輯級」圖表 skill，提供 27 種靜態視覺類型（架構、流程、時序、ER、狀態機等）的 HTML + SVG 範本，主打「不再產生 Mermaid-slop 或泛用圓角框」。repo 本體就是 `SKILL.md` + references + assets，沒有 build step，直接打開就能用。
- **3W1H：**
  - **What：** Claude Code / Codex / Pi 的 diagram-design plugin — 一個完整 skill，可重畫 draw.io / Mermaid 來源，也可從空白生成。
  - **Why：** LLM 預設產生的工程圖太通用、設計品質低落，作者想把它「升級到編輯部水準」並內建品牌對齊（讀取網站色票、字體）。
  - **Who：** 寫技術部落格、做架構/流程文件、需要 editorial-grade 視覺的開發者與內容創作者。
  - **How：** 透過 Claude Code plugin marketplace 安裝；在 Claude 對話裡直接要求「draw an architecture diagram」或用 `/skill:diagram-design` 顯式觸發。
- **安裝方式：**
  - **Claude Code plugin marketplace：** `/plugin marketplace add cathrynlavery/diagram-design` 然後 `/plugin install diagram-design@diagram-design`。
  - **Codex：** `codex plugin marketplace add cathrynlavery/diagram-design`（啟動時 refresh，可手動 `codex plugin marketplace upgrade diagram-design`）。
  - **Pi：** `pi install https://github.com/cathrynlavery/diagram-design`，session 內 `/reload`，搭配 `/skill:diagram-design` 觸發。
  - **Editable install（開發用）：** `git clone git@github.com:cathrynlavery/diagram-design.git ~/code/diagram-design`，再 symlink `~/.claude/skills/diagram-design`。
  - 沒有 `pip install` / `npm install` 形式 — 屬於 plugin/skill 安裝而非語言套件。
- **近期 release：** 未找到 GitHub release（repo 採用 marketplace 版本通道，作者透過 plugin marketplace 發版而非 GitHub Releases）。

### `macro-inc/macro`
- **Repo 摘要：** 一個「all-in-one workspace」產品，把 email、chat、docs、tasks、agents、calls、CRM 全裝進同一個介面，並用 `@link` 雙向圖把所有物件串起來，搭配 shared team-level AI memory。技術底層是 SolidJS + Rust，後端是同一個雙向 graph；定位是想取代 Slack + Linear + Notion + HubSpot + Superhuman 的拼貼。
- **3W1H：**
  - **What：** 統一型工作系統（client + server），把 7 種 SaaS 收成一個 blocks-of-Lego 的 workspace。
  - **Why：** 多工具拼貼在 ~20 人規模後會失控，資料不互通、AI 沒共享記憶，「公司變得不可計算」；Macro 想用單一後端 + 雙向圖直接治這個痛點。
  - **Who：** 早期 startup 與中型團隊的 operator/founder，需要把工具堆疊收斂成一個 OS 的人。
  - **How：** 在 https://macro.com/app 註冊使用雲端版；社群版可從 source build（Rust + SolidJS）。下游 agent 可用內建的 `@` link + shared AI memory 結構化整個工作流。
- **安裝方式：**
  - 主要是 SaaS — `https://macro.com/app` 註冊即用，沒有對外釋出的 `pip`/`npm`/`cargo install` 套件。
  - 自架需要從 source build Rust backend + SolidJS frontend（README 未提供一鍵 dev script，AGPL-3.0 對自架有 copyleft 限制）。
- **近期 release：** 最新 release 為 `v2026.8.12.0`，發佈於 2026-08-12（published_at 2026-08-12T22:59:30Z），非 prerelease。採用日曆式版本號（YYYY.M.D.N），明顯是每日出貨節奏。

### `semantica-agi/semantica`
- **Repo 摘要：** 自稱「開源版 Palantir for AI Agents」— 一個 graph-native 基礎設施，把企業資料 ingest 後建成 Context Graph + Knowledge Graph（RDF + LPG 雙模式），並提供可追溯、可解釋的決策智能。它把 ontology、context engineering、graph-RAG、provenance 整合成同一個 Python 套件。
- **3W1H：**
  - **What：** Python SDK + CLI，定位是給 regulated / high-stakes domain 用的 AI 決策基礎建設。
  - **Why：** 企業 AI 最痛的兩件事 — context 散落各系統、決策過程不可審計；Semantica 用 knowledge graph + provenance 一次解決。
  - **Who：** 在金融、醫療、政府等高合規領域，要把 LLM 接進內部資料並留下 audit trail 的工程團隊。
  - **How：** `pip install semantica`（已有 PyPI 套件），Python 3.8+；從 enterprise 資料 ingest 開始 → 建立 ontology → 跑 graph-RAG / causal reasoning，全程有 decision provenance。
- **安裝方式：**
  - **pip：** `pip install semantica`（PyPI 套件，README badge 直接掛 pypi.org/project/semantica/）。
  - **uv：** `uv pip install semantica`（與 pip 相容，未額外標示但可通用）。
  - 另支援自架 backend（Neo4j / Memgraph 等 LPG + RDF store），完整部署需參考 docs.getsemantica.ai。
- **近期 release：** 最新 release 為 `v0.6.5`，發佈於 2026-08-11（published_at 2026-08-11T17:19:00Z），非 prerelease — 仍屬 0.x 階段但發版節奏密集，2 天內接續 macro 出貨。

### `shiyu-coder/Kronos`
- **Repo 摘要：** 第一個專為金融 K 線（OHLCV）設計的開源 foundation model，decoder-only Transformer + 兩階段 tokenizer（先量化 OHLCV 為 hierarchical discrete tokens，再 autoregressive 預訓練）。訓練資料橫跨 45 個交易所，已被 AAAI 2026 接受，arXiv 2508.02739。
- **3W1H：**
  - **What：** 金融時序基礎模型（Kronos family），附 tokenizer + 多尺寸 pretrained weights。
  - **Why：** 通用 TSFM 在高雜訊 OHLCV 上表現不佳；Kronos 把 K 線視為「語言」並用 discrete tokenization 解掉連續數值噪訊問題。
  - **Who：** 量化交易員、做市/造市團隊、研究金融時序的學術與工業界工程師。
  - **How：** 從 Hugging Face (`NeoQuasar/Kronos-*`) 拉模型 → 用作者提供的 tokenizer 把 OHLCV 編碼成 tokens → 餵進 Kronos 推理；也有 BTC/USDT live demo 可直接看 24 小時 forecast。
- **安裝方式：**
  - **pip：** `pip install -r requirements.txt` 後從 Hugging Face 拉 model weights（依賴：numpy、pandas、torch>=2.0、einops、huggingface_hub、safetensors 等）。
  - 沒有 PyPI 套件化，沒有 `setup.py` / `pyproject.toml`，純 source + requirements。
- **近期 release：** 未找到 GitHub release（採用 Hugging Face Hub 發布 weights + GitHub 更新程式碼的模式；最近一次 code push 是 2026-04-13）。

### `msitarzewski/agency-agents`
- **Repo 摘要：** 「一個 AI 代理商號的完整 roster」— 從 frontend wizard 到 Reddit community ninja 一系列預先寫好的 specialist agent prompts（帶 personality + workflow + deliverables），可直接裝進 Claude Code / Cursor / Codex / Gemini CLI / OpenCode / OpenClaw / Hermes 等多種工具。另有 native desktop app（agency-agents-app）做一鍵安裝與 auto-update。
- **3W1H：**
  - **What：** 一個 prompt-as-agent 的 collection repo（Shell 為主，因為是檔案+script），加上配套桌面 app。
  - **Why：** LLM agent prompt 寫起來重又散，agency-agents 把「開一家 AI agency」這個比喻實體化 — 每個角色有自己的 identity、mission、deliverable，給人直接抄。
  - **Who：** 想用 Claude Code / Cursor / OpenClaw / Hermes 等多工具的 indie hacker 與小團隊，需要快速組裝一支 specialist team。
  - **How：** `brew install --cask msitarzewski/agency-agents/agency-agents`（桌面 app）或執行 repo 內 `./scripts/install.sh --tool claude-code`；或單純把 `engineering/*.md` 之類 copy 進 `~/.claude/agents/`。
- **安裝方式：**
  - **Homebrew (cask)：** `brew install --cask msitarzewski/agency-agents/agency-agents`（macOS / Linux / Windows 桌面 app）。
  - **Shell script：** `./scripts/install.sh --tool claude-code`（支援 Claude Code / Cursor / Codex / Gemini CLI / OpenCode / Qwen / Osaurus / Hermes 等）。
  - **手動：** `cp engineering/*.md ~/.claude/agents/` 直接複製 markdown prompt 檔使用。
  - 沒有 `pip` / `npm` 套件（純 markdown + shell installer）。
- **近期 release：** repo 本體未找到 GitHub release（distribute 走 desktop app release channel），最近一次 push 為 2026-08-06；配套桌面 app `agency-agents-app` 有獨立 release，由 README 的 Download badge 指向。

## 重點觀察
- **Release 新鮮度兩極：** macro `v2026.8.12.0` 與 semantica `v0.6.5` 都在 8/11–8/12 內發版，節奏明顯是日/週更；反觀 diagram-design、Kronos、agency-agents 三者都不用 GitHub Releases，改走 plugin marketplace / Hugging Face / 桌面 app 的 release channel，所以「沒 release」不代表不活躍 — 要看對應通道。
- **安裝/上手門檻差異大：** semantica 對 Python 開發者最友善（`pip install semantica` 即可，PyPI 已上架）；Kronos 是研究取向（clone + requirements.txt + HF weights）；diagram-design 是 plugin/skill 形式（`/plugin marketplace add` 或 `pi install`）；agency-agents 走 brew cask + shell installer；macro 純 SaaS。語言生態分布：Python 2、TypeScript/HTML 1、Shell 1、Rust 1。
- **語言/技術棧光譜：** Python（semantica、Kronos）— 偏 AI infra / research；HTML+SVG（diagram-design）— 偏 UX/editorial；TypeScript+Swift/Kotlin+Electron（orca 缺席，但 agency-agents-app）— 偏桌面；Rust+SolidJS（macro）— 偏後端效能與 startup 生產力工具。
- **pip/npm 套件化狀況：** 只有 semantica 真正做了 PyPI 套件化並掛 badge；Kronos 沒做 PyPI 但走 Hugging Face weights；其餘三個都不是「library」性質而是「product / skill / prompt collection」，本來就不適合打包。
- **與主人近期關注的對齊：** Kronos（金融 K 線、量化）與 macro（startup 工作系統）都直接命中主人背景；agency-agents 是 agent orchestration 維度，跟主人 8 月正在用的 Hermes agent / OpenClaw 互通（明確列在支援工具名單）；semantica 對應主人過去的 ontology / knowledge graph 探索；diagram-design 偏 editorial UX，跟主人對視覺品質的偏好同向。
