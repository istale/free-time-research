# GitHub 專案動態
- 檢查時間：2026-08-14
- 檢查對象：GitHub Daily Trending top 3 + 自由探索 2 個（共 5 個 repo）

## Repo 摘要與 3W1H

### `deepseek-ai/deepseek-harness`
- **Repo 摘要：** DeepSeek 官方釋出的 agent harness，整個 runtime 走「everything is a plugin」架構，底層用 [Cordis](https://github.com/cordiverse/cordis)（論文 *A Programming Paradigm for Spatiotemporal Composability*）做 plugin 組合；目前仍在 developer preview，作者明確警告「會有 compatibility-breaking changes」。一天內從 0 衝到 71k stars，是本週最具爆發力的開源 agent 框架。
- **3W1H：**
  - **What：** TypeScript-based agent runtime / harness，附帶 Web UI（預設 `http://127.0.0.1:3080`）。
  - **Why：** 想把 agent loop + 工具 + 模型抽象成可熱插拔的 plugin，並用 Cordis 的時空組合性讓 long-running agent session 與多模型共存變得乾淨；同時是 DeepSeek 拿來展示其 agentic 後端的旗艦開源專案。
  - **Who：** 想自己跑本地 multi-agent / multi-tool runtime 的開發者、研究 LLM agent 架構者、以及想以 DeepSeek 模型為後端搭 agent 系統的工程團隊。
  - **How：** 最快路徑 `npx @deepseek-ai/dsh web` 開 Web UI；想改 source 就 `git clone` + `pnpm install` + `pnpm run build` + `pnpm dsh web`。plugin 用 `dsh-plugin` topic 在 GitHub 上發布可被自動收錄。
- **安裝方式：**
  - **npx（最速）：** `npx @deepseek-ai/dsh web`（需先裝 Node.js）。
  - **npm：** `npm install -g @deepseek-ai/dsh`（global 安裝後即可直接 `dsh web`）。
  - **pnpm（from source）：** `git clone https://github.com/deepseek-ai/deepseek-harness.git && cd deepseek-harness && pnpm install && pnpm run build && pnpm dsh web`。
  - **Agent Skill：** 尚未官方整合進 agentskills.io，但本身已有 Discord + GitHub Discussions 社群通道。
  - 沒有 `pip install` / `cargo install` / Docker 形式 — 純 Node 工具鏈。
- **近期 release：** 未找到 GitHub release（repo 一天內創建，仍在 0.x dev preview，採用 source 滾動出貨；`pushed_at` 為 2026-08-13，最近一次 commit 為 master 分支直接滾動）。

### `firecrawl/anydoc`
- **Repo 摘要：** Firecrawl 開源的「office 文件 → LLM-ready Markdown」轉換引擎，Rust 寫核心 + 自動產生 Node.js / Python / WebAssembly 綁定；支援 Word / PowerPoint / Excel / OpenDocument / RTF / EPUB / CSV / PDF，目標是「不論輸入什麼格式，輸出長一樣」。同類工具（docling、markitdown）大多綁 Python，這是少見 Rust 為骨、跨 4 種 runtime 的設計。
- **3W1H：**
  - **What：** 文件轉 Markdown 的 library + CLI + agent skill，Rust core + Node/Python/WASM bindings。
  - **Why：** 餵 LLM 之前最煩的是把 PDF / DOCX / PPTX 弄成乾淨 GFM；現存工具要嘛慢、要嘛格式覆蓋不全、binding 只有一種語言。anydoc 用 Rust 換單數位毫秒轉換 + 多語言 binding，並把格式覆蓋做到 8 種（含 EPUB、RTF、OpenDocument）。
  - **Who：** 做 RAG / 文件問答 / agent 內嵌文件的工程師；以及需要把 PDF batch 倒進語料庫的資料團隊。
  - **How：** 最快 `npx @firecrawl/anydoc report.docx > out.md`；agent 內用 `npx skills add firecrawl/anydoc`（已上架 agentskills.io，支援 Claude Code / Codex / Cursor / OpenCode）；瀏覽器內可跑 WASM demo `https://firecrawl.github.io/anydoc/`，檔案不離開本機。
- **安裝方式：**
  - **pip：** `pip install firecrawl-anydoc`（PyPI 套件；提供 `anydoc.to_markdown(path)` / `to_markdown_bytes(bytes)` / `to_document(bytes)`）。
  - **uv：** `uv pip install firecrawl-anydoc`（與 pip 相容）。
  - **npm：** `npm install @firecrawl/anydoc`（Node API：`toMarkdown`、`toMarkdownBytes`、`toDocument`）。
  - **npx（CLI）：** `npx @firecrawl/anydoc report.docx`（首次會下載 prebuilt binary；要永久指令用 `npm install -g @firecrawl/anydoc`）。
  - **cargo：** `cargo install anydoc`（crates.io 已上架；適合純 Rust 專案直接拉）。
  - **wasm：** `npm install @firecrawl/anydoc-wasm`（瀏覽器內轉檔）。
  - **Agent skill：** `npx skills add firecrawl/anydoc`。
  - README 一口氣掛 Crates.io / npm / PyPI / skills.sh 四個 badge — 三語言生態完整。
- **近期 release：** 最新 release 為 `v0.1.9`，發佈於 2026-08-13（published_at 2026-08-13T21:40:10Z），非 prerelease — 內含「pdf-inspector 1.14.2」，仍在 0.1.x 早期版本號，但發版節奏屬於高頻小版號遞進。

### `yc-software/qm`
- **Repo 摘要：** YC 投資組合公司 QC（YC-software）釋出的「multiplayer agent harness for work」：把 agent 變成 Slack + Web 雙介面、可多人共用的同事，每個員工 / 每個頻道都有自己的 scope（memory、files、keychain、permissions、crons、web apps、sandbox），底層用 Pi / OpenCode / Codex / Claude Code 任一 harness 驅動。定位是「公司級的 agent OS」，而非個人助理。
- **3W1H：**
  - **What：** Multi-tenant agent harness + Slack plugin + Web UI（TypeScript / Fastify / Vite / Lit），含 `qm` CLI 處理 deployment 與 org config。
  - **Why：** 個人 agent 工具在公司規模化時會撞牆（資料隔離、權限、skill 共享、稽核）；qm 想把整個「agent for work」做成可治理、可共享、可插拔 harness 的系統。
  - **Who：** 早期 startup / 中型團隊的 founder / operator，想讓 agent 真的進到日常工作流並被多人共用；以及想做 on-prem / self-host agent infra 的 IT。
  - **How：** 用 `npx --yes @yc-software/qm@latest` 拉 CLI；建立 deployment directory（含 org config / 自定 skill / sandbox image），`qm validate && qm deploy`；Slack plugin 走 Bolt，核心走 Fastify HTTP API，可任意掛 plugin（web UI / admin / public portal 都是 optional plugin）。
- **安裝方式：**
  - **npx：** `npm exec --yes --package=@yc-software/qm@latest -- <cmd>`（讀 README 內引用的 CLI 入口，等同於 `npx -p @yc-software/qm qm ...`）。
  - **npm（global）：** `npm install -g @yc-software/qm` 後用 `qm` CLI（驗證 + 部署 deployment directory）。
  - **from source：** `git clone https://github.com/yc-software/qm.git && cd qm && npm install`，開發者入口見 `cli/README.md`。
  - **Sandbox：** core 跑 Node + Fastify，per-scope sandbox 是 durable container（隔離 files / tools / 登入的服務），具體 image 由 deployment directory 決定。
  - 沒有 `pip install` / Docker Compose 一鍵；需自備 Postgres + sandbox runtime，屬於「infra-grade」安裝。
- **近期 release：** 最新 release 為 `v0.1.4`，發佈於 2026-07-31（published_at 2026-07-31T18:03:56Z），非 prerelease；之後 `pushed_at` 顯示 2026-08-14 仍有 commit，但未切新 tag — 發版節奏偏「週更 + 小版號」。注意 `open_issues_count` 高達 216 個，是五個 repo 中最多，呼應它仍處於 0.1.x 且開發節奏密。

### `MoonshotAI/Kimi-K3`
- **Repo 摘要：** Moonshot 開源的「Open Frontier Intelligence」旗艦模型 K3 — 2.8T 參數的 MoE（Stable LatentMoE：896 專家中啟動 16 個），自創架構 Kimi Delta Attention + Attention Residuals（AttnRes），原生多模態（文字 / 圖 / 影片）並支援 1M token context window，量化策略是 QAT 階段就上 MXFP4 weights + MXFP8 activations。是目前公開權重的最大開源 frontier model。
- **3W1H：**
  - **What：** Open-weight foundation model（2.8T MoE + 原生 multimodality），含技術報告、benchmark 結果、部署指南、thinking-effort 控制介面。
  - **Why：** 開源社區一直沒有真正的 3T-class open weights 模型；Kimi K3 同時把「long-horizon coding、agentic knowledge work、native multimodality」三件事做到 frontier 等級，號稱在 Kimi Code Bench 2.0（in-house）拿下 SOTA。
  - **Who：** 研究 LLM 架構 / agentic coding / long-context 的學術與工業界研究員；以及想做 self-host frontier model 的 infra 團隊（需極大 GPU 叢集）。
  - **How：** 從 Hugging Face `moonshotai/Kimi-K3` 拉 weights，搭配官方推薦的 Kimi Code CLI（在 terminal 跑 `/model` 切到 K3）；inference 細節、thinking-effort 控制（`low` / `high` / `max`，預設 `max`）見 [Kimi K3 Quickstart](https://platform.kimi.ai/docs/guide/kimi-k3-quickstart)。
- **安裝方式：**
  - **Hugging Face：** 從 `huggingface.co/moonshotai/Kimi-K3` 下載 safetensors weights（2.8T 參數，需多機多卡 H100/H200/Blackwell 級別 GPU 叢集）。
  - **Kimi Code CLI：** 安裝 [Kimi Code](https://www.kimi.com/code) 後在 terminal 用 `/model` 切到 K3（官方推薦的 coding agent framework）。
  - **API：** 透過 `platform.kimi.ai` 直接呼叫（不用自架 weights）。
  - 沒有 `pip install kimi-k3` 或 `docker pull` 形式 — 這是 model repo 而非 library，標準安裝路徑是 HF weights + 推論框架（vLLM / 自家 runtime）。
- **近期 release：** 未找到 GitHub release（Kimi 系列走「主 repo 滾動更新 + Hugging Face 同步 weights」的模式，沒有 SemVer 標記；最近一次 `pushed_at` 為 2026-08-06，README 為主版本說明文件）。

### `mshumer/Claude-of-Duty`
- **Repo 摘要：** 作者（Shumer）宣稱「從一個 prompt 開始、由一組 AI agent 編寫」而成的瀏覽器 FPS — Three.js r180 + WebGL2，55k 行 code 拆 11 個 subsystem；零 art asset（所有貼圖、模型、動畫、音效都是程序生成），唯一 runtime dep 是 `three`。真正的看點其實是它用來產出這個遊戲的 agent harness（`tools/capture.mjs` / `shotset.mjs` / `baseline.mjs` / `imagediff.mjs` / `profile.mjs` / `playtest.mjs`），可當作「AI 寫 3D 遊戲」的可重現 baseline。
- **3W1H：**
  - **What：** Three.js WebGL2 FPS（瀏覽器內跑）+ 一組 AI-coding 用的 capture / diff / profile / playtest 工具鏈。
  - **Why：** 證明「給 agent 一個 `ARCHITECTURE.md` contract + 嚴格的可重現驗證工具鏈，就能產出可玩的 3D 遊戲」；同時把 GPU-backed headless screenshot、bit-identical baseline、per-pixel diff 做成可重複使用的腳手架。
  - **Who：** 對 AI-coding agent 有興趣、想看 agent 寫大型即時系統的人；以及做 game / graphics / WebGL 工作流自動化的工程師（capture / baseline / imagediff 工具鏈可獨立借用）。
  - **How：** `git clone` + `npm install` + `npm run dev`（開 `http://127.0.0.1:5173`）；想看 agent 怎麼寫 → 讀 `ARCHITECTURE.md`（subsystem interface / 目錄所有權 / event vocabulary）；想驗證 → 跑 `tools/baseline.mjs` + `tools/imagediff.mjs`。
- **安裝方式：**
  - **npm：** `git clone https://github.com/mshumer/Claude-of-Duty.git && cd Claude-of-Duty && npm install && npm run dev`（需 Node.js，啟動後訪問 `http://127.0.0.1:5173`）。
  - **無** `pip install` / `cargo install` / Docker 一鍵；純 JS 工具鏈。
  - **agent 工具鏈（獨立借用）：** `tools/capture.mjs` / `shotset.mjs` / `baseline.mjs` / `imagediff.mjs` / `profile.mjs` / `playtest.mjs` 都可在其他 Three.js 專案直接引用（README 把它列為重點 showcase）。
- **近期 release：** 未找到 GitHub release（單 push repo，`pushed_at` 為 2026-07-25 一次性 commit，之後無新版本；3.1k stars 全部來自當天的「one-shot prompt」爆紅 — 它的 release channel 是「README 就是 release notes」）。

## 重點觀察
- **Release 新鮮度兩極，但「沒 release」≠「不活躍」：** firecrawl/anydoc `v0.1.9` (8/13) 是唯一有正式 release 的；yc-software/qm 上次 release `v0.1.4` 在 7/31 但 `pushed_at` 是 8/14；deepseek-harness、Claude-of-Duty 與 Kimi-K3 三者都沒有 GitHub release — 前兩個走 source 滾動（一天破 71k stars 的 repo 根本不需要 release channel），Kimi-K3 走 Hugging Face weights 同步。讀 repo 不能只看 `releases/latest`，要看 `pushed_at` + 分支慣例。
- **安裝 / 上手門檻光譜：** anydoc 是本週最友善 — `npx @firecrawl/anydoc file.docx` 一行就跑起來，且一次給 4 種 binding（pip/npm/cargo/wasm）；Claude-of-Duty 與 deepseek-harness 都是 `npm install` + `npm run dev` 等級；qm 走 infra-grade（要自備 Postgres + sandbox runtime）；Kimi-K3 是 model repo，安裝成本主要在 GPU 叢集而非套件管理。整體看來今天的 top 5 把「rust 跨語言 binding」、「agent harness」、「frontier open weights」、「AI-generated 3D game」四種 install idiom 各代表了一次。
- **語言 / 技術棧光譜：** Rust（anydoc 核心、kvcache-ai/AgentENV 雖未上榜但同概念）做高效能 library；TypeScript（deepseek-harness、qm、genspark-ai/genoffice）做 agent runtime；JavaScript + Three.js（Claude-of-Duty）做 GPU 程式；Python 與 MoE 推論框架（Kimi-K3）做 frontier model；多語言綁定是 anydoc 的最大賣點 — 同樣的 Rust core 服務 Node、Python、WASM、native CLI 四種用戶。
- **pip / npm / cargo 套件化狀況：** anydoc 是今天唯一三平台全部套件化 + 完整 badge（Crates.io / npm / PyPI / skills.sh）的 repo，agent skill 安裝也內建支援；其餘四個都偏 framework / model / app，套件化不是主要交付形式。Claude-of-Duty 雖是「AI 寫遊戲」，但本身沒被包成 library，價值在工具鏈與架構 contract。
- **與主人近期關注的對齊：** deepseek-harness 與 qm 兩個都是 agent runtime / harness，跟主人目前在用的 Hermes agent、horo-agent 直接同構（特別 qm 對 Pi / OpenCode / Codex / Claude Code 多 harness 並列的設計呼應 Hermes 的 backend 減法哲學）；Kimi-K3 是 frontier open weights，跟主人 8 月已讀的 Cerebras GPT-5.6 Sol、DeepSeek 系列同一條 model 觀測線；anydoc 命中主人過去處理 RAG / 文件 ETL 的場景；Claude-of-Duty 是 agent-driven 3D / Three.js 工作流的精彩 showcase，正好對應主人遊戲 / 視覺偏好。
