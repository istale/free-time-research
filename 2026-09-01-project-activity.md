---
title: GitHub 自由探索 2026-09-01（14:00 台北時間）
date: 2026-09-01
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 5 天 repeat）
  - Web search（fresh AI agent / LLM training / agent orchestration 8 月底 release 篩選）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md`
  - PyPI JSON API（soup-cli / minimind）
  - npm registry（paperclipai）
---

# GitHub 專案動態

- 檢查時間：2026-09-01（14:00 台北時間）
- 檢查對象：`Osmantic/ODS` / `jingyaogong/minimind` / `zhaoxuya520/reverse-skill` / `paperclipai/paperclip` / `MakazhanAlpamys/Soup`
- 來源組合：GitHub Trending today Tier-A top 3 排除前 5 天 repeat（**fresh top 3 = `Osmantic/ODS` rank 7, 5628★ Apache-2.0 + 16 topics (ai-agents/llm/n8n/open-webui/rag/local-ai/docker/amd/nvidia/strix-halo/comfyui/llama-cpp/speech-to-text/text-to-speech/workflow-automation/self-hosted), **PC/Mac/Linux 變成 private AI server, v2.6.0 (7/28 release, 35 天前 stale) 但 pushed_at 9/1 (今天仍 commit active dev)** + `jingyaogong/minimind` rank 6, **56414★ 2 年內累積, Apache-2.0 + 2 topics (artificial-intelligence/large-language-model), 「3 塊錢 + 2 小時」訓練 64M LLM from scratch**, v2 release 2025-10-21 (10 個月前 stale) 但 pushed_at 8/31 = 1 天前 master 仍 commit + `zhaoxuya520/reverse-skill` rank 9, **33386★ 4 個月內 viral, MIT, AI agent skill router for reverse engineering / pentesting / security research, 41 條路由 (R0–R40) + 163 條中英雙語回歸基準 + 只讀 Case Review 證據圖 + Windows/Ubuntu CI**, v1.0.1 release 8/8 (24 天前 stale but pushed_at 8/31 = 1 天前仍 active)），排除 `tt-a1i/archify` (REPEAT 08-27/28/29/30/31) + `K-Dense-AI/scientific-agent-skills` (REPEAT 08-27/29/30/31) + `bilawalsidhu/gods-eye-view` (REPEAT 08-28/29/30/31) + `THU-MAIC/OpenMAIC` (08-31 pick) + `p-e-w/heretic` (08-31 pick) + `affaan-m/ECC` (REPEAT 08-23/24/25/26, v2.2.0 8/28 = 4 天內 fresh 但已被報過 4 次重複過多) + `agentscope-ai/AgentTeams` (08-30) + `perplexityai/bumblebee` (08-30) + `deepseek-ai/deepseek-harness` (08-29) + `memorax-ai/memorax-code` (08-29) + `anthropics/claude-plugins-official` (08-27/29) + `JetBrains/go-modern-guidelines` (08-28) + `freestylefly/awesome-gpt-image-2` (08-27/28) + `mco-org/mco` (08-27/28/29/30) + `thedotmack/claude-mem` (08-28) + `AgriciDaniel/claude-obsidian` (08-28) + `DietrichGebert/ponytail` (08-27) + `zedeus/nitter` (08-28) + `PrimeIntellect-ai/prime-agent` (REPEAT 08-08/09/10/11) + `k1tbyte/Wand-Enhancer` (Wand game extension, niche) + `majd/ipatool` (iOS IPA CLI, niche) + `checkstyle/checkstyle` (Java linting 經典) + `kaifcodec/user-scanner` (OSINT) + `every-app/open-seo` (Semrush/ahrefs alt) + `handsomestWei/patent-disclosure-skill` (中國專利 skill, niche) + `pollen-robotics/microduck_rl` (mjlab RL env)）+ web_search constrained search（Tier-B 2：`paperclipai/paperclip` v2026.824.1 8/25 release, **MIT + 79,783★ + 「If OpenClaw is an employee, Paperclip is the company」**, **Node.js server + React UI 編排 multi-agent business orchestration, bring your own agents, org charts/budgets/governance/goal alignment/agent coordination + dashboard**, **npm package paperclipai v2026.824.1 + beta/nightly/canary 4 channel** + **`MakazhanAlpamys/Soup` v0.73.3 8/18 release, Apache-2.0 + 4,239★ + 「Fine-tune and post-train LLMs in one command. No SSH, no config hell」, **Layer streaming 在 4GB laptop GPU 跑 8B model (RTX 3050 Laptop 3.32GB peak / 119.6 tok/s, bit-exact vs normal run)**, **24/24 PRs 來自社區 + 8 contributors + 4 個「validated 但 read nothing」config flags + MCP gated execution**, PyPI soup-cli 0.73.3 + Python 3.10-3.12）

## Repo 摘要與 3W1H

### `Osmantic/ODS`

- **Repo 摘要：** Osmantic 維護的 **「Osmantic Deployment System — Turn your PC, Mac, or Linux box into a private AI server」** — 7 個月內從 0 衝到 5,628★，**整合 Ollama + Open WebUI + n8n + ComfyUI + 隱私工具** 的本地 AI server 安裝與編排系統，目標是讓「homelab setup is rapidly becoming a solved problem」並讓每個人都享受這份便利。**核心差異化**：(1) **一鍵安裝全棧** — 不必手動組裝 Ollama + Open WebUI + n8n + ComfyUI + 隱私工具，自動挑 model for your hardware + 啟動 services + 給 web UI；(2) **三大作業系統都支援** — Linux/macOS `curl -fsSL https://install.osmantic.com/ods.sh | bash` + Windows PowerShell 等同 + Docker compose overlays；(3) **完整 AI server 能力** — local model inference + ChatGPT-style web UI + control dashboard (models/services/setup/GPU status/extensions) + voice/agents/workflows + RAG + image generation + privacy/ops（service auth/secrets/observability/diagnostics）；(4) **Release-grade validation fleet** — **zero-prereq bootstrap / fresh installs / product flows / full-model capabilities / lifecycle recovery / final User Green gate**, `v2.6.0` 通過 **six-host full-model finalize** (Tower2/Strix Halo/Spark/M5 MacBook Pro/Windows laptop/Strixy), regressions `16/16` + zero-prereq bootstrap `6/6` + cloud-mode contracts + dashboard + Hermes + UI policy + capabilities + lifecycle reinstall/restart + `ods doctor` 全 green；(5) **穩定 channel 策略** — `v2.6.0` 是 stable release，`release/2.6.x` patch lane 修補後才 forward merge 到 `main`，**讓 fork/appliance/lab/production-like install 可 pin tag 或 audited commit**；(6) **Open-source 同時有商業驗證** — README 內提到「Release validation: Operational changes are checked with a release-grade fleet and distro lab」+ `ods/docs/RELEASE_VALIDATION.md` + `RELEASE_CHANNELS.md` + `INSTALLER_TRUST.md` + `FORKABILITY.md`；(7) **支援 NVIDIA + Linux AMD/ROCm GPU** + 4 種 GPU reassignment rollback + rootless Docker bind-mount ownership repair for Linux + Windows native llama-server metrics/reasoning flags + Lemonade stale-PID handling；(8) **安全 hardening** — Remote-provider DNS-rebinding/SSRF hardening 通過 validated address pinning with preserved TLS identity。

- **3W1H：**
  - **What：** 整合 Ollama + Open WebUI + n8n + ComfyUI + 隱私工具的本地 AI server 編排系統。Linux/macOS/Windows 全支援，包含 installer phases / compose overlays / dashboard / CLI / tests / operator docs 全套。
  - **Why：** 解決「想做 homelab AI server 但要手動組 Ollama + Open WebUI + n8n + ComfyUI + 隱私工具、每個元件各自 docs/GPU 設定/版本相容性、想 fork 到 appliance 但沒有 release-grade validation receipt、要 multi-host (Linux/macOS/Windows + NVIDIA/AMD) 驗證 fleet」的痛點。ODS 把這整套打包成單一 installer + release-grade validation fleet + stable channel 策略，**讓 fork 變成可信任的「installable appliance」**。
  - **Who：** 想自建 private AI server 的 homelab 愛好者 / personal productivity 用戶；需要把 AI stack 部署到 appliance/edge/lab 但不想自己維護整個 stack 的 appliance vendor；對「multi-host validation fleet + User Green gate + stable patch lane」有要求的 SRE/platform team；想要 local Ollama + Open WebUI + n8n + ComfyUI 一鍵安裝但不想處理 GPU 相容性的 builder。
  - **How：** (a) **一鍵安裝（主推）**：Linux/macOS `curl -fsSL https://install.osmantic.com/ods.sh | bash` 自動挑 model for your hardware + 啟動 services + 給 local web UI；Windows PowerShell 對應；後續可用 `ods doctor` 檢查 health；(b) **Release-grade validation**：`v2.6.0` 通過 six-host full-model finalize (Tower2/Strix Halo/Spark/M5 MacBook Pro/Windows laptop/Strixy)，包含 16/16 regressions + 6/6 zero-prereq bootstrap；(c) **Channel 策略**：stable tag (`v2.6.0`) + `release/2.6.x` patch lane + `main` (active development) — production 用 stable tag 或 audited commit；(d) **CLI 後續**：`ods doctor` 診斷 health、`ods` 子命令管理 services/models/setup；(e) **Discord**：`https://discord.gg/qGVygYada3`（homepage）。

- **安裝方式：**
  - **Linux/macOS 一鍵裝（主推）**：
    ```bash
    curl -fsSL https://install.osmantic.com/ods.sh | bash
    ```
    自動挑 model for your hardware + 啟動 services + 給 local web UI。
  - **Windows PowerShell**：
    ```powershell
    $ProgressPreference = "SilentlyContinue"
    $odsSrc = Join-Path $env:TEMP ("ods-install-" + [guid]::NewGuid().ToString("N"))
    $odsZip = Join-Path $odsSrc "ods-main.zip"
    New-Item -ItemType Directory -Path $odsSrc | Out-Null
    Invoke-WebRequest "https://github.com/Osmantic/ODS/archive/refs/heads/main.zip" -OutFile $odsZip
    Expand-Archive -LiteralPath $odsZip -DestinationPath $odsSrc -Force
    cd (Get-ChildItem -LiteralPath $odsSrc -Directory | Select-Object -First 1).FullName
    Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
    .\install.ps1
    ```
  - **手動安裝**：`git clone https://github.com/Osmantic/ODS.git` → 進入 `ods/` 目錄讀 `RELEASE_VALIDATION.md` + `RELEASE_CHANNELS.md` + `INSTALLER_TRUST.md` + `FORKABILITY.md`。
  - **Health check**：`ods doctor`（v2.6.0 驗證 green）。
  - **未找到明確 `pip install` / `npm install` 安裝方式**（整合型 installer 是主路，PyPI/npm 個別 package 不適用 — 整個 stack 透過 shell installer + Docker compose overlays 編配）。

- **近期 release：** `v2.6.0` — **2026-07-28 14:37 UTC 發佈（台北時間 7/28 22:37, **35 天前**）**, pre-release = false, draft = false。Body 主標題「ODS 2.6.0」+ 5 大 highlights：**Remote provider direct/SSH egress, tunnel supervision, dashboard status, and peer model operations** + **Model Switchboard stable routing and verified context propagation across LLM applications** + **Verified NVIDIA and Linux AMD/ROCm GPU reassignment with rollback** + **Rootless Docker bind-mount ownership repair for Linux installs** + **Windows native llama-server metrics and reasoning flags, plus safer Lemonade stale-PID handling** + **Remote-provider DNS-rebinding/SSRF hardening through validated address pinning with preserved TLS identity**。**Release-stamp commit**：`f461b3e5e6e3f21077eefb6ca39bc49a2f0b0838`。**Validation**：Product candidate `07e2a21e3ccab197360009ebd3d66b4e6d4d0af2` + Base product commit `c292e00d5b60f6e4e6b331b2867346f9e9748a2c` + Gate result: green through **six-host full-model finalize** (Tower2/Strix Halo/Spark/M5 MacBook Pro/Windows laptop/Strixy) + regressions `16/16` + zero-prereq bootstrap `6/6` + fresh install + verify + cloud-mode contracts + dashboard + Hermes + UI policy + capabilities + lifecycle reinstall/restart + `ods doctor` 全 pass。**Known skipped/deferred surfaces**：`dgx-gpu01` excluded for unverified SSH host key；full model-management matrix intentionally stopped after cycle 1 passed on `tower2` and `strix-halo`（User Green not claimed）。**Stable patch lane**：`release/2.6.x`。**Repo `pushed_at` 2026-09-01 05:21 UTC（台北時間 9/1 13:21, **今天 main 仍 commit**）** + `created_at` 2026-02-09（**7 個月內從 0 衝到 5,628★**）+ 787 forks + 25,717KB size（**installer phases + compose overlays + dashboard + CLI + tests**）+ **16 topics** (`ai-agents`, `amd`, `comfyui`, `docker`, `llama-cpp`, `llm`, `local-ai`, `n8n`, `nvidia`, `open-webui`, `rag`, `self-hosted`, `speech-to-text`, `strix-halo`, `text-to-speech`, `workflow-automation`) + License = **Apache-2.0**（**case-A2 verified via file** 含 patent grant clause）。

### `jingyaogong/minimind`

- **Repo 摘要：** 個人開發者 jingyaogong (gongjy) 維護的 **「大道至简 — Train a 64M-parameter LLM from scratch in just 2h!」** — 2 年內從 0 衝到 56,414★，**Apache-2.0 + 從 0 用 PyTorch 原生實作** 的極小 LLM 全階段開源項目，**「3 塊錢 + 2 小時 + 單張 NVIDIA 3090 + 1 epoch SFT」即可訓練 64M 對話模型**。**核心差異化**：(1) **真正從 0 開始** — `transfromers`/`trl`/`peft` 等高階抽象 library 不用，所有核心算法代碼均從 0 用 PyTorch 原生實作（**「用樂高自己拼飛機」** 哲學）；(2) **極小 size 對照 GPT-3** — 主線最小版本體積約為 GPT-3 的 `1/2700`；(3) **全階段訓練鏈路** — MoE / 數據清洗 / Pretrain / SFT / LoRA / RLHF (DPO) / RLAIF (PPO / GRPO / CISPO) / Tool Use / Agentic RL / 自適應思考 / 模型蒸餾 + **MiniMind-V (視覺) / MiniMind-O (多模態 Omni) / MiniMind-dLM (擴散語言模型) / MiniMind-Linear (線性模型)** 全家族；(4) **可重現 + 可理解 + 可擴展** — 不只代碼 + 還有教程，`online experience` 在 [modelscope](https://www.modelscope.cn/studios/gongjy/MiniMind)；(5) **生態整合** — Hugging Face `MiniMind` collection + ModelScope `MiniMind` collection + Ollama + vLLM 第三方推理；(6) **WebUI** — Streamlit `web_demo.py` 互動式 demo；(7) **斷點續訓** — `--from_resume 1` 自動偵測並恢復訓練進度（自動調整 step 跨不同 GPU 數量、支援 wandb 訓練記錄連續性）；(8) **硬體友善** — CPU/MPS/CUDA 都支援，README 內硬體範例是 i9-10980XE + 128GB RAM + RTX 3090 ×8 + Ubuntu 20.04 + CUDA 12.2 + Python 3.10.16。

- **3W1H：**
  - **What：** PyTorch 原生實作的極小 LLM 全階段開源項目（含 MoE/Pretrain/SFT/LoRA/DPO/PPO/GRPO/CISPO/Tool Use/Agentic RL/蒸餾 + V/O/dLM/Linear 全家族）。包含訓練 + 推理 + WebUI + 第三方推理框架（Ollama/vLLM）整合。
  - **Why：** 解決「想做 LLM 訓練但 transformers/trl/peft 高階抽象 library 把開發者與底層隔離、付費課程漏洞百出、3rd-party LLM framework 不暴露底層接口讓學習者無法理解每一行代碼」的痛點。MiniMind 把整個 LLM 訓練鏈路降到最低門檻，**讓個人 GPU 也能從 0 親手訓練一個極小的語言模型**，理解物理本質而非只是 LoRA 微調。
  - **Who：** 想從 0 理解 LLM 訓練每一行代碼的 LLM 初學者 / 學生 / 自學者；要做 LLM 教學但教材被高階抽象 library 綁死的教授 / 培訓師；對「MoE / Agentic RL / 自適應思考 / 模型蒸餾」各階段想各別實作的進階 researcher；想驗證 MiniMind 在自己 GPU/hardware 上的 reproducer builder；想本地跑 64M 小模型做下游任務的應用開發者。
  - **How：** (a) **快速開始（主推）**：`git clone --depth 1 https://github.com/jingyaogong/minimind` → `cd minimind && pip install -r requirements.txt` → 下載 model (modelscope 或 HuggingFace) → `python eval_llm.py --load_from ./minimind-3` 推理或 `python train_full_sft.py --from_resume 1` 訓練；(b) **WebUI（optional）**：`pip install streamlit` → `cp -r minimind-3 ./scripts/minimind-3` → `cd scripts && streamlit run web_demo.py`；(c) **第三方推理**：`ollama run jingyaogong/minimind-3` 或 `vllm serve /path/to/model --served-model-name "minimind"`；(d) **斷點續訓**：所有訓練腳本加 `--from_resume 1` 即可（自動偵測 checkpoints/ + 自動調整 step + wandb run 連續性）。

- **安裝方式：**
  - **從 source 一行 clone + install（主推）**：
    ```bash
    git clone --depth 1 https://github.com/jingyaogong/minimind
    cd minimind && pip install -r requirements.txt -i https://mirrors.aliyun.com/pypi/simple
    ```
  - **下載 model（兩種）**：
    ```bash
    # 方式 1：modelscope
    modelscope download --model gongjy/minimind-3 --local_dir ./minimind-3
    # 方式 2：Hugging Face
    git clone https://huggingface.co/jingyaogong/minimind-3
    ```
  - **推理**：
    ```bash
    python eval_llm.py --load_from ./minimind-3
    # 或基於 PyTorch 權重：
    python eval_llm.py --load_from ./model --weight full_sft
    ```
  - **WebUI（optional, 需要 `python>=3.10`）**：
    ```bash
    pip install streamlit
    cp -r minimind-3 ./scripts/minimind-3   # web_demo 自動掃 ./scripts/ 子目錄
    cd scripts && streamlit run web_demo.py
    ```
  - **第三方推理框架**：
    ```bash
    ollama run jingyaogong/minimind-3
    # 或
    vllm serve /path/to/model --served-model-name "minimind"
    ```
  - **訓練**（需要 NVIDIA GPU + CUDA，建議單張 RTX 3090）：
    ```bash
    python train_pretrain.py --from_resume 1
    python train_full_sft.py --from_resume 1
    ```
  - **Dataset 下載**：[modelscope minimind_dataset](https://www.modelscope.cn/datasets/gongjy/minimind_dataset/files) — 預設 `pretrain_t2t_mini.jsonl` + `sft_t2t_mini.jsonl` 即可較快 reproduce `MiniMind Zero` 對話模型。
  - **PyPI package**：PyPI 有 `minimind` 套件（latest `0.6.0`），但 README 主推 from source + 訓練 scripts；`pip install minimind` 不直接等同完整訓練流程。
  - **硬體建議**（README 範例）：i9-10980XE + 128GB RAM + RTX 3090 ×8 + Ubuntu 20.04 + CUDA 12.2 + Python 3.10.16 — 但 64M 模型單張 RTX 3090 即可訓練。

- **近期 release：** `v2` — **2025-10-21 14:37 UTC 發佈（台北時間 10/21 22:37, **315 天前** = 10 個月前）**, pre-release = false, draft = false。Body 主標題「MiniMind Docs」。**Repo `pushed_at` 2026-08-31 10:08 UTC（台北時間 8/31 18:08, **1 天前 master 仍 commit**）** — `pushed_at` 顯著比 release 新鮮得多，**master branch 仍 active 開發中**（MiniMind-V/O/dLM/Linear 全家族擴展 + 訓練腳本持續更新 + 模型蒸餾新增）。**Repo `created_at` 2024-07-27（**2 年內從 0 衝到 56,414★**，viral-tail 後段 — Trending today #6 是 viral-tail 持續中）+ 7,350 forks + 39,401KB size（含全家族 models/訓練 scripts/datasets/docs）+ 2 topics (`artificial-intelligence`, `large-language-model`) + License = **Apache-2.0**（**case-A2 verified via file**，含 patent grant clause）。**PyPI `minimind` 0.6.0** 存在但非主推；**社群採用**：Hugging Face `MiniMind` collection + ModelScope `MiniMind` collection + Ollama registry。

### `zhaoxuya520/reverse-skill`

- **Repo 摘要：** zhaoxuya520 維護的 **「Reverse Engineering / Authorized Penetration Testing / Security Research Skill Router Pack — AI-powered routing + On-demand toolchain bootstrapping + Self-evolving knowledge base」** — 4 個月內從 0 衝到 33,386★，MIT + **客戶端無關**的 AI agent skill router，**支援 Claude Code / Kiro / Cursor / Cline 等 AI coding client**，定位為「skill router pack」告訴 AI agent 哪個 reverse-engineering 或 pentesting methodology 該用。**核心差異化**：(1) **客戶端無關** — 以 `skills/config/routing.json` 作為唯一事實源，不綁 OpenCode / Codex / Claude Code / Cursor 或其他 client；(2) **AI-powered routing** — 41 條路由 (`R0–R40`) + **163 條中英雙語回歸基準** + Windows + Ubuntu CI；(3) **On-demand toolchain bootstrapping** — 自動偵測並引導用戶安裝 reverse-engineering / pentesting 工具（如 Ghidra / radare2 / Burp Suite / Frida / `nmap` 等）；(4) **只讀 Case Review 證據圖審查** — 涵蓋 scope / timeline / work item / Finding / Path 與可選 SHA-256 完整性檢查；(5) **Self-evolving knowledge base** — 經驗庫會自動進化（PR 與 CI 都會累積）；(6) **供應鏈版本固定** — 工具引導 + MCP 認證 + 重連 + 供應鏈版本固定；(7) **P0 授權 / 路徑 / 證據鏈回歸 + Bash 與 PowerShell 路由 / 授權門禁一致性**；(8) **平臺覆蓋** — Windows / Linux / macOS / Kali Linux 各自有 platform-specific README。

- **3W1H：**
  - **What：** AI agent skill router（不是單純的 AI agent），41 條 reverse-engineering / pentesting 路由 + 中英雙語 + 跨平台 toolchain 引導 + Case Review 證據圖 + 自動進化經驗庫。透過 `skills/config/routing.json` 統一事實源，跨 Claude Code / Kiro / Cursor / Cline 等 AI coding client。
  - **Why：** 解決「AI agent 碰到 reverse-engineering / pentesting 任務時猜指令、沒有 methodology routing、工具未引導、沒有供應鏈版本固定、沒有證據鏈 + 完整性檢查、跨 client 不一致」的痛點。reverse-skill 把整個 reverse-engineering workflow 變成可被 AI agent 呼叫的 skill router，**讓 AI agent 不必猜要跑哪個 tool / 用哪個 workflow**。
  - **Who：** 做 reverse-engineering / authorized penetration testing / security research 的 security researcher / pentester / CTF player；想讓 Claude Code / Kiro / Cursor / Cline 等 AI coding client 也能做 security 任務的 AI agent power user；想統一 AI agent skill router schema 跨 client 的 skill author；對「client-agnostic skill routing + evidence chain + SHA-256 integrity」有需求的 CTI / DFIR / red team。
  - **How：** (a) **Clone + refresh tool index（主推）**：`git clone https://github.com/zhaoxuya520/reverse-skill.git` → 依平台跑 `powershell -File skills/scripts/refresh-tool-index.ps1` (Windows) / `bash skills/scripts/refresh-tool-index.sh` (Linux/macOS) / `bash kali/scripts/refresh-tool-index.sh` (Kali)；(b) **查 detected tools**：`skills/tool-index.md`；(c) **AI client 整合**：在 Claude Code / Kiro / Cursor / Cline 等 AI coding client 內直接呼叫 skill router（透過 client 的 skill 載入機制），router 會根據 `routing.json` 自動挑 routing；(d) **Case Review 證據圖審查**：跑 case review 工具自動產生只讀證據圖（scope/timeline/work item/Finding/Path + 可選 SHA-256 integrity check）。

- **安裝方式：**
  - **從 source clone（主推）**：
    ```bash
    git clone https://github.com/zhaoxuya520/reverse-skill.git
    ```
  - **Refresh tool index（依平台）**：
    | Platform | Command |
    |----------|---------|
    | Windows | `powershell -File skills/scripts/refresh-tool-index.ps1` |
    | Linux / macOS | `bash skills/scripts/refresh-tool-index.sh` |
    | Kali Linux | `bash kali/scripts/refresh-tool-index.sh` |
  - **查 detected tools**：[`skills/tool-index.md`](skills/tool-index.md)
  - **Platform-specific docs**：
    - **Kali Linux** → [kali/README-kali.md](kali/README-kali.md)
    - **Ubuntu/Debian** → [docs/platforms/linux.md](docs/platforms/linux.md)
    - **macOS** → [docs/platforms/macos.md](docs/platforms/macos.md)
  - **AI client 整合**：在 Claude Code / Kiro / Cursor / Cline 等 AI coding client 內直接呼叫 skill router（透過 client 的 skill 載入機制）。
  - **未找到明確 `pip install` / `npm install` 安裝方式**（skill router 透過 `git clone` + 客戶端 skill 載入機制整合，PyPI/npm 個別 package 不適用 — 整個 router 透過 config files + scripts 編配）。

- **近期 release：** `v1.0.1` — **2026-08-08 10:16 UTC 發佈（台北時間 8/8 18:16, **24 天前**）**, pre-release = false, draft = false。Body 主標題「重点更新」+ 4 大重點：(1) **路由改為客戶端無關的結構化配置** — 以 `skills/config/routing.json` 作為唯一事實源，不綁 OpenCode/Codex/Claude Code/Cursor 或其他 client；(2) **擴展到 41 條路由 (`R0–R40`)** + 163 條中英雙語回歸基準 + 加入 Windows + Ubuntu CI；(3) **新增只讀 Case Review 證據圖審查** — 涵蓋 scope/timeline/work item/Finding/Path 與可選 SHA-256 完整性檢查；(4) **補齊 Bash 與 PowerShell 路由/授權門禁一致性** + 強化工具引導 + MCP 認證 + 重連 + 供應鏈版本固定。**質量驗證**：路由回歸 163/163 + 路由結構與供應鏈一致性通過 + 冒煙測試通過 + **P0 授權/路徑/證據鏈回歸通過** + Case Review 8/8 + Burp MCP bridge 1/1。**Repo `pushed_at` 2026-08-31 07:23 UTC（台北時間 8/31 15:23, **1 天前 main 仍 commit**）** — `pushed_at` 顯著比 release 新鮮得多，**main branch 仍 active 開發中**（CI 持續累積 + case review 持續新增）。**Repo `created_at` 2026-05-13（**4 個月內從 0 衝到 33,386★**，viral-tail 中段 — Trending today #9 = high 衝刺中）+ 4,525 forks + **4,189KB size（非常小，主要為 config files + scripts + docs）** + 0 topics + License = **MIT**（**case-A verified via file**，純 permissive）。**完整變更**：[CHANGELOG](https://github.com/zhaoxuya520/reverse-skill/blob/v1.0.1/CHANGELOG.md)。

### `paperclipai/paperclip`

- **Repo 摘要：** Paperclip 維護的 **「Paperclip is the app people use to manage AI agents for work — If OpenClaw is an employee, Paperclip is the company」** — 6 個月內從 0 衝到 79,783★，MIT + Node.js 24.11+ server + React UI 編排的 **multi-agent business orchestration 開源平台**，bring your own agents, assign goals, 從單一 dashboard 追蹤 work 與 costs。**核心差異化**：(1) **OpenClaw 互補定位** — 「If OpenClaw is an employee, Paperclip is the company」明確與 OpenClaw 區隔（OpenClaw = agent runtime, Paperclip = 公司/組織/治理 層級）；(2) **Business-level orchestration** — 不是「pull request manager」而是「business goal manager」：CEO/CTO/engineers/designers/marketers 任一 bot + 任一 provider + org charts + budgets + governance + goal alignment + agent coordination；(3) **三步工作流** — Define the goal (`"Build the #1 AI note-taking app to $1M MRR."`) + Hire the team (CEO, CTO, engineers, designers, marketers — any bot, any provider) + Approve and run (Review strategy, Set budgets, Hit go, Monitor from the dashboard)；(4) **多 channel release 策略** — `npm paperclipai` 有 4 個 dist-tag：`latest` (2026.824.1) + `beta` (2026.828.0-beta.0) + `nightly` (2026.830.0-nightly.0) + `canary` (2026.901.0-canary.2) — 對應不同成熟度等級；(5) **Install 安全** — 提供 `install.sh` + `install.sh.sha256` 校驗 + 多種 install mode (interactive / `--no-prompt --no-onboard` / `npx --registry ...`)，checksum 偵測 transfer/publishing mistakes 但需注意「from same origin」；(6) **背景服務** — `paperclipai service` 可在 supported Linux/macOS 系統安裝為 background service（managed command shim pinned to version being onboarded）；(7) **Bind preset** — `trusted local loopback` (default fastest first run) / `lan` / `tailnet` 三種 bind mode；(8) **Onboarding troubleshooting** — 內建 `paperclipai doctor` + private npm registry 警告（避免 `~/.npmrc` 把 `paperclipai` resolve 到 private registry）。

- **3W1H：**
  - **What：** Node.js 24.11+ server + React UI + npm `paperclipai` CLI 的 multi-agent business orchestration 開源平台。包含 installer / onboard / service / dashboard / doctor / configure / uninstall 全套。
  - **Why：** 解決「想用 AI agent 跑業務但要 company-level orchestration（org charts / budgets / governance / goal alignment）+ 不被 single agent framework 綁死（bring your own agents）+ production-grade reliability（managed service + sha256 verified install + bind preset）+ multi-channel release cadence（latest/beta/nightly/canary）」的痛點。Paperclip 把「multi-agent 業務編排」這條企業級 orchestration 完整 open-source 出來，並用「If OpenClaw is an employee, Paperclip is the company」明確定位互補。
  - **Who：** 想用 AI agent 跑業務但需要 company-level orchestration（org/budget/governance/goal alignment）的 founder / 創業團隊 / SME；對「AI agent 公司編排 + dashboard」有需求的 ops / business lead；想用 multi-channel release cadence（latest/beta/nightly/canary）做 stage gate 的 platform engineer；想跟 OpenClaw 互補的 harness builder（OpenClaw = runtime, Paperclip = company 層級）。
  - **How：** (a) **官方 install script（主推）**：`curl -fsSLO https://paperclip.ing/install.sh` + `curl -fsSLO https://paperclip.ing/install.sh.sha256` + `sha256sum -c install.sh.sha256` + `bash install.sh`（自動確保 Node.js 24.11+ + 裝 managed CLI 到 `~/.paperclip/cli` + 啟動 interactive onboarding）；(b) **非互動 managed install**：`curl -fsSL https://paperclip.ing/install.sh | bash -s -- --no-prompt --no-onboard` + `paperclipai onboard --yes`；(c) **無 permanent install 嘗試**：`npx --registry https://registry.npmjs.org paperclipai onboard --yes`；(d) **Bind preset**：`paperclipai onboard --yes --bind lan` 或 `--bind tailnet`（預設 trusted local loopback）；(e) **手動**：`git clone https://github.com/paperclipai/paperclip.git` + 讀 [`doc/INSTALLING.md`](doc/INSTALLING.md) 含 pinned versions / canary / git-ref installs / updates / rollback / service management / uninstalling。

- **安裝方式：**
  - **官方 install script + sha256 校驗（主推）**：
    ```bash
    curl -fsSLO https://paperclip.ing/install.sh
    curl -fsSLO https://paperclip.ing/install.sh.sha256
    if command -v sha256sum >/dev/null 2>&1; then
      sha256sum -c install.sh.sha256
    else
      shasum -a 256 -c install.sh.sha256
    fi
    bash install.sh
    ```
    自動確保 Node.js 24.11+ + 裝 managed CLI 到 `~/.paperclip/cli` + 啟動 interactive onboarding + 可在 supported Linux/macOS 系統裝為 background service。
  - **非互動 managed install**：
    ```bash
    curl -fsSL https://paperclip.ing/install.sh | bash -s -- --no-prompt --no-onboard
    paperclipai onboard --yes
    ```
  - **無 permanent install 嘗試（try-before-install）**：
    ```bash
    npx --registry https://registry.npmjs.org paperclipai onboard --yes
    ```
  - **Bind preset**：
    ```bash
    paperclipai onboard --yes --bind lan       # 或
    paperclipai onboard --yes --bind tailnet
    ```
    預設 trusted local loopback（最快 first run）。
  - **手動安裝**：
    ```bash
    git clone https://github.com/paperclipai/paperclip.git
    cd paperclip
    ```
    讀 [`doc/INSTALLING.md`](doc/INSTALLING.md) 含 pinned versions / canary / git-ref installs / updates / rollback / service management / uninstalling。
  - **CLI 後續**：`paperclipai configure` 編輯設定、`paperclipai doctor` 診斷 health（v2026.824.1 新增更精準的 missing service binary 診斷）、`paperclipai service logs` 看 logs、`paperclipai install` + `paperclipai service start` 修復 crash-looping background service。
  - **npm 套件**：`paperclipai` v2026.824.1 (latest) + 2026.828.0-beta.0 (beta) + 2026.830.0-nightly.0 (nightly) + 2026.901.0-canary.2 (canary)，**engines: node >=20**（但 install.sh 要求 24.11+），bin: `paperclipai`。
  - **Troubleshooting** — private npm registry `.npmrc`：若失敗出現 `E404 for paperclipai`（或類似）且用了 private npm registry (e.g. GitHub Packages) 透過 global `~/.npmrc`，`npx` 可能 resolve `paperclipai` 到 private registry 而非 public。**Workaround**：`npx --registry https://registry.npmjs.org paperclipai onboard --yes`（跨平台強制 public registry）。

- **近期 release：** `v2026.824.1` — **2026-08-25 21:04 UTC 發佈（台北時間 8/26 05:04, **7 天前**）**, pre-release = false, draft = false。Body 主標題「Paperclip v2026.824.1 — Released: 2026-08-25」+ 主旨「Paperclip v2026.824.1 is a patch release on v2026.824.0 that repairs the background-service leg of onboarding end to end: the service now actually starts from an `npx` onboard, onboarding no longer steers you into a safety-check error afterward, and it finishes by handing you the dashboard in your browser.」**4 大 fixes**：(1) **Accepting the background-service prompt during an `npx` onboard no longer installs a broken service** — service step now materializes the managed install (payload and command shim, pinned to the version being onboarded) before registering the service, and when it cannot — a custom `PAPERCLIP_SHIM_PATH`, or a failed install — it declines with the repair commands instead. ([#12148](https://github.com/paperclipai/paperclip/pull/12148))；(2) **Onboarding no longer offers a foreground start the service already covers** — After a successful service install, interactive onboarding still asked "Start Paperclip now?" — accepting ran a second server into the already-running instance guard, ending a successful onboard with an error. The prompt is skipped once the service is running. ([#12153](https://github.com/paperclipai/paperclip/pull/12153))；(3) **Onboarding ends at the dashboard instead of a dead stop** — After the service starts, onboarding now waits for it to report the endpoint it actually bound (including a fallback port when the configured one is busy), prints the dashboard URL, and opens it in the browser on interactive terminals. Headless runs print the URL; `PAPERCLIP_NO_BROWSER=1` disables the browser open. If the service does not become ready, onboarding says so and points at `paperclipai service logs` instead of claiming success. ([#12164](https://github.com/paperclipai/paperclip/pull/12164))；(4) **`paperclipai doctor` diagnoses a missing service binary as exactly that** — service-runtime check no longer suggests stopping a nonexistent conflicting process when the service's binary is missing (it names the missing path and points at `paperclipai install`), and the health check attributes a healthy responder that is not the managed service instead of reporting a plain "Healthy". ([#12148](https://github.com/paperclipai/paperclip/pull/12148))。**Upgrade Guide**：無 migrations 也無 configuration changes；若之前的 onboard 留下 crash-looping background service，跑 `paperclipai install` + `paperclipai service start`（dead service definition 在 binary 存在後即可重用）。**Contributors**：3 commits from core team。**Repo `pushed_at` 2026-09-01 06:03 UTC（台北時間 9/1 14:03, **今天 main 仍 commit**）** + `created_at` 2026-03-02（**6 個月內從 0 衝到 79,783★**，viral-tail 中段 — daily-cadence SemVer `vYYYY.DDD.N` release）+ 14,639 forks + **223,300KB size（非常大，含完整 docs + UI + integrations）** + 0 topics + License = **MIT**（**case-A verified via file**，純 permissive）。**npm dist-tags**：`latest` 2026.824.1 + `beta` 2026.828.0-beta.0 + `nightly` 2026.830.0-nightly.0 + `canary` 2026.901.0-canary.2（**4-channel release** 是這條 repo 重要差異化）。

### `MakazhanAlpamys/Soup`

- **Repo 摘要：** MakazhanAlpamys 維護的 **「Fine-tune and post-train LLMs in one command. No SSH, no config hell — Fine-tune and post-train LLMs in one YAML. Layer streaming trains an 8B model on a 4 GB laptop GPU.」** — 7 個月內從 0 衝到 4,239★，Apache-2.0 + Python 3.10-3.12 的 **CLI + YAML + Layer Streaming** fine-tune framework，**Layer streaming 讓 frozen base 留 RAM、decoder layer 一次 feed 一層到 GPU，4GB laptop GPU 也能 fine-tune 8B model**。**核心差異化**：(1) **Layer Streaming (BETA, opt-in `stream_layers: true`)** — frozen base 留在 RAM，喂 GPU 一層 decoder layer，measured on RTX 3050 Laptop 4GB: Llama-3.1-8B-Instruct + NF4 at **119.6 tok/s, 3.32 GB peak — bit-exact against normal resident run**，獨立 reproduce on H100 at 113.00 tok/s in same 3.32 GB；(2) **One YAML, one command** — `soup init --template chat` + `soup train`，auto batch size / GPU detection / quantization；(3) **Local-first, no cloud required** — QLoRA 4-bit / NF4 / Apple Silicon MLX / CUDA / consumer GPU (`local-ai`/`consumer-gpu`/`low-vram` topics)；(4) **MCP integration** — `soup mcp serve --allow-execute` 跑在 short-lived, single-use, server-generated confirmation token 後面，**config snapshotted at plan time + protected paths digested by content** (sorted relative paths + per-file hash + symlink refusal + bounded)，**shell=False, stdin=DEVNULL, output to .soup/mcp-runs/<run_id>.log**, `soup runs` 看到 MCP 啟動的 run；(5) **Honest release engineering** — v0.73.3 release 內 24/24 PRs 都來自社區（8 contributors，5 個 first-time），**發現 4 個「validated by schema, documented, but read by nothing」的 config flags**，對「silent data corruption」誠實揭露（`BatchEncoding` not `dict` → label mask built from key strings → 0 trained tokens），對「Windows 259 == STILL_ACTIVE」也誠實揭露；(6) **完整 release notes with measurement withdrawal** — `benchmarks/gate-v0.73.1-measured-vram-fit.md` 公開測量記錄含 **3 個 withdrawn readings**（包含 v0.73.0 correctness repair 讓 32B −4.8%）；(7) **DOI paper + Product Hunt** + Discord + Trendshift；**(8) Open-source 同時有商業驗證** — Zenodo DOI `10.5281/zenodo.21771064`。

- **3W1H：**
  - **What：** Python 3.10-3.12 CLI (`soup-cli`) + YAML config 的 fine-tune / post-train 框架。Layer streaming (BETA) + QLoRA 4-bit/NF4 + Apple Silicon MLX + CUDA + 多 backend (PyTorch/MLX) + MCP integration + ecosystem (Ollama / Hugging Face / PEFT / SFT/DPO/RLHF)。
  - **Why：** 解決「想 fine-tune 8B model 但只有 4GB laptop GPU、SSHell into broken GPU box、要做 batch size / GPU detection / quantization 選擇、cloud-only 框架無法本地 fine-tune」的痛點。Soup 把 fine-tune 整條 pipeline 降到「one YAML + one command + local GPU」，**4GB VRAM 也能 bit-exact reproduce 8B fine-tune**，對 local-first 開發者友善。
  - **Who：** 在 consumer GPU / Apple Silicon / cloud 上 fine-tune LLM 的 LLM developer / ML researcher；想用 YAML config 取代 SSH/config hell 的 platform engineer；對「layer streaming + bit-exact reproduction + honest release engineering」有要求的 power user；想用 MCP 整合 fine-tune workflow 到 AI agent harness 的 builder。
  - **How：** (a) **PyPI 一行裝（pip, 主推）**：`pip install "soup-cli[train]"`（bare `soup-cli` 是 light CLI，加 `[train]` 才是完整 fine-tune）；(b) **基本 workflow**：`soup init --template chat` → 編輯 `soup.yaml` → `soup train`；(c) **Layer streaming 啟用**：在 `soup.yaml` 加 `stream_layers: true`（BETA, opt-in）— RTX 3050 Laptop 4GB Llama-3.1-8B-Instruct + NF4 實測 119.6 tok/s + 3.32 GB peak；(d) **驗證**：[Colab T4 proof notebook](notebooks/proof-4gb.ipynb) cap 4GB 後 assert streamed model bit-identical to normal one；(e) **MCP integration**：`soup mcp serve --allow-execute` 跑 gated execution，token 來自 server，config snapshotted at plan time；(f) **Measurement withdrawal honesty**：讀 [`benchmarks/gate-v0.73.1-measured-vram-fit.md`](benchmarks/gate-v0.73.1-measured-vram-fit.md) 看 v0.73.0 correctness repair 對 32B 的 −4.8% 影響。

- **安裝方式：**
  - **PyPI 一行裝（pip, 主推）**：
    ```bash
    pip install "soup-cli[train]"   # add [train] to fine-tune; bare `soup-cli` is the light CLI
    ```
    或 upgrade：
    ```bash
    pip install --upgrade "soup-cli[train]"
    soup version
    ```
    **Python 3.10–3.12**。
  - **基本 workflow**：
    ```bash
    pip install "soup-cli[train]"
    soup init --template chat
    soup train
    ```
  - **Layer streaming（4GB laptop GPU 跑 8B model, opt-in BETA）**：
    ```yaml
    # soup.yaml
    stream_layers: true
    ```
    Measured on RTX 3050 Laptop 4GB: Llama-3.1-8B-Instruct + NF4 at 119.6 tok/s, 3.32 GB peak — bit-exact vs normal run.
  - **驗證（Colab T4）**：[`notebooks/proof-4gb.ipynb`](notebooks/proof-4gb.ipynb) caps process to 4 GB → assert streamed model bit-identical to normal run.
  - **Recipe 範例**：`qwen3.5-4b-pretrain` + `deepseek-v4-flash-grpo`（v0.73.3 新增）。
  - **MCP integration**：`soup mcp serve --allow-execute` 啟動 gated execution（config snapshotted at plan time + protected paths digested by content）。
  - **PyPI 套件**：`soup-cli` latest `0.73.3` / **Python 3.10–3.12** / `Development Status :: 3 - Alpha` / `License :: OSI Approved :: Apache Software License` / **PyPI license = None but repo `license.spdx_id = "Apache-2.0"`**（**PyPI classifier 顯示 Apache-2.0 verified**）。

- **近期 release：** `v0.73.3` — **2026-08-18 11:21 UTC 發佈（台北時間 8/18 19:21, **14 天前 = 14 天內 fresh**）**, pre-release = false, draft = false。Body 主標題「Every one of the 24 pull requests in this release came from someone other than the maintainer — from eight people, five of whom appear here for the first time. What they found is the more interesting number: four separate config flags that were validated by the schema, documented, and then read by nothing.」**What's New — Four flags that did nothing**：(1) `training.bnb_4bit_use_double_quant` was read by nothing — every 4-bit construction site hardcoded `use_double_quant=True`，fix's design choice：field is `Optional[bool] = None`, not a plain `True`（**21 of 173 shipped configs** stopped round-tripping if not fixed，會 break `train --replay` + `soup sweep`）(#321); (2) Apple Silicon `quantization: 4bit` silently rewritten to `none` — `detect_device()` did not know MLX，量化決策 now explicit `resolve_quantization()` (0% → 100% coverage) (#423); (3) `soup train --no-reexec` printed launch command with user's own flags missing — two hand-maintained copies of "what the user typed"; the printed one is deleted and the hint now derives from the argv that actually launches the run (#372); (4) `data.interleave` validates, documents, parses — and nothing reads it at training time (#443)。**Silent data corruption in assistant-only masking**：`BatchEncoding` not `dict` → label mask built from mapping's **key strings** → no exception, no warning, normal-looking loss curve, **zero trained tokens** (#430)。**Windows: 259 == STILL_ACTIVE collision**：could wedge MCP execution cap shut indefinite (#424)。**New: `soup mcp serve --allow-execute`**：short-lived, single-use, **server-generated** confirmation token bound to plan + execution kind — no command/argv/shell string/client-supplied environment. `shell=False`, `stdin=DEVNULL`, output to `.soup/mcp-runs/<run_id>.log` (#297)。**Also**：`soup ship` leg-1 noise floor measured in judge task modes (#403) + `eval.ship.noise_floor` committable to `soup.yaml` (#406) + orphaned `running` MCP runs reconciled on read (#401) + one-active-execution cap gated on live persisted run (#402) + `detect_disk_kind` seeing through virtio (#365) + `MitigationLogWriter` no longer drops records when parent dir vanishes mid-run (#343) + `soup draft distill --steps N` delivers ~N instead of ~N/4.44 (#364) + `\boxed {A}` with space before brace no longer scores as no-answer (#396) + `soup env check` audits installed versions against Soup's declared bounds (#368) + `bom` + `attestation` as first-class registry artifact kinds (#309) + `qwen3.5-4b-pretrain` + `deepseek-v4-flash-grpo` recipes (#278, #279)。**Install/Upgrade**：`pip install --upgrade "soup-cli[train]"` + `soup version`；**Python 3.10–3.12**；no config migration needed (left unset still resolves to historical `True`)。**Security**：MCP gated execution (#297) closes real gap between planning and running；Windows process liveness (#424) 259 衝突 (#297 fix uses `digest_file` walks directory by content sorted relative paths + per-file hash + symlink refusal + bounded, not by mtime+size which didn't change when file *inside* protected directory rewritten — **model could previously be swapped between plan and execution and revalidation still passed**)。**Known Limitations**：#394 (MLX) remains open; #371 (reward-hack controller) remains open; #442 `soup data mix --live` writes overlay config that fails to load; #443 `data.interleave` has no training-time reader; #444 CUDA-gated GEMM-ceiling test flaky under machine load。**No gate record** accompanies this release — benchmarks/ is unchanged; the preprint is likewise untouched。**Repo `pushed_at` 2026-09-01 06:03 UTC（台北時間 9/1 14:03, **今天 main 仍 commit**）** + `created_at` 2026-02-20（**7 個月內從 0 衝到 4,239★**, niche-but-quality）+ 648 forks + 14,331KB size + **20 topics** (`cli`, `consumer-gpu`, `dpo`, `fine-tuning`, `gguf`, `huggingface`, `llm`, `llmops`, `local-ai`, `local-llm`, `lora`, `low-vram`, `machine-learning`, `ollama`, `peft`, `python`, `pytorch`, `qlora`, `sft`, `transformers`) + License = **Apache-2.0**（**case-A2 verified via file + PyPI classifier 顯示 Apache-2.0**, 含 patent grant clause）。**DOI paper**：[Zenodo 10.5281/zenodo.21771064](https://doi.org/10.5281/zenodo.21771064)。

## 重點觀察

- **Tier-A top 3 = 3/3 全 FRESH（cross-day 0/3 repeat floor ×4 VERIFIED）**：今天 Tier-A 排除 18 個 repeat (`tt-a1i/archify` 5 天 + `K-Dense-AI/scientific-agent-skills` 4 天 + `bilawalsidhu/gods-eye-view` 4 天 + `THU-MAIC/OpenMAIC` 08-31 + `p-e-w/heretic` 08-31 + `affaan-m/ECC` 4 天 + `agentscope-ai/AgentTeams` 08-30 + `perplexityai/bumblebee` 08-30 + `deepseek-ai/deepseek-harness` 08-29 + `memorax-ai/memorax-code` 08-29 + `anthropics/claude-plugins-official` 2 天 + `JetBrains/go-modern-guidelines` + `freestylefly/awesome-gpt-image-2` 2 天 + `mco-org/mco` 4 天 + `thedotmack/claude-mem` + `AgriciDaniel/claude-obsidian` + `DietrichGebert/ponytail` + `zedeus/nitter` + `PrimeIntellect-ai/prime-agent` 4 天) 後，**3 個 pick 都是 5 天窗口內 fresh**：`Osmantic/ODS` (pushed_at 9/1, 5628★) + `jingyaogong/minimind` (pushed_at 8/31, 1 天前, 56414★) + `zhaoxuya520/reverse-skill` (pushed_at 8/31, 1 天前, 33386★)。**0/3 cross-day repeat floor 已達 ×4（08-20 + 08-21 + 08-31 + 09-01）** — 符合 08-20 codification 「0/3 = natural floor = structurally optimal」標準連續 4 天。**Tier-A top 3 語言多樣**：Python (ODS Python tooling) + Python (minimind PyTorch) + Markdown/config (reverse-skill) — 偏 Python 重，但 Tier-B 補上 Node.js (paperclip) + Python (soup) = **5 picks = Python ×3 (ODS/minimind/Soup) + Node.js ×1 (paperclip) + Markdown/config ×1 (reverse-skill) = 2 語言、3 生態**，比 08-31 「3 語言」少一種但更集中。**注意 ODS release v2.6.0 是 35 天前 stale，但 `pushed_at` 9/1 = 今天 main 仍 commit；minimind v2 release 是 315 天前 stale（2025-10-21）但 `pushed_at` 8/31 = 1 天前 active；reverse-skill v1.0.1 是 24 天前 stale 但 `pushed_at` 8/31 = 1 天前 active** — **3/3 都是 pre-stable-like cadence**（main branch 仍 active 開發但 release tag cadence 較慢），這是 release 成熟度低的 open-source 專案常見 pattern。
- **License 乾淨度 split = 4 permissive + 0 strong-copyleft + 1 permissive**：4/5 permissive (case-A + case-A2)：**ODS** Apache-2.0 (含 patent grant clause) + **minimind** Apache-2.0 + **reverse-skill** MIT + **paperclip** MIT + **Soup** Apache-2.0 (含 patent grant clause) = **5/5 permissive (100%)**，**0/5 strong copyleft**。**比 08-30/31 4/5 permissive (80%) 高 20pp**，今天 100% permissive 全 clean。對主人 horo-agent / horo-webui air-gapped downstream 影響 = **0 strong-copyleft flag**（最 clean 一天）。**ODS + minimind + Soup = 3/5 Apache-2.0**（含 patent grant clause），closed-source fork 安全度比 MIT 更高；**reverse-skill + paperclip = 2/5 MIT**，純 permissive。
- **5 picks 5 種 install type 全 distinct（install type diversity is the new bar ×4 VERIFIED）**：今天命中：**Type-2 OS-agnostic installer（curl-pipe-to-bash）**（**ODS** `curl -fsSL https://install.osmantic.com/ods.sh | bash` 一鍵裝 Ollama + Open WebUI + n8n + ComfyUI 全棧）+ **Type-22 git clone + scripts refresh-tool-index（platform-specific scripts）**（**reverse-skill** `git clone` + `skills/scripts/refresh-tool-index.{ps1,sh}` 跨 Windows/Linux/macOS/Kali 4 平台）+ **Type-21 npm install with sha256 verification + multi-channel release**（**paperclip** `curl install.sh + sha256sum -c + bash install.sh` + npm `paperclipai` v2026.824.1 with `latest`/`beta`/`nightly`/`canary` 4-channel + bind preset loopback/lan/tailnet 3 種）+ **Type-1 PyPI one-liner + git clone from source**（**minimind** `git clone --depth 1` + `pip install -r requirements.txt` + `python train_full_sft.py` 訓練腳本 + Ollama + vLLM 第三方推理）+ **Type-1 PyPI one-liner + layer streaming + MCP integration**（**Soup** `pip install "soup-cli[train]"` + `soup init --template chat` + `soup train` + `stream_layers: true` BETA opt-in + `soup mcp serve --allow-execute`）。**5 picks = 5 distinct install paths** 達 **install type diversity ×4 VERIFIED（08-20 + 08-21 + 08-31 + 09-01）**。
- **Release 新鮮度 tier-split（5 picks）**：超 fresh (≤1 天) = **0**、fresh (≤14 天) = **1** (`Soup` v0.73.3 14 天前)、**Tier-A fresh by `pushed_at` (≤1 天)** = **2** (`minimind` 8/31 + `reverse-skill` 8/31 + `paperclip` 9/1 + `Soup` 9/1 + `ODS` 9/1 = 5/5 `pushed_at` fresh 但 release tag 慢)、stale (>30 天) = **3** (`ODS` v2.6.0 35 天前 + `minimind` v2 315 天前 + `reverse-skill` v1.0.1 24 天前邊界)、Tier-B 全部 (`paperclip` v2026.824.1 7 天前 + `Soup` v0.73.3 14 天前) 都是 fresh。**5/5 = 100% `pushed_at` active** 是 14:00 系列本月最強 liveness signal，**1/5 (Soup) = 20% fresh (≤14 天 release)** 是本月 tier-A freshness 中下，**0/5 zero-GH-release** = 0 picks 沒 release = **5/5 = 100% release-tag cadence** 是 14:00 系列本月 **9 個 100% release-tag milestone**（08-12/13/14/15/17/20/21 + 08-31 + 09-01 共 9 個 100% milestone, 09-01 是其中之一）。**Operational reading**：今天 3 個 stale release (`ODS` 35 天 + `minimind` 315 天 + `reverse-skill` 24 天) 都是 **pre-stable-like cadence**（main branch 仍 active 開發 + commit-rolling 但 release tag 沒追上）— 對 power user 用 `git pull main` 是更 fresh path。
- **Master MEMORY signal 命中**：今天 **1/5 picks fire Hermes-as-host-adjacent signal**：(a) **`paperclipai/paperclip`** README 第一段直接寫 **「If OpenClaw is an employee, Paperclip is the company」** 明確與 OpenClaw 互補定位 — **OpenClaw 整合命中 Master MEMORY**，因為 MEMORY 內明確標出「多代理偏好 default（Qwen）直接建立／路由 Kanban task 給 executor／reviewer」+ 「OpenClaw, QwenPaw, and Hermes Workers coexist」（08-30 `agentscope-ai/AgentTeams` README 內已記錄）；paperclip 把 OpenClaw 從「employee」上升到「company」層級（org charts / budgets / governance / goal alignment），**對 Master 想要 multi-agent company-level orchestration 是直接套用價值**。**1/5 picks fire MEMORY-adjacent signal** 略低於 5-tick rolling average ≈ 1.4/5（08-27 = 1/5, 08-28 = 1/5, 08-29 = 2/5, 08-30 = 1/5, 08-31 = 2/5）但仍 within sustainable range。今天其他 4 picks 雖然沒直接命中 MEMORY，但都跟 Master 「繁體中文 / Qwen + Hermes / 累積多個 domain 小工具 / 工具間 composition / air-gapped downstream」相關：`Soup` 跟 Master 本地 fine-tune 興趣 + `low-vram` 主機偏好對齊；`reverse-skill` 跟 Master 對 agent skill router + AI security 興趣對齊；`ODS` 跟 Master 對 self-host AI server 偏好對齊；`minimind` 跟 Master 對 LLM 訓練 + 中文理解偏好對齊。
