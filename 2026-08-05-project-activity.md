# GitHub 專案動態
- 檢查時間：2026-08-05
- 檢查對象：TencentCloud/TencentDB-Agent-Memory、zhaoxuya520/reverse-skill、firecrawl/pdf-inspector、yc-software/qm、trycompai/crm
- 來源：Trending Daily Top 3 + 自由探索 GitHub Search（以 created:>2026-07-15 篩高 star + topic:ai-agent 雙角度交叉）

## Repo 摘要與 3W1H

### TencentCloud/TencentDB-Agent-Memory
- **Repo 摘要：** TencentDB Agent Memory 是「團隊級 Agent 記憶中樞」，把對話、文件、程式碼提煉成 Chat Memory、Skill、LLM-Wiki、CodeGraph 四類可重用資產，並以 Memory Core / Memory Hub / Proxy 三服務形式把資產裝備給不同 Agent 角色。它刻意與單一 Agent 框架脫鉤，且與 OpenClaw、Hermes Gateway、Claude Code 顯示同一條生態線；對想避免「換框架就重來」、要在團隊內共享經驗的建構者，比多數 memory plugin 走得更深。Repo 為 TypeScript，license 為 NOASSERTION（README 標示 MIT-衍生），14,103 stars，最後更新 2026-08-05。
- **3W1H：**
  - **What：** 團隊級 AI Agent 記憶中樞 + 四層資產（Chat Memory / Skill / Wiki / CodeGraph）+ Proxy 服務。
  - **Why：** 多數 Agent 每次都從零重學專案；Memory Hub 把已付的學習成本變成可繼承的 save file，並用 ownership/version/ACL 真正管理資產而非丟倉庫。
  - **Who：** 想避免重複上下文與重工的 AI Agent 團隊、要在 OpenClaw / Hermes / Claude Code 跨框架共享記憶的 builder，以及評估 long-term memory + RAG + code graph 整合方案的架構設計者。
  - **How：** clone deploy/global-images、複製 `.env`、填兩組 LLM 參數後執行 `./start-all.sh`，瀏覽器開 `http://localhost:8125` 進入 Memory Hub 面板；Proxy 端產生可貼回 Claude Code 的一行指令。最短上手：deploy + import 既有 codebase → 讓 Agent 自動產生 Wiki 與 CodeGraph。
- **安裝方式：** **npm：** README 顯示 npm scoped package `@tencentdb-agent-memory/memory-tencentdb`（latest 1.0.1，2026-07-14）；**GitHub source deploy：** `git clone https://github.com/Tencent/TencentDB-Agent-Memory.git` → `cd TencentDB-Agent-Memory/deploy/global-images` → `cp .env.example .env` → `$EDITOR .env` → `./start-all.sh`。README 未列 pip/uv/poetry 安裝方式；完整流程見 `INSTALL.md` / `INSTALL_CN.md`。v1.x/v0.x 升級 v2.0.0+ 須跑 `MemoryCore/scripts/migrate-v2-to-v3` 工具。
- **Repo metadata：** Description `TencentDB Agent Memory is a team-level memory hub for AI Agents — turning conversations, docs, and code into four reusable memory assets (Chat Memory, Skill, LLM-Wiki, Code-Graph) that are governed, shared, and equipped across agents and frameworks.`；Stars 14,103；Language TypeScript；Topics `agent`, `ai-agent`, `embedding`, `llm`, `local-first`, `long-term-memory`, `memory`, `openclaw-plugin`, `vector-search`；License NOASSERTION（README 標示 MIT）；Updated at 2026-08-05。
- **近期 release：** **2026-08-03**；`v2.0.0`；**v2.0.0**（v2.0.0 不是預發行）。

### zhaoxuya520/reverse-skill
- **Repo 摘要：** reverse-skill 是針對 AI 程式客戶端的「逆向與滲透」技能路由包：用 PowerShell + Bash 工具鏈，把 APK、ELF、JS、CTF、Pwn、API、LLM supply chain 等情境路由到對應的 skill，再以 case-init / MASTER-ROUTING 維持 scope 與證據鏈節奏。它並非 CLI 工具，而是 LLM 導向的「playbook + on-demand toolchain bootstrap」，適合做安全研究、CTF 與授權滲透的 AI 工作流；也等於示範了「skill pack + agent」這個交付形態。Repo 為 PowerShell，MIT，18,284 stars，最後更新 2026-08-05。
- **3W1H：**
  - **What：** 給 AI 程式客戶端的攻擊面 / 逆向 playbook 集，由 `RULES.md` + `MASTER-ROUTING.md` + `routing.md` 構成，並隨工具改自動進化經驗庫。
  - **Why：** Claude Code / Codex / Cursor / Kiro / Cline 拿到 APK、二進位、JS 加密時不會自動挑 `jadx` / `apktool` / `Frida` / `IDA` / `BurpSuite`，也無法保證 scope 與 authorizations；這個包把分類、工具自舉、證據鏈交給 agent 自己。
  - **Who：** 搞授權滲透 / 逆向 / CTF / Malware / 紅隊 / firmware / GraphQL 與 LLM-security 的安全研究者，以及想用 AI 自動化 security workflow 的 builder（**明確需在授權範圍內使用**）。
  - **How：** clone 整包，AI 客戶端先讀 `README_AI.md` 並嚴格遵守；依平台執行 `refresh-tool-index.ps1` 或 `refresh-tool-index.sh` 重新整理工具清單；實際任務走 `skills/MASTER-ROUTING.md` → `master-route.ps1` → `case-init.ps1` → 對應 skill 資料夾。
- **安裝方式：** 未找到明確 pip/npm 套件安裝方式。**Clone + tool index refresh：** `git clone https://github.com/zhaoxuya520/reverse-skill.git`；前置：JDK（jadx / apktool）、Node.js 22.12+、Python 3.x、各工具鏈；Windows `powershell -File skills/scripts/refresh-tool-index.ps1`、Linux/macOS `bash skills/scripts/refresh-tool-index.sh`、Kali `bash kali/scripts/refresh-tool-index.sh`；詳見 `kali/README-kali.md` / `docs/platforms/linux.md` / `docs/platforms/macos.md`。
- **Repo metadata：** Description `Reverse Engineering / Authorized Penetration Testing / Security Research Skill Router Pack AI-powered routing + On-demand toolchain bootstrapping + Self-evolving knowledge base`；Stars 18,284；Language PowerShell；Topics 未列；License MIT；Updated at 2026-08-05。
- **近期 release：** **2026-07-17**；`v1.0.0`；**v1.0.0 — First formal release**（v1.0.0 不是預發行）。

### firecrawl/pdf-inspector
- **Repo 摘要：** firecrawl/pdf-inspector 是 Firecrawl 釋出的 Rust PDF 解析器，目標是「在 200ms 內判斷 text-based vs scanned」，跳過對 54% 不需要 OCR 的 PDF 跑 OCR。它把 classification 與 text extraction 合併成一次載入、位置感知的 pipeline，並提供 Python / Node.js / WebAssembly 三種 binding，是看到「本地 PDF 處理先於 OCR」這條優化路線時最現成的組件。Repo 為 Rust，MIT，10,371 stars，最後更新 2026-08-05。
- **3W1H：**
  - **What：** 純 Rust 的 PDF 解析庫，附加 PyO3 / napi / WASM 三種綁定與 `pdf2md` / `detect-pdf` 兩個 CLI。
  - **Why：** 線上 OCR pipeline 對純文字 PDF 來說太慢也太貴；本地端用 content stream 抽樣就能 <100ms 判斷要不要送 OCR，順便避開重複 I/O 與 CMAP 編碼坑。
  - **Who：** 做 RAG / 文件 ingestion / 報告工具 / 線上文件代理 / 瀏覽器內 PDF 預覽的工程團隊；評估 PyMuPDF4LLM / MarkItDown / OpenDataLoader / liteparse 替代品的 builder。
  - **How：** Python 用 `maturin develop --release` 後 `pip install`；Node `npm install @firecrawl/pdf-inspector`；瀏覽器 `npm install @firecrawl/pdf-inspector-wasm`；Rust `cargo add pdf-inspector`；CLI `cargo install pdf-inspector` 拿到 `pdf2md` / `detect-pdf`。
- **安裝方式：** **pip/uv/poetry：** `pip install maturin` + `maturin develop --release`（PyPI `pdf-inspector` 0.2.6，2026-07-31 上傳）；**npm/pnpm/yarn/npx：** `npm install @firecrawl/pdf-inspector`（Node binding）、`npm install @firecrawl/pdf-inspector-wasm`（瀏覽器/WASM）；**cargo：** `cargo add pdf-inspector` 0.1.7 或 `cargo install pdf-inspector` 拿 CLI。README 也有 `mkdir -p $(rustc --print sysroot)/lib && cp ./target/release/libpdf_inspector.so ...` 版的進階 build 指引。
- **Repo metadata：** Description `Fast Rust library for PDF inspection, classification, and text extraction. Intelligently detects scanned vs text-based PDFs to enable smart routing decisions.`；Stars 10,371；Language Rust；Topics `markdown`, `nodejs`, `ocr-routing`, `pdf`, `pdf-classification`, `pdf-extraction`, `pdf-parser`, `python`, `rust`, `text-extraction`；License MIT；Updated at 2026-08-05。
- **近期 release：** **未找到 GitHub release**（最近一次 PyPI 上傳 `pdf-inspector 0.2.6` 為 2026-07-31、crates.io `pdf-inspector 0.1.7` 為 2026-07-31、npm `@firecrawl/pdf-inspector` 與 `@firecrawl/pdf-inspector-wasm` 隨 README 同步釋出；GitHub release 頁面仍為空）。

### yc-software/qm
- **Repo 摘要：** qm 是「企業內多人多 Agent 協作 harness」，每人 / 每頻道有自己的 scoped memory、files、keychain、permissions、crons、web apps 與 durable sandbox；同一個核心可掛 Pi、OpenCode、Codex、Claude Code 任一家做為底層，Slack 與 Web UI 共享身分。它和個人化 CLI agent 完全相反，目標是把 agent 做成可以「一個組織共用、權限明文化」的基礎設施。Repo 為 TypeScript，MIT，11,310 stars，最後更新 2026-08-05。
- **3W1H：**
  - **What：** Headless Agent Core（identity / policy / scheduler / per-scope sandbox）+ Slack + Web UI + Admin 的 multi-tenant agent harness，並提供 `qm` CLI 與 deployment dir 框架。
  - **Why：** 個人 assistant 模式很難擴到組織；qm 把「誰 / 哪個頻道 / 哪個 cron」對應到「能跑什麼 skill、能拿哪些 token、有沒有審核」，並用 Postgres 取代檔案存會話、用 plugin 取代 UI 鎖死。
  - **Who：** 9–200 人規模的 startup、需要共用 agent 卻又怕權限爆炸的 IT 負責人、想把 Slack / Web 與多個 coding agent 統一管理的 DevOps platform team。
  - **How：** 用 `qm init . --org <slug> --target <fly-or-aws>` 產出 deployment skill，交給 agent 走完 infra ／ 登入 ／ connector ／ 部署 ／ 驗證；個人可在該 deployment repo 內 `npm install`。
- **安裝方式：** **npm/pnpm/yarn/npx：** `npm exec --yes --package=@yc-software/qm@latest -- qm init . --org <slug> --target <fly-or-aws>` 後 `npm install`；README 強調「不需要 source checkout」。要走 deeper fork，則 `git clone --bare` upstream 後 mirror 到私有 repo，`pnpm/dev` 在私有 fork 內進行（核心檔永遠與 upstream 保持 byte-identical，見 `CONTRIBUTING.md`）。
- **Repo metadata：** Description `Multiplayer agent harness for work`；Stars 11,310；Language TypeScript；Topics 未列；License MIT；Updated at 2026-08-05。
- **近期 release：** **2026-07-31**；`v0.1.4`；**v0.1.4**（v0.1.4 不是預發行；PR #40 把 web-ui model picker 限制為 org allowed-models；PR #41 讓 `qm init` 從 `@latest` 啟動）。

### trycompai/crm
- **Repo 摘要：** trycompai/crm 是「agent-first CRM」：NestJS API 本身沒有 intelligence，只把「有人寄信 / 某聯絡人未確認」等事件寫成 queue row；research agent 透過 `apps/agent` 用 `eve` durable runtime 自己排程、租 lease、決定要不要打 API，靠 `bash + grep + glob` + deny-all egress 的 sandbox 跑研究。核心規則是「絕不猜測人的資料」——所有工具都回報「觀察到的事實」，由人類仲裁弱證據為建議。Repo 為 TypeScript、Bun + Postgres + Vercel Sandbox，MIT，5,060 stars，最後更新 2026-08-05。
- **3W1H：**
  - **What：** 開源、self-hosted、agent 為主體的 CRM；由 Next.js 前端、NestJS API、Eve-based research agent、Vercel Sandbox 沙盒組成。
  - **Why：** 多數 CRM 形同「資料 + 表單」，AI 版只是把 chat 視窗貼在旁；trycompai 反過來把 agent 當成「住在自己 deployment 裡的同事」，按自己的預算與排程工作，把 CRM 變成它的筆記本。
  - **Who：** 想要 on-prem / single-tenant / 真正 self-host 且可隨時 audit 的 B2B 銷售 / 客戶成功團隊，以及研究「evidence ledger / strong-vs-weak fact / no-confidence-score」CRM 設計模式的 builder。
  - **How：** `git clone https://github.com/trycompai/crm.git && cd crm` → `cp .env.example .env` → 填 `BETTER_AUTH_SECRET` / `ALLOWED_SIGN_IN` / Google OAuth → `bun install` → `docker compose up -d`（Postgres :5432）→ `bun run db:deploy` → `bun run dev`（App :3000、API :3001）。
- **安裝方式：** **npm/pnpm/yarn/npx：** `bun install`（不是 npm/yarn，但 README 寫在主流程）；**Docker compose：** `docker compose up -d` 啟 Postgres；**OAuth：** 需自備 Google OAuth client（README 附 2 分鐘申請教學）。**套件實際上是 Bun 工具鏈 + NestJS + Next.js 構成的 Turborepo，**沒有 PyPI / cargo / Homebrew 等獨立安裝方式。部署到 Vercel 以後端 API + 前端 Next.js 為主。
- **Repo metadata：** Description `An open-source, agentic-first CRM.`；Stars 5,060；Language TypeScript；Topics 未列；License MIT；Updated at 2026-08-05。
- **近期 release：** **未找到 GitHub release**（repo 2026-07-31 首次推送，迄今未打 GitHub release；版本節奏靠 commit + Bun 部署）。

## 重點觀察

- release 新鮮度今天整體偏熱：TencentDB Agent Memory `v2.0.0` 與 reverse-skill `v1.0.0` 都在當週（分別 8-3 與 7-17），qm `v0.1.4` 也在 7-31 發過；firecrawl/pdf-inspector 與 trycompai/crm 還沒發 GitHub release，但前者用 PyPI / crates.io / npm 三邊同步發，後者則是剛萌芽的新 repo。Web 探索因此刻意挑了 qm（multi-agent harness）與 trycompai/crm（agent-first CRM）—— 兩者都走「agent 為主、文件 / SaaS 為輔」的反主流方向。
- 上手門檻分三層：qm 與 trycompai/crm 都要自行拉 Postgres / 連 Vercel / 配 OAuth，devOps 成本明顯；TencentDB Agent Memory 只要 `git clone + ./start-all.sh` 就能起三個服務；reverse-skill 嚴格說不需要「裝」，是給 AI 客戶端讀 Markdown + 跑 skill 腳本；firecrawl/pdf-inspector 是五個 repo 裡唯一一條「裝 API 即可」的 production 級硬底函式庫。
- 語言生態集中在 TypeScript（TencentDB、qm、trycompai/crm）+ Rust（pdf-inspector）+ PowerShell（reverse-skill）。TypeScript 還是 2026 團隊級 agent 系統的事實標準；Rust 出現在確實有 perf 需求（PDF 解析）的底層；PowerShell 是 Windows / 滲透場景的「政策選擇」，不是主流偏好。
- 套件化方面，pdf-inspector 唯一三棲（pip / npm / cargo）+ WASM、TencentDB Agent Memory 走 npm scoped package + git deploy、qm 走 `npm exec` 派 `qm init` CLI、trycompai/crm 是 Bun monorepo 內跑、reverse-skill 完全沒有套件化——以「agent 為消費端」幾乎都用 Markdown + cloner 的非典型交付，反而 `pdf-inspector` 是少數傳統 library 化、AI 友善的解。
- 五個 repo 跨出三條明顯路線：(1) TencentDB + qm + trycompai/crm 把 agent 從「個人工具」升級成「團隊 / 組織基礎設施」，差別是記憶中樞、harness 平台、還是 CRM 業務單點；(2) reverse-skill 把整包 markdown + scripts 賣回給 AI 客戶端，證明「skill pack」確實是低成本分發手段；(3) pdf-inspector 守住傳統 library 戰場，把「不要每個文件都 OCR」這條具體優化變成可嵌入元件。對主人的意義：觀察重點不在五個 repo 本身，而是「AI 消費端」逐漸壓過「人類消費端」這個結構性轉變。
