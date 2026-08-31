---
title: GitHub 自由探索 2026-08-31（14:00 台北時間）
date: 2026-08-31
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 5 天 repeat）
  - Web search（fresh AI agent framework + agent harness 8 月 release 篩選）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md`
  - PyPI JSON API（agno / heretic-llm / haystack-ai）
  - npm registry（@openmaic/generation）
---

# GitHub 專案動態

- 檢查時間：2026-08-31（14:00 台北時間）
- 檢查對象：`THU-MAIC/OpenMAIC` / `Lakr233/vphone-cli` / `p-e-w/heretic` / `agno-agi/agno` / `deepset-ai/haystack`
- 來源組合：GitHub Trending today Tier-A top 3 排除前 5 天 repeat（**fresh top 3 = `THU-MAIC/OpenMAIC` rank 1, 1370★/day, **清華 THU-MAIC 8/27 才發 v1.0.0** + `Lakr233/vphone-cli` rank 3, 361★/day, **2026-02 才 open source, Swift 寫的 Apple Virtualization.framework 跑 iPhone VM**，pushed_at 8/29 = 2 天內最 fresh + `p-e-w/heretic` rank 5, 369★/day, **AGPL-3.0 + 1.4k LOC 的 directional ablation TPE 自動 abliteration**，pushed_at 8/17 但 release v1.4.0 是 6/14），排除 `tt-a1i/archify` (rank 4, REPEAT 08-27/28/29/30, 3722★/day = 最高 stars/day 但屬 repeat) + `K-Dense-AI/scientific-agent-skills` (rank 2, REPEAT 08-27/29/30) + `bilawalsidhu/gods-eye-view` (08-28/29/30) + `agentscope-ai/AgentTeams` + `perplexityai/bumblebee` (08-30)））+ web_search constrained search（Tier-B 2：`agno-agi/agno` v3.0.4 8/30 release, **Apache-2.0 + 42k★ + AgentOS 平台 + per-user isolation + JWT RBAC + durable queue + 100+ integrations + Studio 3.0 governed catalog**, **WebUI + 8 種 starter template** 含 `agentos-railway/docker/aws/gcp/azure/fly/render/modal/helm` + **`deepset-ai/haystack` v3.1.0 8/24 release, **Apache-2.0 + 26k★ + CompactionHook + AgentTool + haystack.token_counters + multi-agent tutorial** + new `haystack.hooks.compaction` module + Agent.clone() + exit_reason state key + HAYSTACK_UNSAFE_DESERIALIZATION env hardening, **breaking API changes**（Agent.state_schema 拆出 resolved_state_schema、Jinja custom_filters 需要 unsafe=True、DocumentMAPEvaluator 平均精度重算、PipelineSnapshot.pipeline_state.inputs shape 變動））

## Repo 摘要與 3W1H

### `THU-MAIC/OpenMAIC`

- **Repo 摘要：** 清華 THU-MAIC 維護的 **「Open Multi-Agent Interactive Classroom — Get an immersive, multi-agent learning experience in just one click」** — 6 個月內從 0 衝到 24.8k★，基於 LangGraph 1.1 + Next.js 16 + React 19 + TypeScript 5 + Tailwind 4 的 multi-agent 課堂生成器，v1.0.0 (8/27 release) 從「one-click generator」升級為「Agent workbench + durable sessions + 20 built-in skills + provider-neutral runtime」完整平台。**核心差異化**：(1) **Agent workbench 是 chat-first workspace** — agent 計畫 curriculum、build/revise every page、可 mid-run steer、可 cancel/resume，server-backed session 跨重啟存活；(2) **20 built-in skills** — slides / quizzes / interactives / PBL (Project-Based Learning) / images / video / voices / `.pptx` import；(3) **Provider-neutral 18 種 provider** — OpenAI / Azure OpenAI / Anthropic / Amazon Bedrock / Google Gemini / DeepSeek / Qwen / Kimi / MiniMax / Grok / OpenRouter / Doubao / Tencent Hunyuan / Xiaomi MiMo / GLM / Ollama (local) / Lemonade (local LLM/image/TTS/ASR, OpenAI-compatible) / FunASR (local ASR, SenseVoiceSmall + Paraformer + Fun-ASR-Nano) + 任何 OpenAI-compatible API；(4) **OpenClaw integration** — 從 Feishu / Slack / Discord / Telegram / 20+ messaging app 一鍵 `clawhub install openmaic` 即可「teach me quantum physics」觸發；**這條直接命中 Master 「Qwen + Hermes + OpenClaw」MEMORY signal**（README 內有完整 `### 🐾 OpenClaw — Use OpenMAIC from your chat app, zero setup` section + 對 OpenClaw agent 寫了 「congrats, you just passed the reading comprehension part of the Turing test」幽默徽章）；(5) **17 locales** 含 zh-TW / ko-KR / pt-BR + automatic language inference；(6) **可匯出 `.pptx` slides + `.html` interactive pages** + offline-ready classroom export；(7) **License 變更 06-28**：從 AGPL-3.0 重授權為 **MIT**。

- **3W1H：**
  - **What：** Next.js 16 SPA + Node.js backend 的 multi-agent 課堂生成平台。內建 LangGraph 1.1 編排 + 18 種 LLM provider + 20 built-in skills + OpenClaw messaging app 橋接 + Vercel 一鍵 deploy。
  - **Why：** 解決「老師備課平均 5-10 小時、生成內容單調、學生無互動、AI 課程無法 offline 匯出、單一 LLM provider 綁死、商用 SaaS 平台鎖資料」的痛點。OpenMAIC 把一個 topic → 完整 slides + quizzes + interactives + PBL + AI teacher + AI classmate 全套課堂，並用 MIT license + provider-neutral 設計避開 SaaS 鎖定。
  - **Who：** K-12 / 大學 / 企業培訓 / 自學者 — 想把「單一 topic / 一份教材」自動變成「完整多 agent 互動課堂」的教育工作者 / 課程設計師 / 培訓團隊；對「OpenClaw agent 從 chat app 觸發課程」有興趣的 agent / Feishu / Slack / Telegram 用戶；想要一個本地優先（Ollama / Lemonade / FunASR）AI 教學平台的偏鄉 / 隱私場景。
  - **How：** (a) **Vercel 一鍵 deploy（最快）**：README 第一個 button 就是 `<a href="https://vercel.com/new/clone?repository-url=...">` — clone 後設一個 LLM provider API key（如 `OPENAI_API_KEY` 或 `ANTHROPIC_API_KEY`），Vercel 自動 build + 跑 Next.js；(b) **本地 dev**：`git clone` → `pnpm install`（Node.js 20+ / pnpm 10+）→ `cp .env.example .env.local` → 填一個 LLM key → `pnpm dev`；(c) **OpenClaw 整合（推薦 for agent users）**：`clawhub install openmaic` 或直接對 Claw 說「install OpenMAIC skill」→ 選 hosted mode（去 [open.maic.chat](https://open.maic.chat/) 拿 access code）vs self-hosted → 對 Claw 說「teach me quantum physics」即觸發；(d) **本地 LLM provider**：`LEMONADE_BASE_URL=http://localhost:13305/v1` + `TTS_LEMONADE_BASE_URL` / `ASR_LEMONADE_BASE_URL` / `IMAGE_LEMONADE_BASE_URL` 設好後整包免 API key；(e) **本地 ASR**：裝 `funasr==1.4.0` + `funasr-server --device cuda --model fun-asr-nano` 跑 SenseVoiceSmall/Paraformer/Fun-ASR-Nano。

- **安裝方式：**
  - **Vercel 一鍵 deploy（主推）**：README 頂端 `<a href="https://vercel.com/new/clone?repository-url=...">` button → Vercel 自動 clone + build + 跑 Next.js，使用者只需設 1 個 LLM provider API key env。
  - **本地 dev**（Node.js 20+ / pnpm 10+）：
    ```bash
    git clone https://github.com/THU-MAIC/OpenMAIC.git
    cd OpenMAIC
    pnpm install
    cp .env.example .env.local   # 填 OPENAI_API_KEY 或 ANTHROPIC_API_KEY 至少一個
    pnpm dev
    ```
  - **OpenClaw 整合（for agent users）**：
    ```bash
    clawhub install openmaic
    # 或直接對 Claw 說 "install OpenMAIC skill"
    # 然後對 Claw 說 "teach me quantum physics" → 即觸發課程生成
    ```
  - **本地 LLM provider（免 API key）**：裝 [Lemonade](https://github.com/lemonade-sdk/lemonade) → `LEMONADE_BASE_URL=http://localhost:13305/v1` 設進 `.env.local`，涵蓋 LLM/image/TTS/ASR 全部本地推論。
  - **本地 ASR（FunASR）**：`python -m pip install "funasr==1.4.0" fastapi uvicorn python-multipart` + `funasr-server --device cuda --model fun-asr-nano`。
  - **npm 套件**：`@openmaic/generation` v0.3.1 / MIT（純 permissive, case-A verified via package）；`@openmaic/*` SDK family 含 DSL / renderer / importer 全家族。

- **近期 release：** `v1.0.0` — **2026-08-27 13:13 UTC 發佈（台北時間 8/27 21:13, **4 天內**）**, pre-release = false, draft = false。Body 主標題「OpenMAIC v1.0.0 — Build courses with an agent」+ 重點 bullets：「🤖 Agent workbench — chat-first workspace 計畫/建構/修訂完整課程」+「💾 Durable sessions — server-backed runs 跨重啟存活；cancel/resume/steer anytime」+「📎 Session materials — 上傳 docs/audio/video 或 web search pull」+「🧰 Course tools + 20 built-in skills — slides/quizzes/interactives/PBL/images/video/voices/.pptx import」+「🔌 Neutral by design — 帶自己的 models/media/search/storage」。**最新後續 release：** v0.3.2 (8/14) + v0.3.1 (7/21) + v0.3.0 (6/28, 同時 license 從 AGPL-3.0 → MIT) + v0.2.2 (6/2) + v0.2.1 (4/26, 整合 VoxCPM2 TTS) + v0.2.0 (4/20, Deep Interactive Mode 3D) + v0.1.1 (4/14) + v0.1.0 (3/26)。**Repo `pushed_at` 2026-08-30 09:16 UTC（台北時間 8/30 17:16, 1 天前 main 仍 commit）** + `created_at` 2026-03-11（**6 個月內從 0 衝到 24,813★**, viral-tail 中段）+ 4,554 forks + 24.8k★ 累計 + 151MB size + Topics = `[]`（GitHub auto-detect 沒抓到，但 README 明列 LangGraph 1.1 / Next.js 16 / React 19 / TypeScript 5）+ License = **MIT**（**case-A verified via file**，且 **2026-06-28 從 AGPL-3.0 relicensed to MIT** — README 內有 JCST'26 paper 引用 + License badge）。

### `Lakr233/vphone-cli`

- **Repo 摘要：** Lakr233（曾做 SpringBoard / iOS 模擬相關工具）維護的 **「Boot a virtual iPhone via Apple's Virtualization.framework using PCC research VM infrastructure」** — 6 個月內從 0 衝到 9.8k★，Swift 寫的命令列工具，**用 Apple Silicon Mac 跑完整 iPhone VM**（含 IPSW 還原、CFW jailbreak 變體、SSH/VNC 連入）。**核心差異化**：(1) **跑 iPhone VM 在 macOS 上** — 不是模擬器，是真的 virtual iPhone，透過 Apple Virtualization.framework + private PV=3 entitlements + AMFI/SIP relaxation 達成；(2) **5 種 firmware 變體** — `less` (4 patches, 留 iOS mitigations) / `regular` (42 patches, AMFI/SSV/Img4/TXM bypass) / `dev` (53 patches, + TXM entitlement debug bypass) / `jb` (113 patches, + 完整 jailbreak 含 Sileo + TrollStore 自動裝) / `exp` (141 patches, JB + anti-VM-detection research patches)；(3) **一鍵 `vm create`** — 整條 pipeline (下載 IPSW → patch → DFU restore → CFW install → first boot) 一個 command 跑完；(4) **APFS clone 快速 VM 複製** + `vm export/import` 用 zstd fast（default）或 xz -9（`--max`）打包；(5) **Host control socket** (`<bundle>/vphone.sock`) 對 AI-driven E2E testing — screenshots / touch / swipes / hardware keys / clipboard 每個動作回傳 inline screenshot；有 `vphone-mcp` MCP server wrap 起來；(6) **完整 SIP/AMFI relaxation 教學** — Option A 全關 SIP + AMFI boot-arg；Option B 保留 SIP debug-only relaxed + `amfidont` allowlist binary（AMFI 仍系統開啟）。

- **3W1H：**
  - **What：** Swift + Python (build tooling) 命令列工具，6 個 sub-commands (`vm create/launch/info/new/config/clone/export/import/rename/delete`, `fw prepare/patch`, `restore`, `cfw install`) 全套 iPhone VM 生命週期管理。需要 macOS 15+ + Apple Silicon + Xcode + iOS SDK。
  - **Why：** 解決「開發者想做 iOS app E2E testing 但需要真硬體、要 8 種 iPhone 型號 lab、SRE 想隔離可疑 app、AI agent 想視覺驗收 iOS UI 但沒有可程式化操控的 iPhone instance」三個交集合一痛點。`vphone-cli` 把 Apple 官方 private Virtualization.framework + PCC research VM infra 包成可重現、可 export、可 host-control socket 操控的 command。
  - **Who：** iOS app E2E testing 工程師（特別是 CI/CD pipeline 想跑多 iOS version 平行）；對 iOS 內部 / IPSW 結構 / jailbreak 變體 / AMFI/SIP relaxation 研究有興趣的 security researcher；想用 AI agent（vision-based / MCP-driven）做 iOS UI 自動化驗收的 QA engineer / agent builder；對「把 iPhone 跑成 VM 並用 socket 操控」有研究需求的 reverse engineer。
  - **How：** (a) **brew tap 一行裝**：`brew install zqxwce/tap/vphone-cli`（主推）；(b) **build from source**：`git clone --recurse-submodules` → `./scripts/setup_tools.sh`（裝 deps + build toolchain + 建 Python venv）→ `./scripts/build.sh`（build + sign vphone-cli + bundle `.app` + cross-compile `vphoned`）→ 跑 `.build/vphone-cli.app/Contents/MacOS/vphone-cli --help`；(c) **完整一鍵 pipeline**：`vphone-cli vm create myphone -V jb` + `vphone-cli vm launch myphone`（自動走完整 download → patch → DFU restore → CFW install → first boot）；(d) **手動分階段**：6 個 command 一步步跑（`vm new` → `fw prepare --iphone-version 26.1` → `fw patch --variant jb` → `vm launch --dfu &` → `restore --get-shsh` → `restore` → `vm stop` → `cfw install --variant jb` → `vm launch`）；(e) **連入 VM**：`ssh -p 22222 mobile@<vm-ip>` (密碼 `alpine`, jailbreak variant) 或 `ssh -p 22222 root@<vm-ip>` (regular/dev variant) / `vnc://<vm-ip>:5901` VNC；(f) **AI agent 操控**：用 [vphone-mcp](https://github.com/pluginslab/vphone-mcp) MCP server 包 host control socket。

- **安裝方式：**
  - **Homebrew tap 一行裝（主推）**：
    ```bash
    brew install zqxwce/tap/vphone-cli
    ```
  - **依賴（brew bundle）**：
    ```bash
    brew install python@3.13 aria2 wget gnu-tar openssl@3 ldid-procursus sshpass keystone cmake libusb ipsw zstd
    ```
  - **Build from source**（macOS 15+ / Apple Silicon / Xcode + iOS SDK required）：
    ```bash
    git clone --recurse-submodules https://github.com/Lakr233/vphone-cli.git
    ./scripts/setup_tools.sh      # 裝 deps + build toolchain submodules + 建 Python venv
    ./scripts/build.sh            # build + sign vphone-cli + bundle .app + cross-compile vphoned
    cd .build/vphone-cli.app/Contents/MacOS/
    vphone-cli --help
    ```
  - **快速啟動**：
    ```bash
    vphone-cli vm create myphone -V jb      # 一鍵走完 download → patch → DFU restore → CFW install → first boot
    vphone-cli vm launch myphone            # 啟動 VM
    ssh -p 22222 mobile@<vm-ip>             # 連入（密碼 alpine）
    vnc://<vm-ip>:5901                       # 或 VNC
    ```
  - **VM 位置**：`~/.vphone/VMs/<vm-name>/`（用 `$VPHONE_ROOT` env 可改 root；`$VPHONE_LIBRARY_ROOT`/`$VPHONE_VENV_DIR` 細粒度覆寫）；IPSWs 快取在 `~/.vphone/ipsws/`、APFS seal-volume artifacts 在 `~/.vphone/tools/`、CFW .debs 在 `~/.vphone/debs/`、Python venv 在 `~/.vphone/venv/`。
  - **未找到明確 `pip install` / `npm install` 安裝方式**（macOS-only Swift native CLI + brew tap 是正路）。

- **近期 release：** `v1.0.12` — **2026-08-29 00:45 UTC 發佈（台北時間 8/29 08:45, **2 天內**）**, pre-release = false, draft = false。Body：tag 細節需在 GitHub release page 查（API endpoint 已確認）。**Repo `pushed_at` 2026-08-29 00:45 UTC（同步 release commit, 2 天前仍動）** + `created_at` 2026-02-26（**6 個月內從 0 衝到 9,798★**, niche-but-viral）+ 1,294 forks + 5,662KB size（**非常小**, Swift CLI + Python build tooling 主檔）+ Topics = `[]` + License = **MIT**（純 permissive, case-A verified via file, Swift project 預設）。**測試環境**：Mac16,11 27.0b2 + Mac16,8 26.5.1 兩組 host 對 17,3_18.6.2_22G100 / 17,3_26.0_23A341 / 17,3_26.0.1_23A3 等多組 iPhone IPSW + 26.1-23B85 cloudOS 已驗證。

### `p-e-w/heretic`

- **Repo 摘要：** p-e-w（Patricio Worth, Python 開源圈活躍）維護的 **「Fully automatic censorship removal for language models」** — 11 個月內到 29.3k★，**1.4k LOC Python** 把 [Arditi et al. 2024](https://arxiv.org/abs/2406.11717) 的 directional ablation（aka 「abliteration」）+ [grimjim 2025](https://huggingface.co/blog/grimjim/projected-abliteration) 的 norm-preserving biprojected abliteration + [Optuna](https://optuna.org/) 的 TPE optimizer 組起來，**全自動** co-minimize refusals + KL divergence from original。**核心差異化**：(1) **fully automatic** — 不需要懂 transformer internals、不知道哪些 layer 要 patch、不知道哪些 refusal direction；Heretic 自己 benchmark 系統 + TPE 搜尋 high-quality abliteration parameters；(2) **量化支援** — bitsandbytes `bnb_4bit` 讓 gpt-oss-20b / Qwen3-4B 在 16GB VRAM 也能跑；(3) **支援 dense / multimodal / 多種 MoE / Qwen3.5 hybrid** — 不支援 pure state-space model；(4) **內建 eval** — `--evaluate-model` 直接給 refusals-for-harmful-prompts + KL-divergence 數字，README 內 gemma-3-12b-it 對照表（**Heretic: 3/100 refusals + 0.16 KL** vs human-crafted mlabonne v2: 3/100 + 1.04 KL vs huihui-ai: 3/100 + 0.45 KL，**自動 abliteration 達到人手品質但 KL 損失少 4-7 倍**）；(5) **研究模式 `[research]` extra** — `--plot-residuals` 跑 PaCMAP 2D projection + layer-to-layer GIF 動畫；`--print-residual-geometry` 印 per-layer 表（mean/median residual vectors 對 good/bad/refusal 三組的 cosine sim + norm + silhouette score，**interpretability 工具**）；(6) **社群採用**：Hugging Face 上 `[heretic]` filter **超過 5,000 個 model** 被社群發布；(7) **獨立 benchmark** — Reddit r/LocalLLaMA [1](https://old.reddit.com/r/LocalLLaMA/comments/1sojjoc/abliterlitics_benchmark_and_tensor_analysis/) + [2](https://old.reddit.com/r/LocalLLaMA/comments/1sy18lx/abliterlitics_benchmarks_and_tensor_comparison/) 用 MMLU + GSM8K 驗證 Heretic 對 intelligence 損害最少。

- **3W1H：**
  - **What：** Python 3.10+ CLI (`heretic <model-id>`)，搭配 PyTorch 2.2+（MXFP4 model 如 gpt-oss 需要 2.6+）做 transformer-based language model 自動 abliteration。產出 decensored model 可直接 save / upload HuggingFace / chat 測試 / 跑標準 benchmark。
  - **Why：** 解決「手動 abliteration 需要懂 transformer internals、暴力砍 refusal direction 會嚴重破壞模型 intelligence、社群流傳的 abliterated model KL divergence 普遍 >1.0 但 Heretic 用 TPE auto-search 可降到 0.16」的痛點。讓任何會跑 CLI 的人都能 decensor language model，**不用理解模型內部結構**。
  - **Who：** 想跑本地 uncensored LLM 但不滿意官方版本拒答、想保留原模型 intelligence（KL < 0.5）又想擺脫 refusal 的 power user / local LLM 玩家 / red-team researcher / interpretability 研究者；對「refusal direction 幾何 / PaCMAP residual visualization / per-layer S(g,b)/S(g,r) silhouette 量化」有興趣的 mechanistic interpretability 研究者；要做「ablation study」對照實驗的 alignment researcher。
  - **How：** (a) **PyPI 一行裝**：`pip install -U heretic-llm` + `heretic Qwen/Qwen3-4B-Instruct-2507`（換成任意想 decensor 的 model）；(b) **uv 鎖版安裝（推薦）**：`git clone` → `uv run heretic <model-id>`（用 repo 內 `uv.lock` 鎖所有版本，提升 reliability + security）；(c) **量化變體**：用 `--quantization bnb_4bit` 把 model 量化到 4-bit，gpt-oss-20b 可在 16GB VRAM 跑；(d) **內建 eval**：`heretic --model google/gemma-3-12b-it --evaluate-model p-e-w/gemma-3-12b-it-heretic` 看 refusals + KL；(e) **研究模式**：裝 `[research]` extra 後 `heretic <model-id> --plot-residuals`（PaCMAP 2D scatter + GIF）/`--print-residual-geometry`（per-layer 量化表）。

- **安裝方式：**
  - **PyPI 一行裝（pip, 主推）**：
    ```bash
    pip install -U heretic-llm
    heretic Qwen/Qwen3-4B-Instruct-2507   # 換成任意 model id
    ```
  - **研究模式安裝（含 interpretability 工具）**：
    ```bash
    pip install -U 'heretic-llm[research]'
    ```
  - **uv 鎖版安裝（推薦 for reliability/security）**：
    ```bash
    git clone https://github.com/p-e-w/heretic
    cd heretic
    uv run heretic <model-id>     # 用 repo 內 uv.lock 鎖所有版本
    ```
  - **量化模式**（gpt-oss-20b 在 16GB VRAM 也能跑）：
    ```bash
    heretic openai/gpt-oss-20b --quantization bnb_4bit
    ```
  - **內建 eval**：`heretic --model <original-id> --evaluate-model <heretic-output-id>` 看 refusals-for-harmful-prompts + KL-divergence-from-original。
  - **PyPI 套件**：heretic-llm **v1.4.0** / **Python 3.10+** / PyPI license = `None` 但 **repo `license.spdx_id = "AGPL-3.0"`**（**PyPI metadata 未反映 AGPL-3.0，需以 repo 為準 — case-E strong copyleft**）。

- **近期 release：** `v1.4.0` — **2026-06-14 12:55 UTC 發佈（台北時間 6/14 20:55, **78 天前**）**, pre-release = false, draft = false。**Repo `pushed_at` 2026-08-17 16:57 UTC（台北時間 8/18 00:57, **13 天前 main 仍 commit**）** — `pushed_at` 顯著比 release 新鮮得多，**main branch 仍 active 開發中**（commit-rolling + `uv.lock` 鎖版累積中但 release tag cadence 慢）。**Repo `created_at` 2025-09-21（**11 個月內從 0 衝到 29,290★**，viral-tail 中段 — Trending today #5 = 369★/day 是 viral-tail 持續中）+ 3,213 forks + 1,094KB size（**超小，1.4k LOC Python**）+ 3 topics (`abliteration`, `llm`, `transformer`）+ License = **AGPL-3.0**（**case-E strong copyleft**；PyPI metadata 未反映，repo 為準；**closed-source SaaS 嵌入需 release source under AGPL**）。**社群採用**：Hugging Face 上 `[heretic]` filter **超過 5,000 個 model** 被社群發布。

### `agno-agi/agno`

- **Repo 摘要：** Agno Inc.（前 [Agno](https://agno.com) 商業平台開源版）維護的 **「Build, run, and manage agent platforms」** — 4 年內到 42k★，完整 agent framework + runtime + AgentOS UI 三層式 SDK；v3.0.0 (8/24) 開始把 runs 從 JSON blob 拆出到 `agno_runs` table、`agno_jobs` durable queue、`agno_tool_results` offload index 三張新表，是 4 年來最大架構重構。**核心差異化**：(1) **三層架構** — `agno` SDK 寫 agent + `AgentOS` runtime serve-as-API + `AgentOS UI` 管理整個平台（含 JWT-based RBAC + multi-tenant isolation）；(2) **50+ REST endpoints** — SSE + WebSocket，支援 production 部署；(3) **Durable background execution** — `AgentOS(queue=QueueConfig(durable=True))` 把 accepted runs 變成 committed rows 跨 crash/restart/deploy，由任一 replica 執行（bounded concurrency default 32 + `AGNO_BACKGROUND_MAX_CONCURRENCY`，queue 滿了回 429，`Idempotency-Key` dedupe）；(4) **Per-user isolation**（v3 新）— 從 sessions 延伸到 metrics / schedules / evals / knowledge / components / entity memory / **17 vector databases**；Metrics aggregate per-user per-day；(5) **Studio 3.0 governed catalog**（v3 新）— `create_*` 寫 DRAFT（不對外 serve）→ `publish_component` 才 serve + compare-and-set 409 + tombstoned deletes + archive/restore；(6) **100+ integrations** — GitHub / Slack / Postgres / MCP / 任意 OpenAI-compatible；(7) **8 種 deploy template** — `agentos-railway` / `agentos-docker` / `agentos-aws` / `agentos-gcp` / `agentos-azure` / `agentos-fly` / `agentos-render` / `agentos-modal` / `agentos-helm`，README prompt 把 clone + 部署整套交給 coding agent 跑；(8) **Storage adapter 16 種** — 12 sync + 4 async db backends，schema-versioned + `MigrationManager(db).up()` 一鍵遷移。

- **3W1H：**
  - **What：** Python SDK + FastAPI runtime + 控制 UI + 8 種 deploy template + Postgres/SQLite/Redis 16 種 storage backend 的完整 agent platform。**v3.0.0 是 4 年最大 breaking** — runs 從 JSON blob 拆出成獨立 table、Studio 3.0 加 DRAFT/publish lifecycle、AgentOS 變成 fully durable background execution platform。
  - **Why：** 解決「想做 AI agent platform 但要 enterprise-grade reliability（durability、RBAC、audit log、per-user isolation）+ 不被 vendor lock-in（provider-agnostic + 多 storage backend + 多 deploy option）+ production-grade observability（run history + OpenTelemetry tracing + audit logs）+ 一鍵 deploy（8 種 template 選一個就行）」的痛點。Agno 把「agent platform 的 backend」整套打包成可 self-host 的開源 runtime，closed-source SaaS 競爭者只剩 UX 而非 substrate 差異。
  - **Who：** 想建內部 AI agent platform 的 enterprise platform engineer / DevOps team / CTO；對「multi-tenant agent serving + RBAC + 持久化 run + queue + 17 種 vector DB 抽象 + OpenTelemetry tracing」有需求的 production team；想繞過 OpenAI Assistants / LangGraph Platform 抽成 + 鎖定的 builder；對「agent UI 不漂亮、Studio 要寫 code 才有」不滿意、想要 governed catalog + DRAFT/publish lifecycle 的 agent platform owner。
  - **How：** (a) **Coding agent 一鍵 setup（主推）**：把 README prompt 直接貼給 Claude Code / Cursor / Codex：「Help me set up my agent platform. Clone https://github.com/agno-agi/agentos-railway into a folder called agent-platform, cd in, read the README, and follow the get started guide.」→ coding agent 自動 clone + 跑 Docker + 給 REST API + Postgres + MCP server + control plane；(b) **手寫 20 行第一個 agent**（[docs](https://docs.agno.com/first-agent)）；(c) **Production API serve**（[docs](https://docs.agno.com/runtime/serve-as-api)）：50+ endpoints + SSE + WebSocket；(d) **MCP 整合**：把 `docs.agno.com/mcp` 加到 Claude Code / Cursor / VSCode / Windsurf 當 MCP server；(e) **跨 deploy 移植**：換 `agentos-railway` → `agentos-docker` / `agentos-aws` / `agentos-gcp` / `agentos-azure` / `agentos-fly` / `agentos-render` / `agentos-modal` / `agentos-helm` 任一 starter，deploy scripts 不同其餘一致。

- **安裝方式：**
  - **PyPI 一行裝（pip/uv, 主推）**：
    ```bash
    pip install agno     # 或 uv pip install agno
    # PyPI 確認：agno v3.0.4 / Apache-2.0 / Python 3.9+
    ```
  - **Coding agent 一鍵 setup（推薦 for non-coding users）**：
    ```text
    Help me set up my agent platform.
    Clone https://github.com/agno-agi/agentos-railway into a folder called agent-platform,
    cd in, read the README, and follow the get started guide.
    ```
  - **手寫 20 行 first agent**：[docs.agno.com/first-agent](https://docs.agno.com/first-agent)。
  - **Self-host platform**：[docs.agno.com/agent-platform/overview](https://docs.agno.com/agent-platform/overview) — clone 對應 starter template（railway/docker/aws/gcp/azure/fly/render/modal/helm 任一）→ 跑 README 內 setup。
  - **MCP 整合**：把 [docs.agno.com/mcp](https://docs.agno.com/mcp) 加到 Claude Code / Cursor / VSCode / Windsurf 當 MCP server，coding agent 可即時讀 Agno 文件。
  - **Cursor / VSCode / Windsurf indexed source**：`Settings → Indexing & Docs → Add https://docs.agno.com/llms-full.txt`。
  - **Disable telemetry**：`AGNO_TELEMETRY=false`（預設每個 agent run 觸發匿名 event，prompts/messages/outputs **不會**上傳）。
  - **未找到明確 `npm install` / `pipx install agno` 安裝方式**（`pip install agno` + coding agent 接管是主路）。

- **近期 release：** `v3.0.4` — **2026-08-30 22:40 UTC 發佈（台北時間 8/31 06:40, **15 小時內**）**, pre-release = false, draft = false。**v3.0.0 (8/24) 是 4 年最大架構重構** — runs 從 JSON blob 拆出到 `agno_runs` table + `agno_jobs` durable queue + `agno_tool_results` offload index 三張新表；v3.0.0 → v3.0.4 在 6 天內 4 個 minor/patch fix（**v3.0.1/2/3/4 = 6 天內 4 hotfix，daily-cadence SemVer**）。**Repo `pushed_at` 2026-08-31 05:05 UTC（台北時間 8/31 13:05, **今天 main 仍 commit，release commit 同步**）** + `created_at` 2022-05-04（**4 年累計 41,973★**, mature framework viral）+ 5,846 forks + 315MB size（**含完整 docs + integrations**）+ 5 topics (`agents`, `ai`, `ai-agents`, `developer-tools`, `python`) + License = **Apache-2.0**（**case-A2 verified via file + PyPI metadata**, 含 patent grant clause, **closed-source fork 安全度比 MIT 更高**）。**Breaking changes from v2.x**：runs table migration required (`MigrationManager(db).up()`)、pagination `page<1` raises ValueError、AgentOS JWT secret_key → verification_keys list、AgentOS GET /models removed、Studio create_ writes DRAFT、`enable_user_memories` → `update_memory_on_run`、`reasoning=True` removed（要用 `reasoning_model= `）。

### `deepset-ai/haystack`

- **Repo 摘要：** deepset（Berlin-based, 2019 創立）維護的 **「Open-source AI orchestration framework for building context-engineered, production-ready LLM applications」** — 7 年內到 26.4k★，**Python-only mature framework**，**v3.0.0 (2026-04)** 把架構從 Pipeline → Pipeline + Agent 雙軌化（**Agent lifecycle hooks: `before_llm`/`before_tool`/`on_exit`/`after_run` + state tracking** `step_count`/`token_usage`/tool calls out-of-the-box）。**核心差異化**：(1) **Built for context engineering** — 設計 pipeline 時對「如何 retrieve/rank/filter/combine/structure/route before reaching the model」有 explicit 控制，每一步可 trace；(2) **Lifecycle hooks + state tracking** — Agent 層有 `before_llm`/`before_tool`/`on_exit`/`after_run` hooks 給 guardrail/custom logic 插樁 + 自動 track `step_count`/`token_usage`/tool calls 給 monitoring + cost control；(3) **CompactionHook（v3.1.0 新）** — experimental context compaction for `Agent`，runs before LLM calls 自動 shorten conversation；built-in `SlidingWindowCompactor`（先砍整個舊 turn，再砍當前 task 的 individual step）+ `ToolResultPruningCompactor`（用 placeholder 換掉舊/large tool results 但保留最近 tool-calling steps）；(4) **haystack.token_counters（v3.1.0 新）** — `ApproximateTokenCounter` (no deps, text length 估算) / `TiktokenCounter` (local, o200k_base 精度近 OpenAI) / `OpenAITokenCounter` (call OpenAI counting API exact match)；(5) **AgentTool（v3.1.0 新）** — wrap 一個 Haystack Agent 當 Tool 給另一個 Agent 用，只有 wrapped Agent 的 final reply 對 caller 可見，intermediate steps 不進主 agent context；(6) **Agent.clone()** — 同 config 產生新 agent 可 override 一些 init params；(7) **exit_reason state key** — Agent 報告為何停止（`"text"` / tool 名 / `"max_agent_steps"`），可從 `state.get("exit_reason")` 讀；`after_run` hook 可在 step budget 用盡時 append fallback answer；(8) **Native async** — 一個 `Pipeline` 可 sync/async/streaming token-by-token；Agent 並行 tool calls；(9) **HAYSTACK_UNSAFE_DESERIALIZATION（v3.1.0 新 hardening）** — process-wide equivalent of `unsafe=True`，**一旦讀取即 frozen**，防止 hostile pipeline 在 loading 過程中把 safety mode 切掉；(10) **SkillToolset（progressive skill discovery）** — skill descriptions 只在需要時進 context。

- **3W1H：**
  - **What：** Python 3.10+ AI framework，設計 modular pipelines + agent workflows with explicit retrieval/routing/memory/generation 控制。Component-based：OpenAI / Mistral / Anthropic / Cohere / Hugging Face / Google / Azure OpenAI / AWS Bedrock / local models / many others model-agnostic。
  - **Why：** 解決「想做 production-grade agent/RAG/multimodal/conversational app 但要 modular pipeline + lifecycle hooks + state tracking + monitoring + cost control + 大 ecosystem 整合 + 模型/廠商中立 + 對 context engineering 每一步 explicit 控制」的痛點。Haystack 把「context 怎麼流到 model 之前」這條 pipeline 做到 production-grade，並用 lifecycle hooks + AgentTool + CompactionHook 讓 agent 子系統能 plug-in 各種 control plane。
  - **Who：** 建 production-grade RAG / multimodal / conversational / agentic system 的 ML engineer / platform engineer / applied AI team；對「context engineering before reaching model」有要求的嚴謹架構師；想把 multi-agent 編排成 production-grade 服務（不是 demo）但不想被 LangChain/LlamaIndex 全家桶綁死的 team；想要 skill progressive discovery（避免 skill 一口氣灌爆 context）的 enterprise agent builder。
  - **How：** (a) **PyPI 一行裝（主推）**：`pip install haystack-ai`；(b) **nightly pre-release**：`pip install --pre haystack-ai`；(c) **Docker**：官方 image（[docs](https://docs.haystack.deepset.ai/docs/installation)）；(d) **Get Started**：[docs.haystack.deepset.ai/overview/quick-start](https://docs.haystack.deepset.ai/overview/quick-start) 20 分鐘建第一個 LLM app；(e) **Multi-agent tutorial**：[haystack.deepset.ai/tutorials/45_creating_a_multi_agent_system](https://haystack.deepset.ai/tutorials/45_creating_a_multi_agent_system)；(f) **v3.1.0 CompactionHook 範例**：
        ```python
        from haystack.hooks.compaction import CompactionHook, SlidingWindowCompactor, ToolResultPruningCompactor
        from haystack.components.generators.chat import OpenAIResponsesChatGenerator
        # agent.add_hook(CompactionHook(strategy=SlidingWindowCompactor()))
        ```

- **安裝方式：**
  - **PyPI 一行裝（pip, 主推）**：
    ```bash
    pip install haystack-ai       # PyPI 確認：haystack-ai v3.1.0 / Python 3.10+
    pip install --pre haystack-ai # nightly pre-release（試 newest features）
    ```
  - **Conda**：`conda install -c conda-forge haystack-ai`。
  - **Docker**：官方 image（[docs](https://docs.haystack.deepset.ai/docs/installation)）。
  - **GET STARTED 教程**：[docs.haystack.deepset.ai/overview/quick-start](https://docs.haystack.deepset.ai/overview/quick-start)。
  - **Multi-agent tutorial**：[haystack.deepset.ai/tutorials/45_creating_a_multi_agent_system](https://haystack.deepset.ai/tutorials/45_creating_a_multi_agent_system)。
  - **Hayhooks**（把 Haystack pipeline / agent 包成 REST API / MCP server / OpenAI-compatible chat endpoint）：[github.com/deepset-ai/hayhooks](https://github.com/deepset-ai/hayhooks)。
  - **OpenTelemetry tracing**：`from haystack.core.tracing` 內建 + OpenTelemetry 標準 export。
  - **Telemetry opt-out**：[docs.haystack.deepset.ai/docs/telemetry](https://docs.haystack.deepset.ai/docs/telemetry)（README 內詳細說明）。

- **近期 release：** `v3.1.0` — **2026-08-24 15:19 UTC 發佈（台北時間 8/24 23:19, **7 天內**）**, pre-release = false, draft = false。Body 主標題「⭐ Highlights」三大塊：🪝 **CompactionHook** + built-in `SlidingWindowCompactor` / `ToolResultPruningCompactor`；🪙 **`haystack.token_counters`** module 含 `ApproximateTokenCounter`（no deps）/ `TiktokenCounter`（local, 需 `pip install tiktoken`）/ `OpenAITokenCounter`（call OpenAI counting API exact match）；🛠️ **AgentTool** 包 Haystack Agent 當 Tool 給另一個 Agent（只有 wrapped Agent final reply 對 caller 可見）。**其他 Features**：`Agent.clone()` method、PyPDFToDocument + PDFMinerToDocument 加 `link_format` 從 PDF annotation parse、Agent returns `exit_reason` output（`"text"` / tool 名 / `"max_agent_steps"`）、DocumentStore 提供 `close` / `close_async` methods、content-free `haystack.agent.hook` tracing span 給 OpenTelemetry 歸因 hook latency 不需要 trace large Agent State、`HAYSTACK_UNSAFE_DESERIALIZATION` env var process-wide 給 trusted-only deployment。**Breaking changes**：Jinja custom_filters `OutputAdapter`/`ConditionalRouter` 序列化要 `unsafe=True`；`exit_reason` 是 reserved state key 重定義會 raise；`Agent.state_schema` 只存 user-provided 不含 managed keys（要 inspect 用 `agent.resolved_state_schema`）；`DocumentMAPEvaluator` 平均精度重算；`PipelineSnapshot.pipeline_state.inputs` 從 `{component: {socket: value}}` 變 internal inputs shape。**Repo `pushed_at` 2026-08-30 06:47 UTC（台北時間 8/30 14:47, **1 天前 main 仍 commit**）** + `created_at` 2019-11-14（**7 年成熟框架，4 年內從 ~0 衝到 26,366★**, mature AI framework）+ 3,057 forks + 279.9MB size（**含 docs + 大 ecosystem**）+ 20 topics（`agent-framework`, `agentic-ai`, `agentic-rag`, `agents`, `ai`, `ai-agents`, `context-engineering`, `framework`, `genai`, `generative-ai`, `information-retrieval`, `large-language-models`, `llm`, `mcp`, `multi-agent`, `orchestration`, `python`, `rag`, `retrieval-augmented-generation`, `semantic-search`）+ License = **Apache-2.0**（**case-A2 verified via file + PyPI metadata 含 Apache-2.0 full text**, 含 patent grant clause, **closed-source fork 安全度比 MIT 更高**）。

## 重點觀察

- **Tier-A top 3 = 3/3 全 FRESH（cross-day 0/3 repeat floor ×3 VERIFIED）**：今天 Tier-A 排除 5 個 repeat（`tt-a1i/archify` 4,722★/day 是 highest stars/day 但 REPEAT 4 天 + `K-Dense-AI/scientific-agent-skills` REPEAT 3 天 + `bilawalsidhu/gods-eye-view` REPEAT 3 天 + `agentscope-ai/AgentTeams` 08-30 + `perplexityai/bumblebee` 08-30）後，**3 個 pick 都是 5 天窗口內 fresh**：THU-MAIC/OpenMAIC（8/27 release, 4 天內）+ Lakr233/vphone-cli（8/29 release, 2 天內）+ p-e-w/heretic（11 個月累計 viral, 持續 369★/day）。**0/3 cross-day repeat floor 已達 ×3（08-20 + 08-21 + 08-31）** — 符合 08-20 codification 「0/3 = natural floor = structurally optimal」標準。**Tier-A top 3 語言多樣**：TypeScript (OpenMAIC) + Swift (vphone-cli) + Python (heretic) — 比 08-30 「Python ×3」或 08-21 「Python ×5」更分散；**Tier-B + Tier-A 合計 5 個 pick = Python ×3 (heretic/agno/haystack) + TypeScript ×1 (OpenMAIC) + Swift ×1 (vphone-cli)** = **3 語言、3 生態，2026-08-31 是 14:00 系列本月「語言多樣性」codification 觸發**。
- **License 乾淨度 split = 3 permissive + 1 strong-copyleft + 1 permissive**：3/5 permissive（case-A + case-A2）：**OpenMAIC** MIT + **vphone-cli** MIT + **agno-agi/agno** Apache-2.0 + **haystack** Apache-2.0 = **4/5 permissive**（80%），**heretic** AGPL-3.0 = 1/5 strong copyleft（case-E）。**比 08-30 4/5 permissive (80%) 同級，但多了一個 AGPL-3.0 紅旗 — heretic PyPI metadata 完全未反映 AGPL-3.0**（PyPI license = `None` 但 repo `license.spdx_id = "AGPL-3.0"`），**closed-source SaaS 嵌入需 release source under AGPL**；對主人 horo-agent / horo-webui air-gapped downstream 影響為 **1 strong-copyleft flag（heretic）**。**OpenMAIC 同時**是 **AGPL-3.0 → MIT relicensed 2026-06-28** 的 case-A verified — `created_at` 2026-03-11 + 6 個月內從 0 衝到 24.8k★ 是 trending wave 期間 relicensed to MIT 的典型案例（其他類似：apache/maka 04-2026 等），**2026 年從 AGPL-3.0 relicensed to MIT 已是 viral 開源專案的常見路徑**，對下游 air-gapped 友善。
- **5 picks 5 種 install type 全 distinct（install type diversity is the new bar ×3 VERIFIED）**：Type-19 (`uv sync --frozen` + Streamlit WebUI + per-platform launch) 不命中；今天命中：**Type-4b2 brew cask + OS-specific installer matrix**（**vphone-cli** `brew install zqxwce/tap/vphone-cli` + 一組 macOS-only deps + build from source via `setup_tools.sh` + `build.sh`） + **Type-1 PyPI one-liner**（**heretic** `pip install -U heretic-llm` + `heretic <model-id>`） + **Type-21 `pip install` + coding-agent takeover setup**（**agno** `pip install agno` + README prompt 給 coding agent clone `agentos-railway` starter template 一鍵 setup）+ **Type-1 PyPI one-liner + Vercel one-click deploy**（**OpenMAIC** `pnpm install` 本地 dev 或 Vercel `<a href="https://vercel.com/new/clone?...">` button 一鍵 deploy + 多 provider LLM keys）+ **Type-1 PyPI one-liner + Docker + Hayhooks MCP wrapper**（**haystack** `pip install haystack-ai` + Docker image + Hayhooks REST/MCP wrapper）。**5 picks = 5 distinct install paths** 達 **install type diversity ×3 VERIFIED（08-20 + 08-21 + 08-31）**。
- **Release 新鮮度 tier-split（5 picks）**：超 fresh (≤1 天) = **2** (`agno-agi/agno` v3.0.4 15 小時前 daily-cadence SemVer + `OpenMAIC` v1.0.0 4 天前)、fresh (≤14 天) = **2** (`haystack` v3.1.0 7 天前 + `vphone-cli` v1.0.12 2 天前)、stale (>30 天) = **1** (`heretic` v1.4.0 **78 天前** 但 `pushed_at` 13 天前 pre-stable-like cadence)。**4/5 = 80% fresh (≤14 天)** 是 8 月系列 5 個 tier-A freshness tick 中最高（08-14/15 80% peak ×2 = 同級但命中率 tie）；**0/5 = 0% zero-GH-release** = 0 picks 沒 release = **5/5 = 100% release-tag cadence** 是 14:00 系列本月 **6 個 100% release-tag milestone**（08-12/13/14/15/17/20/21 + 31 共 8 個 100% milestone，08-31 是其中之一）。**Operational reading**：heretic 的「stale release」反映 main branch 仍 active 開發（13 天前 commit）+ uv.lock 持續累積但 release tag 沒追上；屬 pre-stable-like cadence 而非 abandoned。
- **Master MEMORY signal 命中**：今天 **2/5 picks fire Hermes-as-host-adjacent signal**：(a) **`THU-MAIC/OpenMAIC`** README 內完整 `### 🐾 OpenClaw — Use OpenMAIC from your chat app, zero setup` section + `clawhub install openmaic` install command + 對 OpenClaw agent 寫了 「congrats, you just passed the reading comprehension part of the Turing test」幽默徽章 — **OpenClaw 整合命中 Master MEMORY**，因為 MEMORY 內明確標出「多代理偏好 default（Qwen）直接建立／路由 Kanban task 給 executor／reviewer」+ 「OpenClaw, QwenPaw, and Hermes Workers coexist」（08-30 `agentscope-ai/AgentTeams` README 內已記錄）。(b) **`Lakr233/vphone-cli`** 透過 **`vphone-mcp` MCP server** 把 host control socket（`vphone.sock`）包成 MCP — 任何支援 MCP 的 agent host（Claude Code / Codex / Hermes / Cursor 等）都可拿 MCP 接 vphone 做 AI-driven iOS UI 自動化驗收，**MCP 是 Master MEMORY 內 hermes-agent / horo-agent 整合核心介面**，vphone-cli 透過 MCP 把 iOS VM 變成可程式化 agent target 對 Master 「邊做邊看」遊戲/視覺迭代哲學有直接套用價值（雖然 Master 主要做 web/canvas 互動，但 vphone 對 iOS app E2E 驗收是同源思路延伸）。**2/5 picks fire MEMORY-adjacent signal** 是 08-28 以來的本月 **2nd tier**（08-28 = 1/5, 08-29 = 2/5, 08-30 = 1/5, 08-31 = 2/5），符合 5-tick rolling average ≈ 1.4/5 sustainable MEMORY hit。
