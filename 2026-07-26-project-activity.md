# GitHub 專案動態
- 檢查時間：2026-07-26
- 檢查對象：block/buzz、alibaba/open-code-review、citrolabs/ego-lite、xai-org/grok-build、Blaizzy/nativ

## Repo 摘要與 3W1H

### block/buzz
- **Repo 摘要：** Buzz 是可自行託管的人類與 AI agent 協作工作區，核心以自有 relay 與 Nostr 簽名事件日誌承載訊息、反應、工作流、審核和 git 事件。它把「人和 agent 在同一個房間協作」與可稽核、可擁有的基礎設施結合，適合重視資料主權的開發團隊與 agent 平台建造者。Repo 目前為 Rust、Apache-2.0，12,224 stars，最後更新 2026-07-26。
- **3W1H：**
  - **What：** 可自架的 agent/human 團隊工作區與 Nostr relay；訊息、工作流和程式碼事件都是簽名事件。
  - **Why：** 解決把 agent 塞進既有聊天工具後缺少權限邊界、身份一致性與 audit trail 的問題；近期因「agent-native collaboration」和主權式 relay 架構受到注意。
  - **Who：** 需要自有協作空間、可驗證 agent 身份，以及人機共同開發流程的團隊。
  - **How：** 依 README 的 Vision、Architecture 與 relay 文件部署服務，讓人與 agent 進入同一 community，以房間、事件、審核和 workflow 協作。
- **安裝方式：** 未找到明確 pip/npm 安裝方式；README 指向自架 relay 與 Rust 專案文件，實際上應 clone repo 後依 `ARCHITECTURE.md`/開發文件建置。**Rust/build**：需 Rust toolchain。
- **Repo metadata：** Language Rust；Topics 未列出；License Apache-2.0；Stars 12,224。
- **近期 release：** **2026-07-25**；`v0.4.26`；**Buzz Desktop v0.4.26**。

### alibaba/open-code-review
- **Repo 摘要：** OpenCodeReview 是 Alibaba 開源的混合式程式碼審查工具，把 deterministic pipeline 與 LLM agent 結合，能做精確到行的評論，並內建 NPE、thread-safety、XSS、SQL injection 等規則。它適合想把 AI code review 放進 CI/CD、又不願完全依賴模型自由判斷的工程團隊。Repo 為 Go、Apache-2.0，13,138 stars，最後更新 2026-07-26。
- **3W1H：**
  - **What：** repository-level context 的 AI code review assistant / harness，支援 OpenAI 與 Anthropic 相容介面。
  - **Why：** 將可重現的靜態/規則檢查和 LLM 的語意審查合併，降低漏掉安全與併發問題、或評論無法定位的痛點；Alibaba 的大規模實戰背景也是關注原因。
  - **Who：** 維護大型 Go/多語言 codebase、需要 PR gate、規則化安全檢查和 AI 輔助審查的團隊。
  - **How：** 依 README 的 release / deployment 文件啟動服務，設定模型 provider 與 repository-level context，再接入 CI 或 PR review 流程。
- **安裝方式：** **npm/pnpm/yarn：** README 明確提供 `@alibaba-group/open-code-review` npm 套件連結；可用 `npm install @alibaba-group/open-code-review`（或相應 pnpm/yarn）。若要開發核心則 clone 後依 Go build 文件建置。
- **Repo metadata：** Language Go；Topics `agent`, `agent-skills`, `code-review`, `code-review-assistant`, `harness`, `repository-level-context`；License Apache-2.0；Stars 13,138。
- **近期 release：** **2026-07-24**；`v1.7.16`；**v1.7.16**。

### citrolabs/ego-lite
- **Repo 摘要：** ego-lite 是讓 AI agent 使用瀏覽器進行 web automation 的輕量瀏覽器，能把使用者已登入的 browser state 分享給 Codex 或 Claude Code，同時讓 agent 在自己的 Spaces 執行任務而不干擾使用者分頁。它適合需要真實登入狀態、平行瀏覽任務與低設定成本的 agent workflow。Repo 為 JavaScript、MIT，3,761 stars，最後更新 2026-07-26。
- **3W1H：**
  - **What：** agent-oriented browser 與平行 web automation runtime，提供 agent skills / Spaces。
  - **Why：** 避免每個自動化任務重新登入、維護脆弱 cookie 流程，也避免 agent 操作直接打斷人的瀏覽；「zero cost, zero config」降低試用門檻。
  - **Who：** 使用 Codex、Claude Code 或其他 agent 做研究、表單、後台操作與瀏覽器測試的人。
  - **How：** macOS 直接下載對應 Apple Silicon/Intel DMG，啟動 ego-lite，讓 agent 透過文件中的 browser skill 在獨立 Space 執行任務；README 另提供 docs 入口。
- **安裝方式：** 未找到 pip/npm 套件安裝主路徑；**macOS binary**：README 提供 Apple Silicon 與 Intel DMG 下載連結（Apple Silicon：`https://cdn.ego.app/channel/github_github_referral/setup/macos/arm64/egolite.dmg`）。
- **Repo metadata：** Language JavaScript；Topics `agent-skills`, `ai-agent`, `browser`, `skills`, `skills-sh`；License MIT；Stars 3,761。
- **近期 release：** **2026-07-17**；`v1.2.5`；**v1.2.5**。

### xai-org/grok-build
- **Repo 摘要：** Grok Build（CLI 名稱 `grok`）是 xAI 的終端機 AI coding agent，以全螢幕 TUI 操作 codebase，能編輯檔案、執行 shell、搜尋 web、管理長任務，並支援互動、headless/CI 與 ACP editor embedding。它是面向開發者的完整 agent harness，而不是只有聊天介面；Repo 為 Rust、Apache-2.0，22,569 stars，最後更新 2026-07-26。
- **3W1H：**
  - **What：** terminal-based AI coding agent、TUI 與 agent runtime，支援 Agent Client Protocol。
  - **Why：** 把理解 repository、改檔、跑命令與長任務管理整合在 CLI，適合從互動開發延伸到自動化與 CI；近期新 repo 卻快速累積大量 stars，使它成為值得觀察的 agent harness。
  - **Who：** terminal-first 開發者、需要 headless coding automation 的 CI 維護者，以及 ACP editor 整合者。
  - **How：** **binary install**：`curl -fsSL https://x.ai/cli/install.sh | bash`（macOS/Linux/Git Bash）；Windows 使用 README 的 PowerShell 指令。也可 clone 後用 Rust toolchain build。
- **安裝方式：** **curl/binary：** `curl -fsSL https://x.ai/cli/install.sh | bash`；未找到 pip/npm 安裝方式。開發者可 clone source，依 Rust build/development 文件建置。
- **Repo metadata：** Language Rust；Topics 未列出；License Apache-2.0；Stars 22,569。
- **近期 release：** **未找到 GitHub release**。README 另指向 x.ai/cli 的 released binary，因此不能把 binary 發佈日期誤寫成 GitHub release 日期。

### Blaizzy/nativ
- **Repo 摘要：** Nativ 是 Apple Silicon 上原生 macOS 的本地 AI 工作區，把聊天、vision、MLX model 管理、效能分析、系統監控，以及 OpenAI/Anthropic 相容的本地 inference server 放在一個 SwiftUI app。它會尋找 Hugging Face cache 中的相容 MLX models，也能下載、切換與移除模型，適合想在 Mac 上私有化執行模型、又不想自行拼裝多個服務的人。Repo 為 Swift、MIT，887 stars，最後更新 2026-07-26。
- **3W1H：**
  - **What：** 原生 macOS local-AI app / MLX model workspace。
  - **Why：** 將本地聊天、vision、模型庫、metrics 與可供既有工具使用的 API server 統一，降低 MLX 本地推理的操作摩擦；Apple Silicon/macOS 26+ 的原生取向是其差異。
  - **Who：** 使用 Apple Silicon Mac、重視資料不出機、需要 MLX 模型管理或本地 API endpoint 的開發者。
  - **How：** 下載/建置 macOS app，讓它讀取 `HF_HUB_CACHE`/`HF_HOME`，在模型庫選擇或下載 MLX model，再以 chat 或 OpenAI/Anthropic-compatible server 接上其他工具。
- **安裝方式：** 未找到明確 pip/npm 安裝方式；README 顯示它是 Swift 5 / SwiftUI macOS app，需求為 **macOS 26+、Apple silicon**，可 clone 後用 Xcode/Swift Package 工具建置。
- **Repo metadata：** Language Swift；Topics 未列出；License MIT；Stars 887。
- **近期 release：** **2026-07-20**；`v0.0.1`；**v0.0.1**。

## 重點觀察

- 今日 Trending Top 3 是 `block/buzz`、`alibaba/open-code-review`、`citrolabs/ego-lite`；三者分別落在人機協作、AI code review、browser automation，顯示 agent 正從「模型能力」往協作介面與可執行工具鏈擴張。
- release 新鮮度不完全等於活躍度：Buzz 於 7/25 發版且 7/26 更新，OpenCodeReview 於 7/24 發版；ego-lite 最新 release 是 7/17 但仍在 7/26 更新。Grok Build 沒有 GitHub release，只有 released binary，需分開看待。
- 安裝門檻分化明顯：OpenCodeReview 有 npm 套件化入口；Grok Build 有一行 installer；ego-lite 與 Nativ 偏 macOS binary/app；Buzz 與 Nativ 則較需要讀部署或 Swift/Rust build 文件。
- 語言生態呈現 Rust（Buzz、Grok Build）承擔 runtime/harness，Go（OpenCodeReview）承擔服務與 CI，JavaScript（ego-lite）承擔 browser skill 層，Swift（Nativ）承擔 Apple 平台整合；五個 repo 沒有 pip 套件，只有 OpenCodeReview 明確提供 npm 套件入口。
- 兩個 web 探索選擇為 `xai-org/grok-build` 與 `Blaizzy/nativ`：前者是 agent 的終端執行面，後者是本地模型的 Mac 執行面，和 Trending 的協作/審查/瀏覽器工具互補。
