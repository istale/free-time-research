---
title: GitHub 自由探索 2026-09-08（14:00 台北時間）
date: 2026-09-08
tags:
  - github-activity-patrol
  - 14-00
  - free-exploration
sources:
  - GitHub Trending daily（Tier-A top 3 — 排除前 7 天 repeat）
  - Web search（kvcache-ai/ktransformers — September 2026 AI release tracker 多項報導，清華 MADSys Lab 維護的 CPU-GPU 異構 LLM 推論 + 微調框架，v0.7.0 @ 2026-08-17 擴 Full Fine-Tuning + AMD CPU AVX512 + DeepSeek-V3.1 原生 FP8 LoRA；OpenCut-app/OpenCut — 9 月多篇 round-up 文章列入「值得關注」，MIT、88k★、v0.3.0 @ 2026-04-15 + 重寫中 Rust core + MCP server 路線圖）
  - GitHub REST API `/repos/<slug>` + `/releases/latest` + raw `README.md`
---

# GitHub 專案動態

- 檢查時間：2026-09-08（14:00 台北時間）
- 檢查對象：`heygen-com/hyperframes` / `microsoft/markitdown` / `mksglu/context-mode` / `kvcache-ai/ktransformers` / `OpenCut-app/OpenCut`
- 來源組合：GitHub Trending today Tier-A 排除前 7 天 repeat（**fresh top 3 = `heygen-com/hyperframes` rank 1（474★/day，TypeScript，Apache-2.0，HeyGen 開源的「HTML → MP4」視訊框架，內建 AI agent skill + frame.md 設計系統）+ `microsoft/markitdown` rank 2（886★/day，Python，MIT，Microsoft 官方 file→Markdown 工具，v0.1.8b1 prerelease 09-04）+ `mksglu/context-mode` rank 3（96★/day，TypeScript，ELv2 license，AI coding agent context window optimization，宣稱 98% 工具輸出縮減、17 平台 routing）**— 排除 `affaan-m/ECC`（rank 6，09-01/03/04/05/06/07 repeat×6）+ `coreyhaines31/marketingskills`（rank 7，09-07×1）+ `The-Swarm-Corporation/AutoHedge`（rank 8，08-XX prior repeat）+ `BraveOPotato/FckSignups`（rank 9，清單型不算 3W1H 主力）+ `bytedance/deer-flow`（rank 10，09-XX prior repeat）+ `openai/skills`（rank 11，09-07×1 且 self-deprecated）+ `ruvnet/ruflo`（rank 14，09-06×1））+ Web search 2 = `kvcache-ai/ktransformers`（olud.ai September 2026 release tracker 收錄 v0.7.0 重點，**清華 MADSys Lab 維護的「CPU-GPU 異構 LLM 推論 + LLaMA-Factory 微調」框架**，Python + Apache-2.0，19k★，v0.7.0 @ 2026-08-17 五大更新：Full AVX512 LoRA / 原生 FP8 LoRA for DeepSeek-V3.1 / Qwen3-VL MoE 微調 / DeepSeek V4 Docker 快速部署 / CPU activation reuse）+ `OpenCut-app/OpenCut`（「10 Free GitHub Repos Everyone's Using Right Now (Sept 2026)」第 3 名 + 多篇 round-up 文章列入，**TypeScript + MIT，88k★，「CapCut 開源替代」網頁版影片剪輯器**，v0.3.0 @ 2026-04-15（5 個月 stale release）但 `pushed_at` 2026-08-10 = 28 天內，主分支正在做 Rust core 重寫 + MCP server + headless mode 大改）。

---

## Repo 摘要與 3W1H

### `heygen-com/hyperframes`

- **Repo 摘要：** HeyGen 官方開源的「HTML → MP4」影片生成框架，把 HTML / CSS / GSAP / Lottie / Three.js / Anime.js 這些原本給網頁用的動畫 runtime 收進單一 deterministic video pipeline，**內建 26 個 agent skill（`/hyperframes` 路由 + 9 個 creation workflow + 8 個 domain skill）**，還有 `frame.md` design token 系統把網頁 design spec 翻譯成「for camera」用的 frame spec。46,585★、TypeScript、Apache-2.0、412 MB repo size、活躍度高（`pushed_at` 2026-09-08 1:28 UTC = 今天凌晨）。適合產品 launch video / PR walkthrough / talking-head recut / motion graphics / 從網站 URL 生成 explainer video；對「要把 agent 變成影片工的人」這條線，比之前 09-06 的 `harry0703/MoneyPrinterTurbo` 純 Python + Streamlit 那條線更現代化。
- **3W1H：**
  - **What：** TypeScript + Node.js 22+ 的 video 框架（不是 library、不是 SaaS；既是 CLI 工具也是 skill 包）。
  - **Why：** 影片剪輯的痛點是「跨 runtime 整合」（GSAP + Lottie + Three.js + CSS keyframes 各做各的）加上「AI 產出難以 deterministic」（同 prompt 重跑出不同影片）。HyperFrames 把這兩層都收進同一個 framework——透過 `data-*` 時間軸屬性、`<hf-audio-group>` submix bus、`hyperframes doctor` 等約束，讓 agent 寫的影片也 seek-safe、可重跑。最新 v0.8.31（09-07）跟 v0.8.30（09-06）、v0.8.29（09-05）、v0.8.28（09-05）、v0.8.27（09-03）連發，五天內 5 個 patch release，**今天 5 個 pick 裡 release cadence 最熱的**。
  - **Who：** 產品經理 / 行銷 / developer relations / 教學內容創作者，要把網站 / PR / podcast 變成短影音的人；以及想做 batch video generation pipeline 的人。
  - **How：** 兩條路徑——(1) **給 AI coding agent：** `npx skills add heygen-com/hyperframes`，在 Claude Code / Codex / Cursor / Hermes 內呼叫 `/hyperframes` 路由，描述需求（ex: 「Using `/hyperframes`, create a 10-second product intro with a fade-in title, a background video, and subtle background music」）；(2) **手動 CLI：** `npx hyperframes init my-video` → `npx hyperframes preview`（瀏覽器 live reload）→ `npx hyperframes render`（產 MP4）。兩條路徑 requirement：Node.js 22+ + FFmpeg。
- **安裝方式：**
  - **npx（agentskills.io CLI，給 AI agent 裝 skill）：**
    - `npx skills add heygen-com/hyperframes`（裝全部 skill——但會裝 26 個 published skills + 6 個 repo-internal）
    - `npx hyperframes skills update`（non-interactive 跑，建議給 agent / CI 走這條，只裝 core 6 個）
    - 個別 skill：`npx hyperframes skills add <name>`（ex: `hyperframes-core`、`hyperframes-cli`、`hyperframes-audio`、`figma`）
  - **npx（手動 CLI，給開發者裝）：**
    - `npx hyperframes init my-video` → 進專案資料夾 → `npx hyperframes preview` → `npx hyperframes render`
    - 需求：Node.js 22+ + FFmpeg（系統 PATH 找得到）
  - **雲端 / Lambda 渲染：** `npx hyperframes cloud render`（HeyGen hosted）或 `npx hyperframes lambda deploy / render / progress`（自架 AWS Lambda 渲染，繞過本地 FFmpeg）
  - **未提供 pip / brew / apt 路徑**——整個 stack 是 Node.js + FFmpeg，不走 Python 或系統套件。
- **近期 release：** **`v0.8.31` — published 2026-09-07 16:17 UTC（**台北時間今天凌晨 0:17，距現在 < 14 小時**）**。release cadence：v0.8.27 (09-03) → v0.8.28 (09-05) → v0.8.29 (09-05) → v0.8.30 (09-06) → v0.8.31 (09-07) = **5 天 5 版**。GitHub Trending 今天 `stars_today: 474`，**與 microsoft/markitdown 一起是今天 5 個 pick 裡唯二當週新發版的 Tier-A pick**。**對主人的 takeaway**：HyperFrames 的 `frame.md` 設計系統是「網頁 design.md 翻譯成 for-camera 用的 frame spec」這個概念——可直接套用主人「先在 common 場域驗證 hypothesis 再遷 niche」的工作節奏，套到「先寫 design spec 再翻成影片」這個 workflow。

### `microsoft/markitdown`

- **Repo 摘要：** Microsoft 官方 Python utility，把 PDF / Word / Excel / PowerPoint / 圖片（含 EXIF + OCR）/ 音訊（含 EXIF + 語音轉錄）/ HTML / CSV / JSON / XML / ZIP / YouTube URL / EPub 等 12+ 種檔案格式轉成 **LLM-friendly Markdown**——保留 heading / list / table / link 結構，token 友善。180,758★、Python、MIT、4.6 MB repo size，**今天 Trending #2（886★/day = 今天 5 個 pick 裡星速最高的）**。Microsoft 出品、生態成熟、README 第一段就開門見山警告「MarkItDown performs I/O with the privileges of the current process... Sanitize your inputs in untrusted environments」—— 安全 caveat 寫在最前面是 Microsoft 的招牌風格。適合要把企業內文件 / 報告 / 投影片餵進 LLM / RAG / agent pipeline 的人。
- **3W1H：**
  - **What：** Python CLI / Python library + plugin 系統（`markitdown <file>` 把檔案轉 `.md`）。
  - **Why：** 文件 → LLM pipeline 的痛點是「LLM 吃 plain text 最舒服、吃 binary 很費 token、吃 Office binary 更貴」。Markdown 在這中間——保留結構又 token-efficient（GPT-4o / Claude 都「native speak」Markdown）。MarkItDown 把 12+ 種格式的轉換收進單一 CLI / Python API，外加 plugin system（`markitdown --list-plugins` / `markitdown-ocr` 用 LLM Vision 抽 embedded image OCR / Azure Content Understanding 整合）。
  - **Who：** 任何要把企業文件 / 投影片 / Excel / PDF 餵進 LLM / RAG 的人；agent builder（要把檔案當 tool 餵進 agent）；knowledge engineer（建 knowledge base 前的 format-normalize）。
  - **How：** `markitdown path-to-file.pdf > document.md` 或 `markitdown path-to-file.pdf -o document.md`；Python API：`from markitdown import MarkItDown; md = MarkItDown(); print(md.convert("file.pdf").markdown)`。optional deps 拆得很細：`markitdown[pdf, docx, pptx]`、`markitdown[az-doc-intel]`、`markitdown[az-content-understanding]`（Azure 多模態，含 audio + video）、`markitdown[audio-transcription]`、`markitdown[youtube-transcription]`。
- **安裝方式：**
  - **pip / uv（標準 PyPI 套件）：**
    - `pip install 'markitdown[all]'`（一次裝齊全部 optional deps）
    - `pip install 'markitdown[pdf, docx, pptx]'`（精簡裝）
    - uv：`uv venv --python=3.12 .venv` → `source .venv/bin/activate` → `uv pip install 'markitdown[all]'`
  - **conda：** `conda create -n markitdown python=3.12` → `conda activate markitdown` → `pip install 'markitdown[all]'`
  - **從 source：** `git clone git@github.com:microsoft/markitdown.git` → `cd markitdown` → `pip install -e 'packages/markitdown[all]'`
  - **Python 版本要求：** Python 3.10+（建議 3.12）
  - **未提供 npm / brew 路徑**——純 Python CLI / library。
- **近期 release：** **`v0.1.8b1` — 「Version 0.1.8b1」（prerelease），published 2026-09-04 03:46 UTC（台北時間 09-04 上午 11:46，距現在 4 天）**。本版是 prerelease，body 開頭明寫「**This prerelease includes numerous small bug fixes. None adds new features or is expected to change output in typical cases. Nevertheless, the volume of changes warrants a cautious rollout. Please report any issues or regressions.**」約 30+ 個 PR：PPT chart O(n²) 修正、LaTeX μ/ν/τ/↓ macro 修正、PPTX SVG image 沒 rasterized fallback 修正、RSS RecursionError 修正、CSV UTF-8 BOM + 空行修正、strikethrough preservation（`\<strike\>` + CSS `line-through`）、MARKITDOWN_CU_ENDPOINT / MARKITDOWN_DOCINTEL_ENDPOINT CLI flag 等。**前一版 stable `v0.1.7` 是 2026-07-29（41 天前），b1 算是這段累積 bug-fix 的謹慎 batch release**。

### `mksglu/context-mode`

- **Repo 摘要：** 給 AI coding agent 用的「context window optimization + persistent session memory + 跨 17 平台 routing」一條龍外掛，**宣稱 MCP 工具輸出 98% 縮減**、context 從 30 分鐘 40% 滿載降到 << 滿載；底層用 FTS5 做 persistent local index、PreToolUse / PostToolUse / UserPromptSubmit / PreCompact / SessionStart / Stop 共 6 個 hooks、11 個 MCP tool（6 個 sandbox + 5 個 meta）。21,022★、TypeScript、**ELv2 license（Elastic License v2 — source-available 不是 OSI-approved open source）**、31 MB、**`pushed_at` 2026-09-07 = 今天才 push 過**。README 自家報告 enterprise 客戶名單：Microsoft / Google / Meta / Amazon / IBM / NVIDIA / ByteDance / Stripe / Datadog / Salesforce / GitHub / Red Hat / Supabase / Canva / Notion / Hasura / Framer / Cursor 共 18 家。適合想要「把 agent 的 context 從揮霍變成可控資源」的人。
- **3W1H：**
  - **What：** Node.js CLI + MCP server + 17 平台 agent host 的 plugin（TypeScript 為主，Elv2 license）。
  - **Why：** AI coding agent 的「context window 滿了就壞」是當前最大的 silent killer——MCP Playwright snapshot 56 KB / 20 條 GitHub issue 59 KB / 1 條 access log 45 KB，30 分鐘後 40% context 被這些 raw output 吃完。Context Mode 的解法是「在 tool 結果進 context 前先 sandbox + index + 摘要」，需要時才 `ctx_fetch_and_index` 把資料撈回來——這與 09-07 NVIDIA SkillSpector 的「裝 skill 前先掃安全」是同一思路（都把「不可逆的資料進入 agent」這層設 gate），但 SkillSpector 擋在「skill 進入」boundary，context-mode 擋在「tool 結果進入」boundary。
  - **Who：** Claude Code / Codex / Gemini CLI / Cursor / Copilot / Hermes / OpenCode / Pi 等 17 種 agent host 的重度用戶；做企業內部 AI 工具 + context budget 控管的團隊。
  - **How：** 標準流程（以 Claude Code 為例）：`/plugin marketplace add mksglu/context-mode` → `/plugin install context-mode@context-mode` → 重啟 Claude Code → 跑 `/context-mode:ctx-doctor` 驗證全部 `[x]`。驗證後所有 tool 自動被 PreToolUse hook 攔截並 sandbox。需要看統計 `/context-mode:ctx-stats`、re-index `/context-mode:ctx-index`、search `/context-mode:ctx-search`。
- **安裝方式：**
  - **Claude Code plugin（install type-20 — plugin marketplace + per-host command）：**
    ```bash
    /plugin marketplace add mksglu/context-mode
    /plugin install context-mode@context-mode
    ```
    重啟 Claude Code 或 `/reload-plugins`，跑 `/context-mode:ctx-doctor` 驗證。
  - **Gemini CLI：** `npm install -g context-mode` + 編輯 `~/.gemini/settings.json` 註冊 MCP server + 4 個 hooks（BeforeTool / AfterTool / PreCompress / SessionStart）+ 重啟 Gemini CLI。
  - **VS Code Copilot：** `npm install -g context-mode` + 建立 `.vscode/mcp.json` + `.github/hooks/context-mode.json` + 重啟 VS Code。
  - **MCP-only install（no hooks / no slash commands，輕量先試）：** `claude mcp add context-mode -- npx -y context-mode`——拿到 11 個 MCP tool 但 routing 是 optional（agent 不會自動偏好它們）。
  - **Node.js 版本：** >= 22.5（或 Bun）
  - **未提供 pip / brew 路徑**——整個 stack 是 Node.js + MCP。
- **近期 release：** **`v1.0.169` — published 2026-06-29 18:18 UTC（距今 71 天）**。**0 個 Tier-A pick 釋出日 release 的今天，這是 release 標籤最 stale 的一個**（hyperframes v0.8.31 昨天、markitdown v0.1.8b1 4 天前、ktransformers v0.7.0 22 天前、OpenCut v0.3.0 145 天前）——但 `pushed_at 2026-09-07` 證明 main 分支今天還在動。**更新模式**：v1.0.165 (06-22) → v1.0.166 (06-23) → v1.0.167 (06-26) → v1.0.168 (06-26) → v1.0.169 (06-29) = **連 8 天每天一版 + 突然停在 v1.0.169 = 71 天無新版 release**，符合「研發節奏強但 release 治理延遲」的 pattern。**操作 takeaway**：read `pushed_at` 而非 release 標籤——這個 repo 對今天的 star gain 是 release-cadence 的庫存量撐起來的，不是新版本。**License 警示**：ELv2 (Elastic License v2) 是 **source-available 但非 OSI-approved open source**——禁止用於「提供競爭性 SaaS 服務」，對主人 horo-agent / horo-webui 內部用 OK，對外發佈 SaaS 一定要先讀 license 全文。商用嵌入前請逐條驗。

### `kvcache-ai/ktransformers`

- **Repo 摘要：** 清華大學 MADSys Lab + Approaching.AI + 9#AISoft 共同維護的「**CPU-GPU 異構 LLM 推論 + 微調**」框架，目標是把 DeepSeek-V3 / R1 / Qwen3-MoE / Kimi-K2.5 / GLM-5 這些超大 MoE 模型塞進 **單張消費級 GPU（24GB VRAM）+ 大記憶體主機（382GB+ DRAM）** 的組合跑起來，靠 CPU-GPU expert scheduling 把 hot expert 放 GPU / cold expert 放 CPU / disk 三層分層；同時跟 SGLang / LLaMA-Factory 深度整合。19,478★、Python、Apache-2.0、67 MB repo size。**SOSP 2025 最佳論文（10.1145/3731569.3764843）的對應開源實作**，引用價值高。適合要做 MoE 模型本地部署 / 個人 / 團隊 fine-tune 的人。
- **3W1H：**
  - **What：** Python framework（核心 `kt-kernel` C++/CUDA extension + Python bindings），提供 Inference 跟 SFT 兩條主軸。
  - **Why：** 大 MoE 模型（671B - 1T+ params）的部署痛點是「一張 H100 不夠塞完整 BF16 權重」。KTransformers 的解法是 **3 層 prefix cache reuse + CPU-GPU 異構 expert 排程 + AMX/AVX-512/AVX2 量化 kernel + ROCm / Intel Arc / 華為 Ascend NPU 多 backend**——v0.7.0 還把 DeepSeek-V3.1 的 host-memory 從 1.4 TB（BF16 展開）壓到 800 GB（原生 FP8 LoRA），**訓練速度比 ZeRO-Offload 快 6-12 倍**（benchmarked MoE SFT workload）。對主人 horo-agent 下游 / 個人 fine-tune 路線有直接 reuse 價值。
  - **Who：** 想本地跑 DeepSeek-V3/R1、Qwen3-MoE、Kimi-K2.5、GLM-5.x、MiniMax-M3 等 MoE 模型的人；研究機構 / 個人 developer；雲端低成本 SFT 需求（與 AutoDL 整合）。
  - **How：** 兩條 entry point——(1) **Inference：** `git clone` → `cd kt-kernel` → `pip install .` → 看 [`kt-kernel/README.md`](./kt-kernel/README.md) 與各 model tutorial（DeepSeek-V4-Flash、GLM-5.3-Flash、Qwen3-Next、MiniMax-M3 等）；(2) **SFT：** 進 LLaMA-Factory repo → `python -m pip install -e .` → `python -m pip install "ktransformers[sft]==0.7.0"` → `python -m pip install "sglang-kt==0.7.0"` → `accelerate launch --config_file examples/ktransformers/accelerate/fsdp2_kt_int8.yaml src/train.py examples/ktransformers/train_lora/qwen3_5moe_lora_sft_kt.yaml`。
- **安裝方式：**
  - **Inference 安裝：**
    ```bash
    git clone https://github.com/kvcache-ai/ktransformers.git
    cd ktransformers/kt-kernel
    pip install .
    ```
  - **SFT / Fine-tune 安裝（與 LLaMA-Factory 整合）：**
    ```bash
    cd /path/to/LLaMA-Factory
    python -m pip install -e .
    python -m pip install "ktransformers[sft]==0.7.0"
    python -m pip install "sglang-kt==0.7.0"
    ```
  - **v0.7.0 release YAML 配置（自動選擇 AMX / AVX512）：**
    ```yaml
    kt_config:
      kt_backend: auto   # 自動選 AMX (Intel) 或 AVX512 (AMD/Intel)
    ```
  - **v0.7.0 CPU activation reuse（要更多 host memory 換 throughput）：**
    ```yaml
    kt_cpu_activation: retain
    ```
  - **DeepSeek V4 Docker 快速部署：** v0.7.0 新增的 docker workflow，免手動 build runtime stack。
  - **整合：** SGLang integration（PR/roadmap：[lmsys.org/blog/2025-10-22-KTransformers/](https://lmsys.org/blog/2025-10-22-KTransformers/)）+ LLaMA-Factory integration（fine-tune 主線）+ AutoDL 雲端低價訓推 + Intel Arc GPU（[`doc/en/xpu.md`](./doc/en/xpu.md)）+ ROCm AMD GPU（[`doc/en/ROCm.md`](./doc/en/ROCm.md)）+ 華為 Ascend NPU（[`doc/zh/DeepseekR1_V3_tutorial_zh_for_Ascend_NPU.md`](./doc/zh/DeepseekR1_V3_tutorial_zh_for_Ascend_NPU.md)）+ GLM-5/5.2/5.3、MiniMax-M2.1/M2.5/M3、Kimi-K2.5、Kimi-K2-Thinking、DeepSeek-V3/R1/V4-Flash、Qwen3-Next、SmallThinker、GLM4-MoE、Mixtral 8x7B/8x22B、llama.cpp unsloth 1.58/2.51 bit IQ1_S/FP8 hybrid weights 等 20+ model Day0 support。
  - **未提供 `pip install ktransformers` 直接裝**——要走 git clone + 手動 install 路徑，因為核心是 C++/CUDA extension。
- **近期 release：** **`v0.7.0` — 「KTransformers v0.7.0: Support Full Fine-Tuning and AMD CPU (AVX512) for Fine-Tuning!」，published 2026-08-17 08:29 UTC（台北時間 08-17 下午 4:29，距今 22 天）**。本版五大主軸：
  1. **Full AVX512 LoRA fine-tuning**——`kt_backend: auto` 自動偵測，MoE expert 訓練可在沒有 AMX 的 AMD server 跑（**直接解主人若買 AMD 伺服器的痛點**）。
  2. **原生 FP8 LoRA for DeepSeek-V3.1**——直接讀 block-wise E4M3 routed-expert weights，不展開 BF16，host memory 從 1.4 TB 壓到 **800 GB**。
  3. **Qwen3-VL MoE fine-tuning**（PR #2156）——多模態 data 走 distributed forward / backward + optimizer + checkpoint save。
  4. **DeepSeek V4 Docker 快速部署**——standardized workflow，免手動配 runtime stack。
  5. **CPU activation reuse**（`kt_cpu_activation: retain`）——trade CPU memory 換 throughput，activation checkpoint recompute 期間保留 CPU expert activations。
  釋出 regression 涵蓋 Qwen3.5-397B-A17B BF16 LoRA on 2 GPUs / DeepSeek-V3.1 native FP8 LoRA on 4 GPUs / Qwen3-VL-30B-A3B-Instruct BF16 LoRA with FSDP2（**含 multimodal preprocessing + checkpoint save**）/ finite training loss / non-zero LoRA param updates / distributed FSDP2 / FP8 loading。
  
  **對主人的 takeaway**：今天 5 個 pick 裡唯一一個 release 主軸**直接命中主人潛在硬件採購 + 本地 LLM 部署需求**——AMD AVX512 fine-tuning、MoE 800GB FP8 LoRA、Qwen3-VL 多模態微調，都是 2026 Q3 的「個人 / 團隊 fine-tune 大 MoE」剛需。SOSP 2025 paper citation 是學術 credibility 的硬通貨。

### `OpenCut-app/OpenCut`

- **Repo 摘要：** 開源的「CapCut 替代品」——**TypeScript + Rust 重寫中的**跨平台影片剪輯器，目標「從一個 codebase 出 web / desktop / mobile + MCP server + headless mode + first-class plugin」。88,956★、TypeScript、MIT、28 MB repo size、**`pushed_at` 2026-08-10 = 28 天內**。目前 **production-ready 版本**是 [`opencut-app/opencut-classic`](https://github.com/opencut-app/opencut-classic)（網頁版），主分支是 Rust core + Editor API 重寫——README 第一行直接說「**OpenCut is being rewritten from the ground up. What's coming: An Editor API, First-class third party plugins, Desktop, mobile, and browser from one codebase (Rust core), MCP server (for AI agents), Headless mode (automation, batch rendering), A scripting tab directly in the editor**」。適合想要「CapCut-like 開源 + AI agent 可程式化操控 + 不綁定商業平台」的人。
- **3W1H：**
  - **What：** TypeScript（apps/web）+ Rust（apps/desktop via Tauri + 將成為 Rust core）+ monorepo（`apps/` + `packages/` + `rust/`），Moon 為 task runner，proto 為 toolchain manager。
  - **Why：** CapCut / Premiere 等剪輯工具的痛點是「閉源 + 商業綁定 + 不能 AI agent 操控」。OpenCut 把這三層都打掉——MIT + 自己 host + MCP server 讓 agent 能剪輯 + 頭less mode 讓 batch rendering 可程式化 + 第一方 plugin API 讓社群擴充。**CapCut 「免費 feature 全部 paywall」事件**（2025-2026）逼出這個 fork 社群，最近半年急速長到 88k★。
  - **Who：** 內容創作者 / 行銷 / 教育 / 任何需要「不被商業平台綁架」的影片剪輯 + 想把 AI agent 接上剪輯 workflow 的人。
  - **How：** **目前 production-ready：** 直接到 [`opencut.app`](https://opencut.app/) 用網頁版（底層仍是 classic TypeScript 實作）；**開發者模式：** clone 後 `bash <(curl -fsSL https://moonrepo.dev/install/proto.sh)`（裝 proto toolchain manager）→ `proto use`（讀 `.prototools` 鎖定版本）→ `moon run web:dev`（localhost:5173）/ `moon run api:dev`（localhost:8787）/ `moon run desktop:dev`（Tauri desktop，需讀 `apps/desktop/README.md`）。**目前不接受 outside contributions**——「We're not set up to take outside contributions yet while the architecture is being designed」——這是觀察期，不是 fork 機會。
- **安裝方式：**
  - **未找到明確 pip/npm 安裝方式**——OpenCut 是「用瀏覽器 + 下載 desktop app」，沒有 library install 路徑。
  - **使用者路徑：**
    - **網頁版：** 開 [`opencut.app`](https://opencut.app/)（classic TS 實作，目前 production-ready）
    - **新版本預覽：** [`new.opencut.app`](https://new.opencut.app/)（Rust core 重寫中，會逐漸取代 classic）
  - **開發者路徑（clone + proto + moon）：**
    ```bash
    # Linux, macOS, WSL
    bash <(curl -fsSL https://moonrepo.dev/install/proto.sh)
    # Windows (PowerShell)
    irm https://moonrepo.dev/install/proto.ps1 | iex
    
    # 跑專案
    git clone https://github.com/OpenCut-app/OpenCut.git
    cd OpenCut
    proto use                  # 讀 .prototools 安裝 pinned toolchain
    moon run web:dev           # localhost:5173
    moon run api:dev           # localhost:8787
    moon run desktop:dev       # 需讀 apps/desktop/README.md
    ```
  - **PowerShell shim 設定（如果 shim 沒跑起來）：** `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`
  - **未提供 brew / apt / npm global / pip**——純 source + custom toolchain。
- **近期 release：** **`v0.3.0` — published 2026-04-15 02:04 UTC（**台北時間 04-15 上午 10:04，距今 145 天 = 5 個月 stale**）**。本版是「最後一個 classic TS 大改版」，亮點：masks（split / rectangle / ellipse / star / heart / diamond / cinematic bars）、keyframe curves 的 graph editor、volume + speed controls（含 maintain pitch 避免 chipmunk effect）、preview zoom、canvas backgrounds、stickers、redesigned settings panel、sponsor logos dark mode 修正、timeline 自動捲到最底讓 main video track 直接可見。**前三版節奏：v0.1.0 (02-23) → v0.2.0 (03-02) → v0.3.0 (04-15) = 12 天 + 45 天**，但 `pushed_at 2026-08-10` 證明 main 分支（Rust core 重寫）仍持續動。**release 標籤 stale ≠ 專案 stale**——這是 classic TS / new Rust 雙軌切換的過渡期。**對主人的 takeaway**：88k★ 體量 + MIT + 重寫中 MCP server 路線圖 + headless mode 路線圖，是 2026 Q4 / 2027 Q1 觀察「開源影片剪輯器 + AI agent」賽道的標的，但目前**不可直接拿來上 production**——觀察 GitHub Trending 持續進榜的頻率 + 等 Rust core + MCP server + Editor API 三件套全部 GA。

---

## 重點觀察

- **Release freshness 今天呈現「v0.x 快速迭代 vs 過渡期 stale release」兩種節奏。** 5 個 pick 有 4 個有 GH release：`heygen-com/hyperframes` v0.8.31（**昨天，< 14 小時內**）+ `microsoft/markitdown` v0.1.8b1（4 天前 prerelease）+ `kvcache-ai/ktransformers` v0.7.0（22 天前，**5 個主軸大改版**）+ `OpenCut-app/OpenCut` v0.3.0（145 天前，**過渡期 stale 但 pushed_at 28 天內**）；`mksglu/context-mode` v1.0.169（71 天前 + `pushed_at` 今天剛 push）。**hyperframes 連 5 天 5 版 = 5★/天 release cadence** 是本月 14:00 觀察到的最熱小型 framework；context-mode / OpenCut 走「main branch pushed_at 撐研發節奏但 release tag 不跟」的過渡型，與 09-04 `ifm-ai/uno` / 09-06 `humanlayer/skills` 同類型——判讀 release freshness 要看 `pushed_at` 與 release tag 的差，**別把 stale release tag 當 inactive 訊號**。
- **安裝門檻覆蓋 5 種零重複 install path，符合 09-04 以來的「install-type 多樣化」紀律。** 最低是 `mksglu/context-mode` 一行 `npm install -g context-mode`（type-10）+ plugin marketplace（type-20 dual path）；中間是 `heygen-com/hyperframes` 一行 `npx skills add heygen-com/hyperframes`（type-20）+ `npx hyperframes init` CLI（type-2 npm library）；中上是 `microsoft/markitdown` 一行 `pip install 'markitdown[all]'`（type-1 PyPI with optional deps）；高是 `OpenCut-app/OpenCut` 走 proto + moon + Rust core 重寫（type-18 source-only 多 SDK + monorepo）；最高是 `kvcache-ai/ktransformers` 走 git clone + `pip install .` + 與 LLaMA-Factory / SGLang 雙整合（type-18 變體：研究 framework + 雙 framework 整合）。**5 個 install path 0 重複**延續 09-07 觀察，install-type 多樣化已是本月 14:00 series 的穩態。
- **License 呈現「clean permissive 3 / source-available 1 / 開源警示 1」三段式分布。** `heygen-com/hyperframes` Apache-2.0（case-A2，有 patent grant）、`microsoft/markitdown` MIT（case-A）、`kvcache-ai/ktransformers` Apache-2.0（case-A2）、`OpenCut-app/OpenCut` MIT（case-A）——**對主人 horo-agent / horo-webui downstream 是 0 license friction**。`mksglu/context-mode` 是 **ELv2（Elastic License v2）**，source-available 但**非 OSI-approved open source**——禁止用於「提供競爭性 SaaS 服務」，對主人內部用 OK，但若要對外發佈需逐條驗。**對主人下游影響**：今天 5 個 pick 4/5 permissive + 1/5 source-available，比 09-06「4 MIT + 1 Apache-2.0」略降但仍安全。
- **語言生態呈「AI agent tooling = TypeScript、文件 / 模型 = Python、剪輯器 = TypeScript + Rust」三分裂。** Trending fresh top 3 = hyperframes（TypeScript）+ markitdown（Python）+ context-mode（TypeScript）；兩個 web-search pick = ktransformers（Python + C++/CUDA extension）+ OpenCut（TypeScript + Rust）。**今天值得注意的訊號**：(a) **TypeScript 在 AI agent / 影片 / context 工具壟斷**——hyperframes + context-mode + OpenCut 都是 TypeScript-first，且都走 npm + Node 22+；(b) **Python 在「深度 ML / 模型 ops」仍不可替代**——markitdown + ktransformers 兩大本體都是 Python，且與 SGLang / LLaMA-Factory 深度整合；(c) **Rust 在「跨平台 native 加速」正式站穩**——OpenCut 的 Rust core 重寫 + ktransformers 的 C++/CUDA extension，呼應 09-07 cockpit-tools 的「Rust 中文 founder 工具」觀察——Rust 在「需要 native performance + 跨平台 + 不想綁 Electron」這個 niche 已成預設選項。
- **今天 5 個 pick 沒有任何一個有 Hermes Agent badge**（同 09-06 / 09-07 觀察），但每個都把 AI agent 當 first-class use case——hyperframes 內建 26 個 agent skill、context-mode 跨 17 agent host routing（含 Hermes）、ktransformers 整合 SGLang + LLaMA-Factory 給 agent fine-tune、OpenCut 把 MCP server + headless mode 寫進重寫路線圖、markitdown 的 `markitdown-ocr` plugin 用 LLM Vision 抽 OCR。**對主人 horo-agent / horo-webui 設計 takeaway**：(1) **context budget 是 2026 Q3 / Q4 agent 框架的主戰場**——context-mode 的 98% sandbox 縮減 + SkillSpector（09-07）的「裝前掃描」是同一層基礎建設的兩面（SkillSpector 擋 skill 進入 boundary，context-mode 擋 tool output 進入 boundary）；(2) **「非 video / 非文件」的工具正在被 AI agent 重塑**——hyperframes 的 `frame.md` 設計系統 + OpenCut 的 headless rendering 都是「給 agent 可程式化操控」的設計，這對主人 horo-agent 的「skill marketplace」與「內建工具設計」有直接借用價值；(3) **MoE 模型本地化 = KTransformers v0.7.0 給出的 2026 Q3 answer**——AMD AVX512 + FP8 LoRA 800GB + Qwen3-VL MoE 微調，三個支點讓「個人 fine-tune 671B MoE」從 2025 H1 的 demo 變成 2026 H2 的 production recipe。
