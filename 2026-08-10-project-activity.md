# GitHub 專案動態
- 檢查時間：2026-08-10
- 檢查對象：PrimeIntellect-ai/prime-agent、google-deepmind/weathernext、addyosmani/agent-skills、QwenLM/Qwen-MM-Plugins、eighttrigrams/us-vs-them（共 5 個 repo）
- 來源：GitHub Trending daily（前 3）+ HN Firebase top/newstories（後 2）

## Repo 摘要與 3W1H

### `PrimeIntellect-ai/prime-agent`
- **Repo 摘要：** Prime Agent 是一個開源、self-improving 的 RLM（Recursive Language Model）程式與研究代理，目標是「長跑型任務」——把 context 視為變數、把工具視為可遞迴呼叫，運行在持久 IPython 控制環境中，並且透過 `/refine` 對自己的補充提示、記憶、技能與子代理規格做小幅、證據驅動的更新。主要適合做研究評測、跨 session 的長期程式任務，以及想用本地 daemon 持續推進背景任務的開發者。
- **3W1H：**
  - **What：** 一個以 RLM 程式模型 + Continual Harness（自我可改進的補充狀態）為核心的開源 CLI/TUI 代理；附帶 daemon、worker、kernel、persistent IPython 等後台服務。
  - **Why：** 多數 coding agent 是「單 session、無記憶、結束即忘」；Prime Agent 把 skills / subagents / context 做成可持續精煉的 durable state，並且把「bounded autonomous mode」明文化（受 turn / token / time budget 與自訂 quality gates 約束）。
  - **Who：** 跑 AI 評測的研究員、需要長跑型自動化任務的研究/工程團隊、對「agent 自我改進」實作有興趣的開發者。
  - **How：** 一行 `curl -fsSL https://app.primeintellect.ai/prime-agent/install.sh | sh` 安裝 `prime-agent` CLI，於工作目錄執行 `prime-agent`，首次啟動 `/login` 選 API provider；常用子命令 `agents` / `attach` / `--resume` / `status` / `doctor --fix` / `update --force` / `shutdown --force`；長跑模式 `/autonomous` 配 `rlm_heartbeat`、`/goal`、`/heartbeat`。
- **安裝方式：**
  - **未列（單檔 shell installer）：** `curl -fsSL https://app.primeintellect.ai/prime-agent/install.sh | sh`（會驗 SHA-256 並安裝 `prime-agent` 指令）。
  - **未找到 pip / npm 安裝方式**：本 repo 沒有 PyPI / npm package，安裝一律走 install.sh；從 source 安裝見 `packages/coding-agent/docs/development.md`。
- **近期 release：** `v0.7.1`（2026-08-07，stable）：修掉 bundled `websearch` skill 描述、讓 `retry_worker` 不會把已停止 session worker 的 stop marker 當 recovery 取消。
- **License：** MIT（API + LICENSE 檔一致）。Copyright 2025 Mario Zechner / 2026 Prime Intellect。

### `google-deepmind/weathernext`
- **Repo 摘要：** WeatherNext 是 Google DeepMind / Google Research 的全球中程大氣與氣旋預報模型開源 repo，主軸是 WeatherNext 2（WN2）與 WeatherNext Cyclones（操作上線版本 FNV3 / GDMI），並同時保留 GraphCast 與 GenCast 等舊世代模型；repo 同時發佈模型權重與 Colab demo，定位為研究/操作單位的天氣預報模型參考實作。
- **3W1H：**
  - **What：** 提供 WeatherNext 2（含 100m 風場）、WeatherNext Cyclones、Cyclones Mini（1° 輕量）三類 pretrained model 的 inference / 評測程式碼與權重，底層走 FGN 架構與 JAX。
  - **Why：** 0.25°（約 30 km）解析度的 AI 天氣預報已被 Google 在內部操作化使用；2025 大西洋颶風季中實際跑出 FNV3 / GDMI，並登上 *Nature* 論文；對想做 AI-based operational forecast、ensemble 或 cyclone tracking 的團隊是公開可重現的 reference。
  - **Who：** 氣象 / 大氣科學研究員、能源與防災單位 ML 工程師、做 ensemble / probabilistic forecasting 的研究團隊。
  - **How：** 推薦路徑是跑官方 Colab notebook（`docs/weathernext2/wn2_demo.ipynb`，預設用 WeatherNext Cyclones Mini + `v5e-1` runtime）；要正式跑 WN2 全模型需 TPU（或 H100 GPU 並切換 attention 實作），mini 模型可在 P100 推論；權重從 `gs://dm_graphcast/` 下載 `WeatherNext2_<2025_model{1..4}.npz` 之類檔案。
- **安裝方式：**
  - **pip/uv：** `pip install git+https://github.com/google-deepmind/weathernext.git@v0.3.0`（README 明確建議 pin 到 release tag，避免 main 破壞性更新）。
  - 額外步驟：下載預訓練權重（`gs://dm_graphcast/` 的 GCS bucket）；TPU 為首選加速器。
- **近期 release：** `v0.3.0`（2026-08-06，stable，「Version 0.3.0」）：加入 WN2 支援。Tags 序列：`v0.3.0` → `v0.2` → `v0.1.1` → `v0.1`。
- **License：** Apache-2.0（API + LICENSE 檔一致）。

### `addyosmani/agent-skills`
- **Repo 摘要：** Addy Osmani 維護的「Production-grade engineering skills for AI coding agents」——把資深工程師常用的工作流（定義 / 規劃 / 建置 / 驗證 / 審 / 上線）打包成 24 個 skill 與 8 個 slash command（`/spec`、`/plan`、`/build`、`/test`、`/review`、`/webperf`、`/code-simplify`、`/ship`），並透過 `vercel-labs/skills` CLI 一鍵安裝到 70+ 個 AI coding agent（Claude Code、Cursor、Codex、Copilot、Cline 等）。適合「想直接複用資深工程師 SOP 在 AI agent 上」的個人/小團隊。
- **3W1H：**
  - **What：** 24 個 skill + 8 個 slash command 的集合，並非單一 CLI 工具——安裝後在你的 agent runtime 內多了一組「資深工程師 SOP」可隨時觸發。
  - **Why：** 與其每次 prompt 都要重新寫「先 spec 後 plan 再 build」之類流程，不如讓 agent 把這些 SOP 變成 reusable skill；`/build auto` 把「approve the plan once, then run autonomously」標準化，但每個 task 仍 test-driven 並獨立 commit、失敗或 risky 步驟會 pause。
  - **Who：** 用 Claude Code / Cursor / Codex / Copilot / Cline 寫 code 的工程師個人與小團隊；尤其想導入「agent-skills as code review」流程的 lead / staff engineer。
  - **How：** 一行 `npx skills add addyosmani/agent-skills` 裝全部 24 個 skill，或 `npx skills add addyosmani/agent-skills --skill <name>` 挑裝；Claude Code 用戶走 `/plugin marketplace add addyosmani/agent-skills` + `/plugin install agent-skills@addy-agent-skills`。
- **安裝方式：**
  - **npm/pnpm/yarn/npx：** `npx skills add addyosmani/agent-skills`（透過 vercel-labs/skills CLI，安裝到 70+ agent runtime）。
  - **Clone + marketplace（Claude Code）：** `/plugin marketplace add addyosmani/agent-skills` + `/plugin install agent-skills@addy-agent-skills`。
  - **未找到 pip 安裝方式**：本 repo 不發 PyPI package。
- **近期 release：** `0.6.6`（2026-08-04，stable，「0.6.6」）：fix release，修回 Claude Code 上四個 persona 沒被載入的問題（#449，`@ingnicolaboccato-lab` 回報），並補上 explicit `agents` array。
- **License：** MIT（API + LICENSE 檔一致）。Copyright 2025 Addy Osmani。

### `QwenLM/Qwen-MM-Plugins`
- **Repo 摘要：** Qwen 團隊發的「Make any agent harness multimodal-native」插件集——把 vision / video / doc / 3D / OCR / 語音 / 影片編輯 / Blender / FreeCAD / 教學影片生成等能力拆成可逐項安裝的 plugin，每個 plugin 都是「1 個 skill（讓模型知道工具存在）+ 1 個 MCP server（工具本體，由 `uvx` 啟動）」，並透過單一 `install.sh` 同時鋪進 Claude Code、Codex、Qoder、OpenClaw、Qwen Code、Gemini CLI 等多個 agent runtime。適合 Qwen 模型用戶想把 agent 升級為多模態、又不想自己寫 MCP server 的場景。
- **3W1H：**
  - **What：** 一個 multi-capability 插件集，目前 6 個 capability（core / video-memory / video-edit / blender / freecad / edu-agent），安裝後 agent 多了圖像 / 影片 / 文件 / 3D / OCR / 影片生成 / Blender / FreeCAD 工具。
  - **Why：** 開源 agent runtime 預設只看得懂文字；這個 repo 把 Qwen3.8-Max 系列的多模態能力模組化成可逐步啟用的 MCP server，並透過 shared config（`~/.qwen-mm-plugins/config`）讓 GUI 與 terminal harness 都能讀到同一組 API key。
  - **Who：** Qwen 模型 + 任一款主流 coding agent（Claude Code / Codex / Qoder / OpenClaw / Qwen Code / Gemini CLI）的使用者；做 3D / CAD / 影片編輯的工程師；做教育內容（中文解說影片）的開發者。
  - **How：** 一行 `curl -fsSL https://raw.githubusercontent.com/QwenLM/Qwen-MM-Plugins/main/install.sh | bash` 跑 guided installer；或逐個 harness 手動：`claude plugin marketplace add https://github.com/QwenLM/Qwen-MM-Plugins.git` + `claude plugin install qwen-mm-plugins-<cap>@qwen-mm-plugins`；API key 設 `DASHSCOPE_API_KEY`（視覺/OCR/生成）與 `SERPER_API_KEY`（web_search）。
- **安裝方式：**
  - **未列（split-distribution — Type-7 multi-harness plugin）：**
    - Guided installer：`curl -fsSL https://raw.githubusercontent.com/QwenLM/Qwen-MM-Plugins/main/install.sh | bash`（支援 Claude Code · Codex · Qoder · OpenClaw · Qwen Code · Gemini CLI）。
    - Per-harness（Claude Code 範例）：`claude plugin marketplace add https://github.com/QwenLM/Qwen-MM-Plugins.git` + `claude plugin install qwen-mm-plugins-<cap>@qwen-mm-plugins`（`<cap>` 換 `core` / `video-memory` / `video-edit` / `blender` / `freecad`）。
    - Per-harness（Codex / Qoder / OpenClaw / Qwen Code）各自有對應的 `plugin` / `extensions install` 命令，見 README §「By hand (per-harness)」。
  - 系統依賴（installer 的 `verify` 會 self-test）：`ffmpeg`（必要），選配 `libreoffice` / `blender` / `texlive` / `chromium`（給 `visualize`）。
  - **未找到 npm / PyPI 安裝方式**：本 repo 不發 npm package，也不發 PyPI package；MCP server 由 `uvx` 在首次呼叫時自動拉。
- **近期 release：** 未找到 GitHub release（`GET /releases/latest` 回 404；repo 沒有任何 GitHub release tag；pushed_at 為 2026-08-09，主要以 commit + install.sh 推進）。
- **License：** Apache-2.0（API + LICENSE 檔一致）。Blender 與 FreeCAD capability 內含第三方 MIT 程式碼，見 `src/capabilities/blender/NOTICE.md` 與 `src/capabilities/freecad/NOTICE.md`。

### `eighttrigrams/us-vs-them`
- **Repo 摘要：** 一個用 diff 從 git 版次紀錄推出「每行/每段是誰寫的」的 CLI / library，輸出 0.00~1.00 的 provenance 區段表——針對 agentic coding 時代「我寫的 vs 它寫的」分界愈來愈模糊的痛點。底層用 babashka + Clojure 寫成單檔可攜腳本，適合對「程式碼/文件中的人類 vs agent 邊界」有研究/標註需求的研究者與工程師。
- **3W1H：**
  - **What：** 一個 CLI 工具（也是 Clojure library），對給定檔案與 `--ours <email>`（人類）/ `--theirs <email>`（agent），回傳一組「island / sea」型的 line-range 評分；不需要在 text 裡埋 markup。
  - **Why：** vibecoded codebase 中想保護自己寫的段落不被下次 session 的 agent 蓋掉？想看 README 哪些段落已被人類改寫、哪些仍是 agent 生成？這套工具用 git history 的 authorship + diff 演算法回答這個問題；不是「per-line author」，而是「coherent piece of text」級別（join / split / dilution 都會被考量）。
  - **Who：** 用 agentic editing 寫程式 / 寫 markdown 的人、想對 agent 生成內容做人類所有權標註的研究者、HCI / provenance 學術領域。
  - **How：** 在 git repo 內 `us-vs-them --ours <your-email> <file>`；輸出像 `1-3 0.00` / `4 1.00` / `5-7 0.00` / `8-20 0.46` / `21-164 0.00`——1.0 全人類、0.46 原人改 agent 修、0.00 全 agent。
- **安裝方式：**
  - **未列（clone + native build via babashka/bbin）：** 需先裝 [babashka/bbin](https://github.com/babashka/bbin)，然後 `git clone https://github.com/eighttrigrams/us-vs-them` + `make install`（會 build 出一個自含所有 namespace 的 `target/us-vs-them` 單檔腳本，再以 `bbin install target/us-vs-them --as us-vs-them` 鋪進 PATH）。
  - **未找到 pip / npm 安裝方式**：本 repo 不發 PyPI / Clojars / npm package；測試 `make test`（跑 Clojure test ns）。
- **近期 release：** 未找到 GitHub release（`GET /releases/latest` 回 404；repo 沒有任何 GitHub release tag；pushed_at 為 2026-08-09）。
- **License：** License **None**（API `license: null`；LICENSE / LICENSE.md 兩條 branch 都 404——repo 內未放 LICENSE 檔；引用 / 二次發佈前需 owner 確認授權）。

## 重點觀察
- **Release 新鮮度落差大：** 5 個 repo 裡只有 3 個有 GH release（prime-agent 08-07、weathernext 08-06、agent-skills 08-04），另 2 個（Qwen-MM-Plugins、us-vs-them）完全沒有 release tag，只靠 commit 推進；對於「以 GitHub Releases 為信號源」的讀者來說，今天的 5 個有 40% 走 commit-only 路徑——比 08-09 的 20%（1/5）更高，呼應之前 08-04 觀察到的「zero-GH-release + active repo 是穩定 pattern」。
- **安裝生態分裂：** 5 個 repo 跨了 5 種 install type——`prime-agent` 是 `curl | sh` 單檔 installer（Type-4）、`weathernext` 是 `pip install git+@v0.3.0`（Type-1 pip）、`agent-skills` 是 `npx skills add`（Type-2 npm）加上 marketplace 多路徑、`Qwen-MM-Plugins` 是 `install.sh` + 6 個 agent runtime 的 per-harness plugin 命令（Type-7 multi-harness plugin）、`us-vs-them` 是 `bbin install` + babashka build（Type-4 clone+build）。今天完全沒有 Type-3 (clone+pnpm dev) 或 Type-6 (desktop-client)——挑選到的清一色是「安裝摩擦最低」的型別，可能反映 Trending 與 HN 對「馬上能裝馬上能跑」的偏好。
- **語言生態：** 5 個裡面 TypeScript × 1（prime-agent）、Python × 2（weathernext + Qwen-MM-Plugins 的 MCP server 用 uvx/Python）、JavaScript × 1（agent-skills）、Clojure × 1（us-vs-them）；這跟 08-09 「TS/JS 3-4/5 支配」觀察偏離，今天 Python 與小眾語言（Clojure）佔比反而較高——可能因為 Tier-B fallback 走 HN Firebase 而非純 Trending，挑出更多偏 research / niche 的 repo。
- **License 乾淨度：** 5/5 API license 欄位都是常見開源許可（MIT × 3、Apache-2.0 × 2），沒有 BUSL / AGPL / 客製化條款；只有 `us-vs-them` 是 LICENSE 檔缺失的「無文件」狀態（API null + 兩個 branch 都 404 → License None）。其餘 4 個都是 case A 或 case B（API 欄位與 LICENSE 檔 header 一致），沒有 08-07 case E (AGPL) 或 08-06 case D (BUSL) 的出現。
- **跟昨日（08-09）有 cross-day overlap：** `PrimeIntellect-ai/prime-agent`（08-09 Tier-A #1，今天再上 Tier-A #1）與 `addyosmani/agent-skills`（08-08 #2 + 08-09 #2，今天再上 Tier-A #3）兩個 repo 連續 2-3 天進 Trending Top 3。結構性 source property：當 Trending 沒有新血時，「昨日 Trending」會自然遞補；對 air-gapped / 下游精簡專案是中性訊號（這些 repo 已是 MEMORY pinned topic），但對想看「全新專案」的讀者要留意——今天的兩個 Tier-B（Qwen-MM-Plugins + us-vs-them）才是真的 fresh face。MEMORY cross-reference：3/5 picks 直接對應到主人 pinned topic——`prime-agent` 的「bounded autonomous mode + autonomous quality gates」↔ 主人 `Stage gate ≠ 真驗證` 規則；`agent-skills` 的 `/build auto`「approve plan once + per-task test+commit+pause-on-failure」↔ 主人「spec-driven N-stage + 每階段 commit + 結束前 self-verify」workflow；`us-vs-them` 的「line-level provenance」↔ 昨日 08-08 PM SkillTrace 文章的同主題子題。
- **MEMORY cross-reference：** `prime-agent` 的 RLM + Continual Harness ↔ 主人 SOUL.md / `skill_view` / `todo` panel 這套「runtime-visible control surface」（forward bridge），而 README 的 `bounded autonomous mode`「A passed gate checks only what that gate verifies; reaching a limit does not imply task success」可 reverse-borrow 為主人「Stage gate ≠ 真驗證」的補強引文；`us-vs-them` 的 island/sea 比喻 ↔ 主人 SKILL.md 編寫時區分「主人自己寫的段落 vs LLM 寫的段落」的邊界判定；`Qwen-MM-Plugins` 的「shared config（`~/.qwen-mm-plugins/config`）讓 GUI 與 terminal harness 都能讀到同一組 API key」↔ 主人 `horo-webui` / `horo-agent` air-gapped downstream「WebUI 從 upstream state.db 讀」的解耦哲學。
