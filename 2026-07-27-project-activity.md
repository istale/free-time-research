# GitHub 專案動態
- 檢查時間：2026-07-27
- 檢查對象：permissionlesstech/bitchat、citrolabs/ego-lite、block/buzz、vercel-labs/scriptc、8NobleTruths/sabba
- 來源：Trending Daily Top 3 + 自由探索 Web Search（HN）

## Repo 摘要與 3W1H

### permissionlesstech/bitchat
- **Repo 摘要：** BitChat 是雙傳輸的去中心化即時通訊 app，離線用 Bluetooth LE mesh，線上用 Nostr relay 連到 290+ 全球節點，所有頻道／私訊都走 Noise Protocol 或自家 XChaCha20-Poly1305 envelope 端對端加密。它是個無帳號、無電話號碼、無中心伺服器的「side-groupchat」，適合災害應變、示威現場，或任何不想被中央基礎設施讀訊息的場景。Repo 為 Swift、Unlicense（public domain），30,827 stars，最後更新 2026-07-27。
- **3W1H：**
  - **What：** iOS / macOS universal app，雙傳輸（Bluetooth mesh + Nostr）p2p 訊息工具。
  - **Why：** 在沒有網路／SIM／電話號碼的場景下仍可通訊，identity 完全在裝置端，避免被中央伺服器審查或鏡像；近期因「protest 現場、anti-censorship、nostr 復興」再度被推到 Trending Top 1。
  - **Who：** 重視隱私與言論自由的工程師、災防／記者／活動現場參與者，以及想把 nostr 當 transport、把 E2E 訊息當應用的開發者。
  - **How：** App Store 直接下載，或 clone 後用 `open bitchat.xcodeproj`、或 `brew install just && just check && just run`；developer 想 build 可照 README 的 `xcodebuild` / `swift test` 指令。
- **安裝方式：** 未找到明確 pip/npm 安裝方式；**macOS/iOS app：** App Store（`bitchat-mesh`）或從 latest release 下載 `.ipa`／`.dmg`。**build：** 需 Xcode + 自己的 Apple Developer Team ID，clone 後 `cp Configs/Local.xcconfig.example Configs/Local.xcconfig` 並改 team ID，再用 `just` 或 `xcodebuild` build；或 `swift test` 跑 SwiftPM suite。
- **Repo metadata：** Language Swift；Topics `bluetooth`, `bluetooth-le`, `decentralized`, `e2e-encryption`, `ios`, `macos`, `mesh-network`, `messaging`, `nostr`, `swift`；License Unlicense；Stars 30,827；Forks 4,821。
- **近期 release：** **2026-07-08**；`v1.7.0`；**v1.7.0**。

### citrolabs/ego-lite
- **Repo 摘要：** ego-lite 是 macOS 原生瀏覽器，把「人與 AI agent 在同一個瀏覽器平行協作」當一級設計目標，agent 跑在獨立 Spaces、透過 `ego-browser` skill 直接呼叫 in-page JavaScript 工具（snapshot、fill、click、wait、navigate、capture），可在繼承使用者真實登入狀態下完成研究、表單、後台操作。它把 web automation 從「外掛 CLI 驅動外部瀏覽器」變成「一個瀏覽器 + agent skills 層」，比 Browser-Use / agent-browser 在 token 與時間上更省。Repo 為 JavaScript、MIT，4,929 stars，最後更新 2026-07-27。
- **3W1H：**
  - **What：** agent-oriented macOS 瀏覽器與平行 web automation runtime，含 `ego-browser` skill 與 `npx skills add` 入口。
  - **Why：** 解決 agent 重登入、與使用者搶分頁、token 浪費在 CLI 來回等問題；kernel-level snapshot 在 deep iframe 等棘手場景品質較高。
  - **Who：** 使用 Claude Code、Codex、Cursor、OpenCode 做研究、表單填寫、後台操作、自動化測試的人。
  - **How：** 下載 Apple Silicon 或 Intel `.dmg` 安裝後開啟，或 `npx skills add citrolabs/ego-lite` 只裝 skill；agent CLI 內打 `/ego-browser <任務>` 即可。
- **安裝方式：** **macOS binary：** README 提供 Apple Silicon 與 Intel DMG 下載連結（Apple Silicon：`https://cdn.ego.app/channel/github_github_referral/setup/macos/arm64/egolite.dmg`）。**npm/npx：** `npx skills add citrolabs/ego-lite`（裝 ego-browser skill）。
- **Repo metadata：** Language JavaScript；Topics `agent-skills`, `ai-agent`, `browser`, `skills`, `skills-sh`；License MIT；Stars 4,929；Forks 236。
- **近期 release：** **2026-07-17**；`v1.2.5`；**v1.2.5**。

### block/buzz
- **Repo 摘要：** Buzz 是 Block 開源、可自架的人機協作工作區，把訊息、reaction、workflow、審核、git 事件全部當 Nostr 簽名事件寫進同一個 relay 與 audit log；人與 agent 在同一個 community 裡，用同一把 key 簽事件、享有同一個搜尋索引。Buzz 桌面端用 Tauri + React，後端是 Rust workspace；agent 透過 `buzz-cli`（JSON in/out）或 ACP harness（Goose、Codex、Claude Code）接入。Repo 為 Rust、Apache-2.0，13,645 stars，最後更新 2026-07-27。
- **3W1H：**
  - **What：** self-hostable 的人 + agent 團隊工作區，本質是 Nostr relay + 多種 client（desktop、CLI、ACP）。
  - **Why：** 把 chat、forge、bots、CI dashboard、release tool 收成「一個社區、一個身份模型、一個事件 log」，讓 agent 跟人一樣是社群成員而非幽靈 cron。
  - **Who：** 想自己擁有 relay 與 audit trail 的開發團隊、做 agent platform 的 builder、以及 Block 內部需要一個 agent-native workspace 的工程團隊。
  - **How：** `git clone` 後用 Hermit 或自備 Rust 1.88+ / Node 24+ / pnpm 10+ 跑 `just setup && just build && just dev`；正式環境用 `deploy/compose/` 的 Postgres + Redis + MinIO 組合。
- **安裝方式：** 未找到明確 pip/npm 安裝方式；**desktop app：** latest release 提供 macOS `.dmg`、Linux `.AppImage`/`.deb`、Windows `.exe`，預設連 `ws://localhost:3000`，可設 `BUZZ_RELAY_URL` 改指自己架的 relay。**build：** clone 後照 README Quick start 用 Docker + Hermit + `just setup && just build`。
- **Repo metadata：** Language Rust；Topics 未列出；License Apache-2.0；Stars 13,645；Forks 1,130。
- **近期 release：** **2026-07-25**；`v0.4.26`；**Buzz Desktop v0.4.26**。

### vercel-labs/scriptc
- **Repo 摘要：** scriptc 是 Vercel Labs 的 TypeScript-to-Native 編譯器：吃普通的 TypeScript、用真的 tsc 做 type check、降到 IR、透過 LLVM（或 fallback 到 C）產出小且快的原生執行檔，binary 內沒有 Node 也沒有 V8。它把「不能靜態編譯」的部分用 quickjs-ng 內嵌處理，並用 800+ 個 corpus 做 differential testing 與 ASan lane 確保與 Node byte-for-byte 一致。Repo 為 TypeScript、Apache-2.0，634 stars，最後更新 2026-07-27（成立於 2026-07-22），首發 5 天。
- **3W1H：**
  - **What：** TypeScript → native compiler + CLI（`scriptc build | run | coverage`），LLVM/C 雙 backend。
  - **Why：** 解決「寫 TS、想部署小且快的 binary、不要拉一個 60–100MB Node SEA」的痛點；對 CLI、edge、agent tool 特別有吸引力。
  - **Who：** 寫 TS/Node 但想要原生 binary 啟動時間與大小的後端／CLI 開發者、做 dev tool 與 agent tool 的人、考慮把 JS 棧往 serverless edge 壓的人。
  - **How：** `npm install -g scriptc`（需 clang；macOS arm64 主平台，Linux/Windows 用 cross-compile），`scriptc run foo.ts` 立刻跑、`scriptc build foo.ts` 產 binary。
- **安裝方式：** **npm/pnpm/yarn/npx：** `npm install -g scriptc`；或 repo 內 `pnpm install && pnpm build`。需要 clang（Xcode Command Line Tools 內建）。
- **Repo metadata：** Language TypeScript；Topics 未列出；License Apache-2.0；Stars 634；Forks 8。
- **近期 release：** **2026-07-27**；`v0.0.17`；**v0.0.17**（一天內連發版）。

### 8NobleTruths/sabba
- **Repo 摘要：** SABBA 是給 coding agent 用的 Security Templates CLI 與 MCP server，主打「每一個 finding 都用執行證明（prove by running）」：模型／Z3 給候選，execution oracle 在 clang+ASan／Foundry／atheris／go fuzz／Jazzer 上實際跑一次，通過才 mint 為 finding，否則直接丟掉。它把 security tooling 變成「harness-untrusted、可重跑、有 bundle 可重現」的流程，並把 Magga change-verification engine vendoring 在 submodule 一起出貨。Repo 為 Python、Apache-2.0，8 stars，最後更新 2026-07-27（成立於 2026-07-13），2 週新。
- **3W1H：**
  - **What：** coding-agent-oriented 的 CLI + MCP server，提供 14 個 tool（`verify_change`/`prove`/`scan`/`hunt`/`security_scan`/`kali_run` 等）做有證據的安全審查。
  - **Why：** 解決 LLM 直接判斷「這段有沒有洞」時 false positive 過高的問題；用 oracle 取代模型當唯一 gate，fuzz 結果也以不可被 harness 偽造的 channel（真實 stack、parent 測量）讀進來。
  - **Who：** 寫 C/C++/Solidity/Python/Go/Java/Node 的安全研究員、需要把 security 跑進 CI 或 coding agent workflow 的團隊、用 Claude Code / Codex / OpenCode / Cursor / Hermes 的人。
  - **How：** `pip install sabba`（或 `pipx install sabba` / `uvx sabba mcp`），跑 `sabba doctor` 檢查 toolchain；agent 端在 `~/.codex/config.toml` 設 `[mcp_servers.sabba] command = "sabba" args = ["mcp"]` 或 `claude mcp add sabba -- sabba mcp`。
- **安裝方式：** **pip/uv/poetry：** `pip install sabba`（或 `pipx install sabba` / `uvx sabba mcp`）。**從 source：** `git clone --recurse-submodules` 後跑 `./install.sh`，會在 `~/.sabba` 建隔離環境並把 `sabba` 放上 PATH。Provers 需對應 toolchain（clang/ASan、Foundry、atheris、go、Jazzer、Jazzer.js），用 `sabba doctor` 確認。
- **Repo metadata：** Language Python；Topics 未列出；License Apache-2.0；Stars 8；Forks 0。
- **近期 release：** **2026-07-27**；`v0.2.1`；**SABBA v0.2.1**（今天剛發）。

## 重點觀察

- 今天 Trending Top 3 落在「去中心化通訊（bitchat）、人機共用瀏覽器（ego-lite）、自架 relay 協作（buzz）」三條不同主線，分別對應 privacy-first、agent browser、agent-native workspace，但都把「人與 agent 放進同一個基礎設施」當核心敘事。
- release 新鮮度明顯兩極：scriptc（v0.0.17）與 sabba（v0.2.1）都在今天 2026-07-27 發版，且都是近 1–2 週才公開的 repo；bitchat v1.7.0 已是 7/8，ego-lite v1.2.5 是 7/17，buzz v0.4.26 是 7/25 — 今天最熱的是「今天剛發」的新專案，不是穩定老專案。
- 安裝門檻分化成四類：(1) `npm install -g`（scriptc）— 一行搞定；(2) `pip install` / `uvx`（sabba）— Python agent 馬上接 MCP；(3) macOS `.dmg` + 可選 `npx skills add`（ego-lite）— 雙軌；(4) `just setup && just dev` 或 Xcode + Team ID（buzz、bitchat）— 需要開發環境。對 agent 開發者最友善的是 scriptc 與 sabba。
- 語言生態：Swift（bitchat）做 iOS/macOS universal、JavaScript（ego-lite）做 agent skill 與 browser 整合、Rust（buzz）做 relay、TypeScript（scriptc）做 compiler、Python（sabba）做安全 oracle + MCP；五個 repo 中有三個提供 npm/pip 套件化入口（scriptc、ego-lite、sabba），是這批觀察中比例最高的一次。
- 兩個 web 探索選擇為 `vercel-labs/scriptc` 與 `8NobleTruths/sabba`：前者是「寫 TS、得 native binary」的 compiler 突破、後者是「用執行 oracle 取代 LLM 判斷」的安全 agent 工具；都和 Trending 的「人機共用基礎設施」主題互補，並且今天都剛發版。
