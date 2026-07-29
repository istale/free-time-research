# GitHub 專案動態
- 檢查時間：2026-07-29
- 檢查對象：pascalorg/editor、jenkinsci/jenkins、moeru-ai/airi、KlaatAI/klaatcode、Nanako0129/pilotfish
- 來源：Trending Daily Top 3 + 自由探索 Web Search（Google CAPTCHA 後以 GitHub Search API 驗證）

## Repo 摘要與 3W1H

### pascalorg/editor
- **Repo 摘要：** Pascal Editor 是用 React Three Fiber 與 WebGPU 打造的瀏覽器 3D 建築編輯器，將 scene schema、viewer、editor UI 與內建 nodes 拆成可組合的 npm packages。它以扁平 node dictionary、Zustand scene state、registry 與 dirty-node systems 管理牆、樓板、屋頂、房間及物件，適合要嵌入 3D 建築檢視／編輯能力的 React 團隊，而不只是一個封閉的線上 app。Repo 為 TypeScript、MIT，18,950 stars，最後更新 2026-07-29。
- **3W1H：**
  - **What：** WebGPU 3D building editor、viewer runtime 與建築節點套件組成的 Turborepo monorepo。
  - **Why：** 傳統 BIM／建築編輯器通常笨重且難嵌入 web app；Pascal 把 scene state、rendering 與 direct-manipulation UI 拆成 npm 套件，讓開發者只取 viewer 或加上完整 editor。
  - **Who：** React／Three.js 開發者、PropTech 與建築視覺化團隊，以及需要在瀏覽器內建立或展示 3D 建築資料的產品。
  - **How：** 安裝 core、viewer、editor、nodes 四個套件，先 `loadPlugin(builtinPlugin)`，再於 React app 掛載 Viewer／Editor；也可 clone monorepo 開發 Next.js editor app。
- **安裝方式：** **npm/pnpm/yarn/npx：** `npm install @pascal-app/core @pascal-app/viewer @pascal-app/editor @pascal-app/nodes`；程式中呼叫 `await loadPlugin(builtinPlugin)` 後掛載 viewer。README 未列 pip/uv/poetry 安裝方式。
- **Repo metadata：** Description `Create and share 3D architectural projects.`；Stars 18,950；Language TypeScript；Topics 未列出；License MIT；Updated at 2026-07-29。
- **近期 release：** **2026-06-10**；`v0.9.1`；**v0.9.1 — Preset System, Rooms & Templates, Building Manipulation**。

### jenkinsci/jenkins
- **Repo 摘要：** Jenkins 是成熟的 Java 開源自動化伺服器，透過 2,000+ plugins 串起 build、test、static analysis 與 deployment。它仍適合需要高度客製、內網部署、跨年代工具鏈或既有 Jenkinsfile 資產的組織；代價是安裝後仍要維護 controller、agents、plugins 與升級相容性。Repo 為 Java、MIT，26,134 stars，最後更新 2026-07-29，且 7 月 28 日剛發布 weekly 2.575。
- **3W1H：**
  - **What：** 可自架、plugin-driven 的 CI/CD 與通用 workflow automation server。
  - **Why：** 把反覆的 build、test、analysis、deployment 自動化，並以龐大 plugin 生態整合企業內各式版本控制、artifact、雲端與 legacy 系統；近期受關注是 weekly release 仍維持高頻交付。
  - **Who：** DevOps／平台工程團隊、需要 on-premises CI/CD 的企業，以及已有大量 Jenkins pipelines 與 plugins 的維運者。
  - **How：** 從官網選 WAR、Docker image、Linux/Windows native package 或 installer，建立 controller 後安裝 plugins、定義 Pipeline／Jenkinsfile，再接 build agents 執行工作。
- **安裝方式：** 未找到明確 pip/npm 安裝方式。README 的正式發行管道為 **WAR、Docker image、Linux/Windows native packages 與 installers**，並分 Weekly 與 LTS 兩條 release line；具體平台指令由 Jenkins Downloads 文件提供。
- **Repo metadata：** Description `Jenkins automation server`；Stars 26,134；Language Java；Topics `cicd`, `continuous-delivery`, `continuous-deployment`, `continuous-integration`, `devops`, `groovy`, `hacktoberfest`, `java`, `jenkins`, `pipelines-as-code`；License MIT；Updated at 2026-07-29。
- **近期 release：** **2026-07-28**；`jenkins-2.575`；**2.575**。

### moeru-ai/airi
- **Repo 摘要：** AIRI 是自架、使用者擁有的 AI companion／AI VTuber「靈魂容器」，以 WebGPU、WebAudio、WebAssembly 等 web 技術跨 browser、desktop 與 mobile，並提供即時語音、VRM／Live2D 角色、Discord／Telegram 互動，以及 Minecraft、Factorio 等遊戲能力。它想把只能在直播時出現的 Neuro-sama 類數位生命變成可本機持有、隨時互動與擴充的個人角色平台。Repo 為 TypeScript、MIT，44,938 stars，最後更新 2026-07-29。
- **3W1H：**
  - **What：** 跨平台 AI companion／AI VTuber app 與可擴充數位角色 runtime。
  - **Why：** 現有角色聊天產品多停在文字／語音或由平台控制；AIRI 把角色身體、即時聲音、遊戲操作、記憶與本地推論路線放在同一個開源、自有部署的系統中。
  - **Who：** AI companion 使用者、VTuber／Live2D／VRM 創作者，以及研究 speech、computer vision、RL、WebGPU 與 game agents 的開發者。
  - **How：** 一般使用者下載 Windows／macOS／Linux／Android release 或直接開 web app；開發者用 pnpm 安裝 monorepo dependencies，`pnpm dev` 啟動 web stage。
- **安裝方式：** 未找到明確 pip/npm 套件安裝方式。**桌面 app：** macOS `brew install --cask airi`；Windows `winget install MoeruAI.AIRI`，也可由 GitHub release 下載 `.dmg`／`.exe`／`.AppImage`／`.apk`。**pnpm 開發：** `pnpm i && pnpm dev`。
- **Repo metadata：** Description `Self hosted, you-owned Grok Companion...`；Stars 44,938；Language TypeScript；Topics `ai-companion`, `ai-vtuber`, `airi`, `digital-life`, `grok-companion`, `live2d`, `neuro-sama`, `neurosama`, `openclaw`, `vrm`, `vtuber`；License MIT；Updated at 2026-07-29。
- **近期 release：** **2026-07-18**；`v0.11.3`；**v0.11.3**。

### KlaatAI/klaatcode
- **Repo 摘要：** Klaat Code 是 terminal-native AI coding agent，具備讀寫程式碼、執行指令、驗證、plan mode、subagents、MCP、code graph 與 cost guard；其差異化核心是 hosted Klaatu router 會逐 request 在 nano／fast／code／reason／heavy tier 間選模型。開源 CLI 是連到 hosted intelligence 的 thin client，因此適合想降低 coding-agent 成本、接受雲端 routing service，並需要可視化預算與 runaway protection 的使用者。Repo 為 TypeScript，GitHub API license 顯示 Other/NOASSERTION（README 標示 Apache-2.0），349 stars，最後更新 2026-07-29。
- **3W1H：**
  - **What：** 一款類 Claude Code／Codex CLI／Aider 的終端 AI coding agent，加上逐訊息模型路由、code knowledge graph 與成本控制。
  - **Why：** 單一 frontier model 處理所有讀檔、修改與推理會浪費成本；Klaat Code 將不同難度導向不同 tier，並用 burn-rate、phase budget、hard cap 與 doom-loop breaker 降低失控風險。專案在 2026-07-29 發布 `V2.3.2`，是今天最鮮的探索候選之一。
  - **Who：** 使用 terminal coding agents 的個人開發者、想在 CI 內限制每次 agent 成本的團隊，以及需要 Claude／GPT／Gemini／DeepSeek 多模型切換的人。
  - **How：** 全域安裝 `klaatcode`，browser login 後在專案目錄啟動；可用 `klaatcode run "Fix all TS errors" --max-cost 0.50` 做 headless／CI 工作，或加入 OpenAI-compatible endpoint 自帶模型。
- **安裝方式：** **npm/pnpm/yarn/npx：** `npm install -g klaatcode`（安裝後不需 Node/Bun runtime）；另有 `brew install KlaatAI/klaatcode/klaatcode` 與官方 macOS/Linux one-line installer。最短上手：`klaatcode login && klaatcode`。
- **Repo metadata：** Description `Open-source AI coding agent for the terminal...`；Stars 349；Language TypeScript；Topics `agentic-ai`, `ai-agents`, `ai-coding`, `cli`, `code-generation`, `coding-agent`, `deepseek`, `llm`, `open-source`, `pair-programming`, `terminal` 等；License API `NOASSERTION`（README 稱 Apache-2.0）；Updated at 2026-07-29。
- **近期 release：** **2026-07-29**；`V2.3.2`；**KlaatCode V2.3.2**。

### Nanako0129/pilotfish
- **Repo 摘要：** pilotfish 是 Claude Code 的純設定型多模型 orchestration layer：主 session 用 Opus family 負責規劃與判斷，Sonnet／Haiku roles 做高量工作，再由新的 Opus context 進行 plan 與 outcome verification。它只有三類全域設定檔、沒有 runtime code，重點不是多開 agent，而是用 capability boundaries、fresh-context verifier、approval gate 與 calibrated verdict 保住品質。適合願意仔細審核全域 `~/.claude/` 改動、並想控制高階模型 quota 的 Claude Code 重度使用者。Repo 為 Python、MIT，546 stars，最後更新 2026-07-29。
- **3W1H：**
  - **What：** Claude Code 的八角色、多模型 orchestration policy／installer templates，而非獨立 app 或 Python library。
  - **Why：** 大量探索、機械修改與測試不必都消耗最昂貴模型；同時自我審查容易失真，因此把 plan challenge 與完成結果交給 fresh-context verifier。`v1.3.5` 在 7 月 29 日把 outcome verification 明確校準為 `CONFIRMED`、`REFUTED`、`INCONCLUSIVE`。
  - **Who：** Claude Code 2.1.219+ 使用者、訂閱 quota 敏感的個人／團隊，以及研究「強模型規劃、便宜模型執行、獨立模型驗證」agent topology 的 builder。
  - **How：** clone 固定 release tag，從該 checkout 啟動 Claude Code，讓它讀本機 `install/AGENT-INSTALL.md`、先展示 merge plan，取得批准後才寫入 `~/.claude/settings.json`、`~/.claude/agents/` 與 `~/.claude/CLAUDE.md`。
- **安裝方式：** 未找到明確 pip/npm 安裝方式。**clone／agent-assisted setup：** `git clone --branch v1.3.5 --depth 1 https://github.com/Nanako0129/pilotfish.git && cd pilotfish && claude`，再要求 Claude 讀 `install/AGENT-INSTALL.md` 並先給完整變更計畫。這個流程會修改全域 Claude 設定，應先審模板、固定 tag／SHA 並保留 approval gate。
- **Repo metadata：** Description `Multi-model orchestration layer for Claude Code...`；Stars 546；Language Python；Topics `ai-agents`, `anthropic`, `claude`, `claude-code`, `multi-agent`, `orchestration`；License MIT；Updated at 2026-07-29。
- **近期 release：** **2026-07-29**；`v1.3.5`；**v1.3.5 — calibrated outcome verification**。

## 重點觀察

- release 新鮮度分成三層：Klaat Code `V2.3.2` 與 pilotfish `v1.3.5` 都在 **2026-07-29** 當天發布，Jenkins `2.575` 是前一天；AIRI `v0.11.3` 已 11 天，Pascal Editor `v0.9.1` 則停在 6 月 10 日。Web 探索因此特意選了兩個「今天有 release」的 agent 工具。
- 上手門檻以 Klaat Code 最低：`npm install -g` 後登入即可；Pascal 也有正式 npm packages，但需要 React 整合。AIRI 給一般使用者原生安裝包、開發者則進 pnpm monorepo；Jenkins 要面對 server／plugin 維運；pilotfish 沒有套件化，且因會改全域 Claude 設定，雖然程式量少，信任與審核門檻反而最高。
- 語言生態集中在 TypeScript（Pascal、AIRI、Klaat Code），分別服務 3D editor、跨平台 AI companion、terminal agent；Java（Jenkins）代表成熟企業自動化，Python（pilotfish）主要承載設定與模板而不是 Python runtime。
- 套件化方面，5 個 repo 中只有 Pascal 與 Klaat Code 提供明確 npm 安裝；沒有 repo 提供正式 pip/uv/poetry library。AIRI 有 pnpm 開發流程但產品分發以 desktop/mobile binaries 為主，Jenkins 走 WAR/Docker/native package，pilotfish 則走 pinned clone + agent-assisted install。
- Web 探索的兩個 repo 正好呈現 agent 成本治理的兩條路：Klaat Code 把 routing、cost guard、code graph 放進 hosted service + CLI；pilotfish 則只用 Claude Code 原生角色設定做「規劃／執行／驗證」分層。前者上手快但依賴服務，後者透明可審但侵入全域設定，選擇點不是功能多寡，而是信任邊界。
