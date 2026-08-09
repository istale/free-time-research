# GitHub 專案動態
- 檢查時間：2026-08-09（台北時間 14:00 巡邏）
- 檢查來源：Trending Top 3（PrimeIntellect-ai/prime-agent、addyosmani/agent-skills、TapXWorld/ChinaTextbook）+ 自由探索 2（genspark-ai/genoffice、deerwork-ai/deer-workflow）

## Repo 摘要與 3W1H

### `PrimeIntellect-ai/prime-agent`
- **Repo 摘要：** Prime Intellect 開源的「自我改進 RLM 編碼 agent」，圍繞兩個核心抽象 — Recursive Language Model（把 prompt 當變數、sub-agent 當函式呼叫）+ Continual Harness（把可重用 prompt / 記憶 / 技能當作可被小幅證據化更新的持久狀態）。強調長任務、背景 daemon、子 agent 之間直接通訊、IPython 持久 REPL，定位是「通用、長跑、可自我演化」的 coding & research agent。目前看起來適合研究型 / 實驗型開發者，或想探索 RLM 與 continual harness 概念的人。
- **3W1H：**
  - **What：** 一個 command-line 的 self-improving RLM（Recursive Language Model）agent，內建 Python 控制環境、子 agent 機制、技能系統、continual harness。
  - **Why：** 解決「長任務 chat agent 上下文流失、子任務串接困難、skill / memory 難以跨 session 累積」的痛點；近期因 Prime Intellect 在 RLM 概念發表後衝上 trending。
  - **Who：** AI 研究 / agent 框架研究者；願意接受「自己 review 變更 + 在 disposable clone 裡跑」的進階開發者。
  - **How：** 從 `app.primeintellect.ai/prime-agent/install.sh` 安裝 `prime-agent` CLI → 在目標目錄跑 `prime-agent` → 首次 `/login` 選訂閱或 API key；過程中可用 `/refine` 讓 harness 自己更新。
- **安裝方式：**
  - **官方 curl 安裝（macOS / Linux，推薦）：**
    ```bash
    curl -fsSL https://app.primeintellect.ai/prime-agent/install.sh | sh
    ```
    安裝器會下載帶版本號的 release、驗 SHA-256、安裝 `prime-agent` 指令、準備 IPython runtime。
  - 套件化（pip / npm）方式：未找到 pip / npm 安裝方式；本機僅走 release installer 或自行 clone repo dev build。
- **近期 release：**
  - **`v0.7.1`** — 發布日期 **2026-08-07**（published_at `2026-08-07T18:39:08Z`）。
  - 為修補型 release：修正 bundled `websearch` skill 描述與 Serper 設定說明漏掉的 `/login` → MCP Connections 步驟；修正 `retry_worker` 在 stopped session worker 留下 stop marker 時會取消自己的 recovery、把 session 卡在 "Session worker is not connected" 的 bug。
  - 提示：安裝檔內含安全警語 — agent 會以使用者權限執行模型產生的 Python 與 shell 指令，並非 sandbox，建議在 disposable clone / worktree 操作。

### `addyosmani/agent-skills`
- **Repo 摘要：** Addy Osmani 維護的「production-grade 工程技能」庫，把資深工程師的 workflow / quality gate / best practice 編成可讓 AI coding agent 穩定遵循的 skill（`/spec`、`/plan`、`/build`、`/test`、`/review`、`/ship` 等八個 slash command 對應開發生命週期）。同時是 Skills 生態中最熱門的 repository，topics 涵蓋 Claude Code、Cursor、Codex、Antigravity。適合：想用現成 skill 把 AI agent 拉到「資深工程師」水準的個人或團隊。
- **3W1H：**
  - **What：** 一組打包好的 agent skill + slash command + persona collection，可被 Claude Code / Cursor / Codex / Antigravity 等 70+ agent 安裝。
  - **Why：** 解決「AI agent 寫程式常見的低品質、缺測試、跳步、不 review」問題；藉 8 個 slash command 把 SDLC 拆成可驗證的階段。
  - **Who：** 用 Claude Code / Cursor / Codex / Antigravity 的工程師；想統一團隊 AI agent 行為的 tech lead。
  - **How：** 直接 `npx skills add addyosmani/agent-skills` 安裝全部 24 個 skill，或用 `--skill <name>` 單裝；Claude Code 用戶另走 `/plugin marketplace add addyosmani/agent-skills` + `/plugin install agent-skills@addy-agent-skills`。
- **安裝方式：**
  - **npx（任意 agent，推薦最快）：**
    ```bash
    npx skills add addyosmani/agent-skills            # 一次裝全部 24 個 skill
    npx skills add addyosmani/agent-skills --list     # 先看再裝
    npx skills add addyosmani/agent-skills --skill code-review-and-quality
    ```
  - **Claude Code（marketplace）：**
    ```bash
    /plugin marketplace add addyosmani/agent-skills
    /plugin install agent-skills@addy-agent-skills
    ```
  - **Antigravity CLI：** `agy plugin install https://github.com/addyosmani/agent-skills.git`
  - **Gemini CLI：** `gemini skills install https://github.com/addyosmani/agent-skills.git --path skills`
  - **本機開發：**
    ```bash
    git clone https://github.com/addyosmani/agent-skills.git
    claude --plugin-dir /path/to/agent-skills
    ```
  - 套件化（pip）方式：未找到 pip 安裝方式；走 npx / 各 agent 內建 plugin installer。
- **近期 release：**
  - **`0.6.6`** — 發布日期 **2026-08-04**（published_at `2026-08-04T04:08:32Z`）。
  - 主要是 fix release：恢復 Claude Code plugin 上沒被載入的四個 persona（`code-reviewer`、`security-auditor`、`test-engineer`、`web-performance-auditor`），原因是 `.claude-plugin/plugin.json` 內顯式 `agents` 陣列壓制了 Claude Code 自己對 `agents/*.md` 的 discovery（#449, 由 @ingnicolaboccato-lab 回報）；同時收尾 ecosystem-neutral pass 與 performance-optimization workflow。
  - README 註記：單 skill 經 `npx skills add` 安裝時只會複製 `skills/<name>/`，不會帶 repo-level `references/`，需要整套整合或自行 clone 才能用 shared checklist（追蹤於 issue #361）。

### `TapXWorld/ChinaTextbook`
- **Repo 摘要：** 一個把中國小學、初中、高中到大學 PDF 教材集中開源的 repository，作者動機是「打破有人把帶水印免費教材轉賣的現況」以及「讓海外華人孩子能持續接觸國內教材」。內容以人教版義務教育教科書為主，依年級 / 學期 / 學科組織為 PDF 直連。適合：在海外想自學中文課程內容的家庭、需要查驗中國義務教育教材內容的人、研究 / 翻譯者。
- **3W1H：**
  - **What：** 一個大型教材檔案倉庫，把人教版中小學到大學 PDF 教材依年級 / 學期 / 學科整理好。
  - **Why：** 解決「免費教材被人加私水印轉售、偏鄉難以取得」與「海外華人缺少國內同步教材」的痛點；純粹公益導向。
  - **Who：** 海外華人家庭；自學 / 補習需求者；對中國課程有興趣的研究者。
  - **How：** 直接到 GitHub repo 依 README 的「小學 / 初中 / 高中 / 大學」目錄點擊 PDF 連結下載，無 install、無 CLI、無 build。
- **安裝方式：**
  - 未找到 pip / npm 安裝方式；沒有套件化或 build 流程。
  - **使用方式：** 直接瀏覽 README / 倉庫目錄，下載對應年級 / 學科 / 學期的 PDF 即可（連結走 GitHub `blob/master/`）。
- **近期 release：**
  - 未找到 GitHub release（repo 沒有任何 GitHub release 標籤）。
  - 補充活躍度：最近 push 為 `2025-10-18`，距今已逾 9 個月沒新增 commit；trending 熱度主要來自星數累積與教材被引用，非近期活躍開發。

### `genspark-ai/genoffice`
- **Repo 摘要：** GenSpark 開源的「AI-native office suite」，六個 Electron app（Docs / Sheets / Slides / PDF / shell / Markdown）共用一個引擎層，圍繞「AI 編輯為一級 workflow」而非附加 chat。強調 byte-preserving docx round trip、自家 Rust sidecar 處理 xlsx、HarfBuzz text shaping、pdf.js + pdf-lib 編輯 PDF。Linux / macOS / Windows 都有 signed installer。適合：想要開源、AI 編輯能力原生整合、不想依賴 MS Office 的使用者或團隊。
- **3W1H：**
  - **What：** 以 Electron 為基礎、整合 AI editing 的開源 office 套件（Docs / Sheets / Slides / PDF / shell / Markdown）。
  - **Why：** 解決「現有 office 工具 AI 功能都是外掛 chat box、且多為閉源」問題；同時 docx / xlsx / pptx / pdf 的 format 處理強調 byte-preserving round trip，不會因為「開了又存」就壞 layout。
  - **Who：** 想脫離 MS Office / Google Docs 鎖定、需要原生 AI 編輯的個人或企業；想研究開源 office engine 的開發者。
  - **How：** 到 Release 頁下載對應 OS installer，安裝即用；AI 功能透過 suite shell 內建；Linux 用戶走 deb 或 AppImage。
- **安裝方式：**
  - **macOS（Apple Silicon，簽章）：**
    ```
    GenOffice-0.5.83-arm64.dmg
    ```
  - **Windows（x64，簽章）：**
    ```
    GenOfficeSetup-v0.5.79.exe
    ```
  - **Linux Debian / Ubuntu：**
    ```bash
    sudo apt install ./genoffice_0.5.149_amd64.deb
    ```
  - **Linux 其他發行版（AppImage，需要 FUSE 2）：**
    ```bash
    sudo apt install libfuse2            # Ubuntu 22.04 之前
    sudo apt install libfuse2t64          # Ubuntu 24.04 之後
    chmod +x GenOffice-0.5.149.AppImage
    ./GenOffice-0.5.149.AppImage
    ```
  - 套件化（pip / npm）方式：未找到 pip / npm 安裝方式；走 OS 專屬 installer / deb / AppImage。
- **近期 release：**
  - **`GenOffice Linux v0.5.149`** — 發布日期 **2026-08-06**（published_at `2026-08-06T06:22:53Z`）。
  - 此 release 是 Linux（x64）專屬 build；macOS / Windows 仍以 `v0.5.83` / `v0.5.79` 為主（release note 用同一份 changelog 帶 macOS/Windows 連結）。
  - 注意：README 下載表同時列出 `v0.5.83` (macOS) 與 `linux-v0.5.149` (Linux)，三平台版本號目前並未完全同步。

### `deerwork-ai/deer-workflow`
- **Repo 摘要：** 由 `bytedance/deer-flow` 衍生的「DeerFlow 3.0 試點」— DeerWork。開源 dynamic workflow runtime，把「流程控制」留在可 review 的 TypeScript，把「語意工作」下放給可替換的 agent runtime（預設 Codex CLI，內建 Claude Code 支援）。主打「code is the plan」、agent 可替換、執行可觀察（互動 TUI / JSONL 事件流）。適合：想用 TypeScript 寫 workflow + AI agent 跑 node 的工程師；喜歡「deterministic 框架 + LLM 局部化」設計哲學的團隊。
- **3W1H：**
  - **What：** 一個 TypeScript 的 dynamic workflow runtime，CLI 為 `deer-workflow`；發布到 npm `@deerwork-ai/deer-workflow`，執行需要 Bun + Codex CLI。
  - **Why：** 解決「整個 workflow 寫死在 agent 對話裡、難以 review / 測試 / 替換底層模型」的問題；用 TypeScript graph 守住控制流，agent 變成可熱插拔的節點。
  - **Who：** 喜歡 code-first / graph engineering 風格的工程師；CI / 排程 / 報告類長任務的 builder。
  - **How：** 安裝 Bun → 安裝 Codex CLI → `bun install --global @deerwork-ai/deer-workflow` → 用 `deer-workflow create "<描述>" > workflow.ts` 讓 workflow-creator skill 自動生成 workflow 模組 → `deer-workflow run ./workflow.ts --input '{...}'` 執行（互動用 TUI，CI 用 `--print` / `-p` 吐 JSONL）。
- **安裝方式：**
  - **npm / Bun（推薦，套件已發布）：**
    ```bash
    bun install --global @deerwork-ai/deer-workflow
    ```
  - 前置：裝 [Bun](https://bun.sh) 並登入 [Codex CLI](https://github.com/openai/codex)。
  - **範例執行：**
    ```bash
    deer-workflow create "Create a Workflow that accepts a topics string array, researches each topic in parallel, and synthesizes a report" > workflow.ts
    deer-workflow run ./workflow.ts --input '{"topics":["Agent Skills","Dynamic Workflows"]}'
    ```
  - **本地開發：** clone repo 後依 `How to develop / Set up` 安裝相依並掛 Git hooks（README 未列簡短指令，需讀 [README.zh-CN.md](./README.zh-CN.md) 或 docs）。
  - 套件化（pip）方式：未找到 pip 安裝方式；走 npm / Bun。
- **近期 release：**
  - **`v0.2.0`** — 發布日期 **2026-07-27**（published_at `2026-07-27T11:11:05Z`）。
  - 透過 PR #3（@Kelvinz-89757）新增 `ClaudeAgent`：以 Claude Code CLI 作為另一個可熱插拔 runtime；從 `v0.1.0` 直接跳到 `v0.2.0`，專案仍處早期。

## 重點觀察
- **Release 新鮮度差異大：** `prime-agent` v0.7.1（08-07）、`genoffice` linux-v0.5.149（08-06）都在過去 72 小時內；`agent-skills` 0.6.6（08-04）也在一週內；`deer-workflow` v0.2.0（07-27）稍早；`ChinaTextbook` 完全沒 release 且 main branch 已 9 個月沒 push — 它的 trending 熱度純粹是星數累積而非開發活動。
- **安裝/上手門檻與生態高度分化：** 只有 `deer-workflow` 同時有 npm + Bun 套件化（`@deerwork-ai/deer-workflow`），可一鍵 `bun install --global`；`agent-skills` 走 `npx skills add` + 各 agent 原生 plugin installer；`prime-agent` 用自家 curl install script；`genoffice` 走 OS 專屬 installer（dmg / exe / deb / AppImage）；`ChinaTextbook` 完全沒有 installable form。這份報告裡「可一鍵裝起來跑」的只有 `deer-workflow` 與 `agent-skills`。
- **語言生態：** 5 個裡有 4 個主語言是 TypeScript / JavaScript（prime-agent、agent-skills、genoffice、deer-workflow），剩 1 個是 Roff（ChinaTextbook — 主要是 PDF / 文字檔）；可見目前 agent / AI 工具類新專案幾乎完全壓在 TS / JS 生態。Python 缺席這份清單是個值得注意的訊號。
- **AI agent 與 skill 生態成主流主題：** 5 個 repo 裡 3 個（prime-agent、agent-skills、deer-workflow）圍繞「AI agent 編碼 / 編排」，1 個（genoffice）主打「AI-native office editing」；只有 ChinaTextbook 完全不相關，顯示 2026-08 這波 trending 與自由探索都集中在 agent / AI 工具這一塊。
- **是否有 pip / npm 套件化的總結：** 5 個中只有 `deer-workflow` 有正式 npm / Bun 套件（`@deerwork-ai/deer-workflow`）；`agent-skills` 走 `npx skills add` 雖然也是 JS 生態但屬 CLI installer，不算標準 npm package；其他 3 個（prime-agent、genoffice、ChinaTextbook）都沒有 pip / npm 套件化，分別採 curl install script / OS installer / 純檔案倉庫。整套看下來，「agent / workflow runtime 類」比「application / content 類」更傾向有套件化發布。