---
title: GitHub 自由探索 2026-09-06（14:00 台北時間）
date: 2026-09-06
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 5 天 repeat）
  - Web search（okf-memory/okf-agent-memory + ifm-ai/uno — LavX News 2026-09-06 報導 Git-native agent memory；IFM K2 Horizon 官方 blog 2026-09-03 + Reuters/AiCybr 多家報導的 Apache-2.0 全開源 fleet 附屬推論加速專案）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md`
---

# GitHub 專案動態

- 檢查時間：2026-09-06（14:00 台北時間）
- 檢查對象：`cathrynlavery/diagram-design` / `ruvnet/ruflo` / `humanlayer/skills` / `okf-memory/okf-agent-memory` / `ifm-ai/uno`
- 來源組合：GitHub Trending today Tier-A 排除前 5 天 repeat（**fresh top 3 = `cathrynlavery/diagram-design` rank 7（editorial diagram agent skill，855★/day）+ `ruvnet/ruflo` rank 9（agent meta-harness，136★/day）+ `humanlayer/skills` rank 10（engineering skill pack，442★/day）— 排除 `mattpocock/skills`（rank 1，09-01/09-04/09-05 repeat）+ `affaan-m/ECC`（rank 2，09-01/09-03/09-04 repeat）+ `DietrichGebert/ponytail`（rank 3，09-01/09-04 repeat）+ `NousResearch/hermes-agent`（rank 4，09-04 repeat）+ `fmtlib/fmt`（rank 5，09-04 repeat）+ `anthropics/skills`（rank 6，09-04 repeat）+ `anomalyco/opencode`（rank 8，09-05 repeat）+ `blader/humanizer`（rank 11，09-05 repeat）+ `magnitudedev/magnitude`（rank 14，09-05 repeat）**）+ Web search 2 = `okf-memory/okf-agent-memory`（LavX News 2026-09-06 報導，**建立於 2026-09-05、當天發 v0.1.0** 的 Git-native agent memory，Go / MIT / zero-dep，宣稱 BM25 檢索 <300µs 對比 vector DB 150-800ms）+ `ifm-ai/uno`（IFM K2 Horizon 2026-09-03 全開源 fleet 的推論加速姊妹專案，Apache-2.0 / Python，discrete diffusion 無損加速，附 K2-Horizon-7B/0.9B Uno checkpoint）。

---

## Repo 摘要與 3W1H

### `cathrynlavery/diagram-design`

- **Repo 摘要：** 一個以 agent skill 形式散佈的「編輯級圖表產生器」，提供 38 種 editorial diagram 類型，輸出自給自足的 HTML + SVG。README 的核心主張是反 Mermaid：「No shadows. No Mermaid slop.」——不用 Mermaid 那套通用但視覺粗糙的語法，而是給 LLM 一組有設計品味的圖表模板。31,836★、HTML 為主語言、MIT，適合需要把 agent 產出的架構圖／流程圖直接放進文件或簡報，而不想再手動修版的人。
- **3W1H：**
  - **What：** agent skill / plugin（非 CLI、非 library），內容是 38 種圖表類型的 SKILL.md + HTML/SVG 模板 + 匯出腳本。
  - **Why：** LLM 畫圖的預設路徑是 Mermaid，但 Mermaid 的排版與視覺品質對正式文件不夠用；這個 skill 把「圖表設計」下放成可由 agent 直接套用的成品模板，並支援 PNG 匯出，補上 agent 文件產出的最後一哩。
  - **Who：** 用 Claude Code / Codex / Pi / Factory Droid / Kiro / OpenCode 寫技術文件、架構說明、產品簡報的人；以及像主人這樣重視「畫面設計要讓人理解」的視覺優先工作流。
  - **How：** 依 agent host 各自的 marketplace 指令安裝，之後 agent 在遇到圖表請求時自動套用，或用 `/skill:diagram-design` 顯式呼叫；PNG 匯出走 Playwright。
- **安裝方式：**
  - **plugin marketplace 多 host 安裝（install type-20）** — 每個 host 一段指令：
    - Claude Code：`/plugin marketplace add cathrynlavery/diagram-design` → `/plugin install diagram-design@diagram-design`（之後要在 `/plugin` → Marketplaces 手動開 Enable auto-update，Claude Code 對第三方 marketplace 預設關閉自動更新）
    - Codex：`codex plugin marketplace add cathrynlavery/diagram-design` → `codex plugin add diagram-design@diagram-design`
    - Factory Droid：`droid plugin marketplace add https://github.com/cathrynlavery/diagram-design` → `droid plugin install diagram-design@diagram-design --scope user`
    - **Pi：`pi install https://github.com/cathrynlavery/diagram-design`**（unpinned git install，需 `pi update --extensions` 拉更新）
    - Claude Cowork：需先把 public repo 鏡射到 org 私有 repo 才能加為 organization marketplace
    - OpenCode：手動 copy/symlink `skills/diagram-design/` 到 `.opencode/skills/`
  - **pip（附屬相依）：`pip install playwright && playwright install chromium`** — 只在需要 PNG 匯出（2× 光柵化）時才要。
  - 開發／編輯用：`git clone git@github.com:cathrynlavery/diagram-design.git ~/code/diagram-design`（README 有 editable install 段）。
- **近期 release：** **未找到 GitHub release**（`/releases/latest` 回 404）。版本靠 plugin manifest 的 display version + marketplace commit 追蹤；活躍度以 `pushed_at 2026-09-03` 判讀（3 天內）。

---

### `ruvnet/ruflo`

- **Repo 摘要：** 自稱「the original agent meta-harness」的多代理編排框架，把 swarm 部署、autonomous workflow 協調、對話式 AI 系統組裝包成一套 TypeScript CLI + MCP server + Claude Code plugin 家族。70,745★、8,422 fork、541 MB repo size，是今天 5 個 pick 裡體量最大的。功能面涵蓋 adaptive memory、self-learning intelligence、RAG 整合，以及跨 agent host 的 federation（`federation init` / `join wss://...` / `send --to`）。適合已經在跑多代理、需要把 swarm 協調與記憶層外包給現成框架的人。
- **3W1H：**
  - **What：** meta-harness / framework（npm 套件 `ruflo` + 一組 Claude Code plugin + MCP server）。
  - **Why：** 多代理系統的難點不在單一 agent，而在 swarm 協調、跨 session 記憶、跨機器 federation；ruflo 把這三件事做成可安裝的預設實作，而不是讓每個人自己重寫 orchestrator。
  - **Who：** 要做 multi-agent swarm、agent 之間互相派工、跨主機協作的開發者——與主人現行「default 協調 + executor + reviewer」的 Kanban 多代理路線同一個問題領域。
  - **How：** 最短路徑是 `npx ruflo@latest init wizard` 走互動式設定；要常駐就 `npm install -g ruflo@latest`；要在 Claude Code 內用就走 `/plugin install ruflo-core@ruflo` 等分包。
- **安裝方式：**
  - **npm / npx（多路徑）：**
    - `npx ruflo@latest init wizard`（互動式，最短上手）
    - **`npm install -g ruflo@latest`**（全域 CLI）
    - MCP 掛載：`claude mcp add claude-flow -- npx ruflo@latest mcp start`
  - **`curl | bash` 一行安裝：** `curl -fsSL https://cdn.jsdelivr.net/gh/ruvnet/ruflo@main/scripts/install.sh | bash`
  - **plugin 分包安裝（Claude Code）：** `/plugin install ruflo-core@ruflo`、`ruflo-swarm@ruflo`、`ruflo-rag-memory@ruflo`、`ruflo-neural-trader@ruflo`、`ruflo-federation@ruflo`
  - 備註：README 描述欄寫「native Claude Code / Codex / Hermes and many more Integrated」，但 README 正文（30 KB）grep `hermes` 為 0 命中 — **Hermes 支援只出現在 repo description，README 內沒有對應的安裝段**，要用的話得自己驗證。
- **近期 release：** **`v3.38.21` — 「v3.38.21 — MCP HTTP bridge memory-persistence fix」，published 2026-09-02**（4 天前）。今天 5 個 pick 裡唯一有成熟 SemVer 序列的專案。

---

### `humanlayer/skills`

- **Repo 摘要：** HumanLayer 團隊釋出的工程用 agent skill 集合，repo 只有 66 KB、README 1.5 KB，沒有 description、沒有 topics——是刻意極簡的 skill pack。2,805★、442★/day 的增速說明關注度來自內容本身而非包裝。收錄的 skill 名稱透露了取向：`improve-claude-md`、`narrow-react-prop-types`、`build-iterated-agentic-loop`、`design-control-loop`、`show-me` — 一半是前端／型別收斂，一半是 agent control loop 設計。適合想要「別人驗證過的 agent 工作習慣」而非通用 prompt 模板的人。
- **3W1H：**
  - **What：** agent skill 集合（TypeScript 為主語言，但實質是 SKILL.md 內容庫），無 CLI、無 npm 套件。
  - **Why：** skill 生態現在的問題是量太多、品質參差；HumanLayer 是做 human-in-the-loop agent 基礎設施的團隊，他們自己 `.agents` 目錄裡留下來的 skill 帶有選擇壓力，這是它的價值主張。`build-iterated-agentic-loop` / `design-control-loop` 兩個 skill 名稱直接對應「agent 迴圈怎麼設計」這個主人也在做的題目。
  - **Who：** Claude Code / Codex 使用者中，寫 React/TypeScript 或在設計 agent control loop 的人。
  - **How：** 逐個 skill 安裝（不是整包），用 `npx skills add humanlayer/skills --skill <NAME>`。
- **安裝方式：**
  - **npx（agentskills.io 標準）：`npx skills add humanlayer/skills --skill SKILLNAME`** — 逐 skill 取用，例如：
    - `npx skills add humanlayer/skills --skill improve-claude-md`
    - `npx skills add humanlayer/skills --skill build-iterated-agentic-loop`
    - `npx skills add humanlayer/skills --skill design-control-loop`
    - `npx skills add humanlayer/skills --skill narrow-react-prop-types`
    - `npx skills add humanlayer/skills --skill show-me`
  - 未提供 pip / npm library / brew 路徑；沒有 CLI binary 產出。
- **近期 release：** **未找到 GitHub release**（404）。活躍度以 `pushed_at 2026-08-13` 判讀——**24 天沒動**，是今天 5 個 pick 裡最舊的推送；星數卻在今天衝 442★/day，屬於「內容穩定、關注度後補」型。

---

### `okf-memory/okf-agent-memory`

- **Repo 摘要：** 把 AI coding agent 的持久記憶做成「純 Git + Markdown」的 Go 工具，實作 Google Open Knowledge Format (OKF) v0.2。記憶存成 repo 內 `knowledge/` 目錄下的 Markdown + YAML frontmatter，檢索走 in-memory BM25（宣稱 <300µs，對比 embedding vector DB 的 150-800ms），沒有外部資料庫、沒有 embedding API 成本、沒有相依套件。**repo 建立於 2026-09-05 21:26 UTC，v0.1.0 在 42 分鐘後發佈** — 是今天最新鮮的 pick（132★、7 fork、MIT、Go 1.26 zero-deps）。適合受夠了「每個新 session 都要重新解釋架構」又不想接 vector DB 的人。
- **3W1H：**
  - **What：** Go CLI + library + 內建 MCP server（`okf mcp`），加上一份 OKF v0.2 格式規範的實作。
  - **Why：** 目前 agent 記憶只有兩個極端——非結構化的 `CLAUDE.md` / `AGENTS.md` 單檔（會膨脹、會 memory rot），或黑箱 vector DB（有成本、不可 `git diff`）。OKF 走中間路線：結構化但仍是純文字，帶 provenance（`sources`）、trust tier（`generated` vs `verified`）、lifecycle metadata（`status`、`stale_after`），並用 progressive disclosure（層級式 `index.md` + link graph）讓 agent 只載入需要的概念，宣稱可省 80% token。
  - **Who：** 跨 session 工作的 agent 使用者；特別是需要 audit trail（記憶改了什麼、誰改的、何時改的都用 `git log` 看）與 air-gapped 部署（零外部依賴）的人 — 對主人的 downstream / 離線場景幾乎是量身打造的形狀。
  - **How：** clone → `make build` 產出 `bin/okf` → `./bin/okf bootstrap /path/to/project --name "My Project"` 把整套記憶結構灌進目標專案 → agent 端透過 stdio 掛 `okf mcp`。日常操作是 `okf validate --strict --drift` / `okf search "<query>" knowledge` / `okf show <concept> --json` / `okf create` / `okf update`。
- **安裝方式：**
  - **未找到明確 pip/npm 安裝方式** — 沒有 PyPI、沒有 npm、沒有 brew、沒有 `go install` 指令寫在 README。
  - **可行路徑（source-only + make build）：**
    ```bash
    git clone https://github.com/okf-memory/okf-agent-memory
    cd okf-agent-memory
    make build          # 產出 bin/okf，Go 1.26，zero external deps
    ./bin/okf bootstrap /path/to/project --name "My Project"
    ```
  - MCP 掛載走 stdio 設定區塊（README 有範例），host 端對應 Claude Code / Cursor / Codex。
- **近期 release：** **`v0.1.0` — 「OKF Agent Memory v0.1.0」，published 2026-09-05 22:08 UTC**（不到 1 天前，也就是台北時間今天凌晨）。今天 5 個 pick 裡最新鮮的 release，且是 repo 建立當天就發首版。

---

### `ifm-ai/uno`

- **Repo 摘要：** IFM（Institute of Foundation Models，MBZUAI 旗下）在 2026-09-03 發佈 K2 Horizon 全開源 fleet 的同時釋出的推論加速專案，題目是「Unlocking Lossless Speedups in LLMs via Discrete Diffusion」——用離散擴散做**無損**（lossless）解碼加速，而非常見的有損近似。Apache-2.0、Python、772 KB、35★（發佈 3 天，星數還在起步），附上 K2-Horizon-7B / 0.9B 的 Uno 權重與一組 Qwen3-8B adapter。適合做推論最佳化研究、想在自家模型上複現 speculative/diffusion 解碼的人。
- **3W1H：**
  - **What：** research codebase（`nano-vllm-uno` 環境名暗示是 nano-vLLM 分支改造），提供訓練（`.[train]`）與評測（`.[eval]`）兩套 extras。
  - **Why：** K2 Horizon 這波開源的差異點是「連訓練資料、recipe、intermediate checkpoint、訓練 log 都放」——Uno 是這條 open-science 路線在推論側的延伸：不只給你模型，還給你把它跑快的方法與可複現的權重。無損加速的價值在於不用在 latency 與品質之間做取捨。
  - **Who：** 推論最佳化研究者、要在受限硬體上跑本地模型的人；相對地**不適合只想「裝起來就用」的人**（下面安裝段會看到為什麼）。
  - **How：** conda 建 Python 3.10 環境 → 裝 CUDA 12.8 版 torch 2.11.0 → 裝預編譯 FlashAttention-2 wheel → `pip install -e '.[eval,train]'`；要跑 tree verification 還得自己編 FlashAttention-3（hopper 分支）。之後從 HuggingFace 拉 Uno checkpoint。
- **安裝方式：**
  - **pip（editable，非 PyPI 套件）：**
    ```bash
    conda create -n nano-vllm-uno python=3.10 pip -y
    conda activate nano-vllm-uno
    python -m pip install --upgrade pip
    python -m pip install torch==2.11.0 --index-url https://download.pytorch.org/whl/cu128
    python -m pip install 'https://github.com/lesj0610/flash-attention/releases/download/v2.8.3-cu12-torch2.11/flash_attn-2.8.3%2Bcu12torch2.11cxx11abiTRUE-cp310-cp310-linux_x86_64.whl'
    python -m pip install -e '.[eval,train]'   # FA2 即可跑 linear decoding
    ```
  - **FlashAttention-3（tree verification 才需要，要自行編譯）：**
    ```bash
    python -m pip install ninja==1.13.0
    git clone --depth 1 --branch v2.8.3 https://github.com/Dao-AILab/flash-attention.git
    cd flash-attention/hopper
    MAX_JOBS=16 python -m pip install --no-build-isolation .
    ```
  - **注意：沒有 PyPI 發佈**（`pip install uno` 不存在），且相依鎖到 CUDA 12.8 + torch 2.11.0 + linux_x86_64 wheel — **在主人這台 Apple M4 Max VM（16 GB、無 NVIDIA）上無法直接安裝**，屬於「讀 code 與看數字」而非「裝起來用」的 pick。
  - Checkpoint：公開權重不需 HF token；GPQA 等 gated dataset 才需要。
- **近期 release：** **未找到 GitHub release**（404）。版本以 repo 建立日 `created_at 2026-09-03`、最後推送 `pushed_at 2026-09-04` 判讀——與 K2 Horizon 官方 blog（2026-09-03）同日開張，2 天內仍在推送。

---

## 重點觀察

- **Release 新鮮度呈兩極分化，且與星數增速完全脫鉤。** 5 個 pick 只有 2 個有 GitHub release：`okf-agent-memory` v0.1.0（**不到 1 天**，repo 建立當天就發版）與 `ruflo` v3.38.21（4 天前，唯一有成熟 SemVer 序列的）。另外 3 個是 404 = **60% zero-release**，正好落在 playbook 記錄的 baseline 上。有意思的是最舊的 `humanlayer/skills`（`pushed_at` 24 天前）今天卻衝 442★/day——**skill pack 的星數來自內容被發現，不是來自版本節奏**；反過來 `ifm-ai/uno` 有頂級機構背書、2 天內還在推 commit，星數只有 35。判讀 skill/研究型 repo 的活躍度要看 `pushed_at` 或母專案事件，看 release tag 會嚴重誤判。
- **安裝門檻跨了 3 個數量級，且與「這東西給誰用」完全對應。** 最低是 `humanlayer/skills` 一行 `npx skills add ...`（單一 install type、逐 skill 取用）；中間是 `okf-agent-memory` 的 `git clone` + `make build`（Go zero-deps，一行編譯出可攜 binary）；最高是 `ifm-ai/uno`——conda + CUDA 12.8 專版 torch + 預編譯 FA2 wheel + 自行編譯 FA3，且鎖 `linux_x86_64`，**在主人這台 M4 Max VM 上根本裝不起來**。install type 覆蓋 5 種零重複：type-20（diagram-design 多 host plugin marketplace，7 個 host 各一段指令）、npm/npx 多路徑 + `curl|bash` + plugin 分包（ruflo）、npx skills add（humanlayer）、source-only + make build（okf）、conda + editable pip + 自編 CUDA kernel（uno）。
- **License 全乾淨：4 MIT + 1 Apache-2.0，0 copyleft、0 NOASSERTION。** `ifm-ai/uno` 是 case-A2（Apache-2.0 帶明示專利授權，比 MIT 多一層保護），其餘 4 個是 case-A。對主人 `horo-agent` / `horo-webui` downstream 是 **0 license friction**；其中 `okf-agent-memory`（MIT + Go zero external deps + 純文字儲存 + 無外部 DB）在 air-gapped 場景幾乎沒有法務或部署阻力，是 5 個裡最能直接嵌的形狀。
- **語言生態繼續分裂成「agent 層 = TS/HTML/Shell、substrate 層 = Go/Python」。** Trending fresh top 3 是 HTML + TypeScript + TypeScript，全是 agent skill / harness 層；兩個 web-search pick 是 Go + Python，全是 substrate 層（記憶引擎、推論加速）。今天特別值得注意的是 **Go 出現在 agent 記憶層** — `okf-agent-memory` 選 Go 而非 Python 的理由寫在數字裡（<4ms 冷啟動、15 MB 記憶體、單一 binary、zero deps），這正是「agent 週邊工具開始要求 CLI 級啟動成本」的訊號，Python 在這個位置的冷啟動吃不下來。
- **Hermes 訊號今天是「description 有、README 沒有」的假陽性，值得記一筆。** `ruvnet/ruflo` 的 repo description 明寫「native Claude Code / Codex / Hermes and many more Integrated」，但 30 KB 的 README grep `hermes` 是 **0 命中**，也沒有對應的安裝段；`diagram-design` README 有 1 次 hermes 命中但不在 Install 區塊。**operational lesson：Hermes 支援訊號只認 README Install 段的實際指令，repo description 的 host 清單是行銷文案，不能當支援憑證。** 今天 5/5 都沒有可驗證的 Hermes 安裝路徑 — agent host 主流化仍在推進（diagram-design 一次列 7 個 host），但 Hermes 的第一方 badge 這一輪缺席。
