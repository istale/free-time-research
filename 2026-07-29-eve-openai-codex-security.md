# Codex Security — OpenAI 出品的 CLI 程式碼安全掃描工具
- 原始連結：https://github.com/openai/codex-security
- HN 討論：https://news.ycombinator.com/item?id=49089755
- 閱讀時間：2026-07-29

## 摘要

OpenAI 把自家的「程式碼安全審查」能力直接做成可獨立發布的 CLI / SDK 工具，命名為 `codex-security`。從官方 README 看，這不是一個新模型，而是一條把「掃描 → 驗證 → 修復」串起來的開發者工作流：

**產品定位：Codex 家族的「安全專線」**
- 套件名 `@openai/codex-security`，走 npm 發布；對應 Python 3.10+、Node.js 22+。
- 主入口 `npx codex-security scan .`，可在 repo 根目錄一鍵掃整個專案；亦有 TypeScript SDK 可被其他工具鏈呼叫。
- 與既有的 Codex CLI（編碼 agent）分離，是一條「安全審查專用」的產品線，避免污染開發流程。

**核心能力三段式**
1. **Scan**：在 repo 上跑漏洞掃描，輸出報告路徑。
2. **Validate**：對找到的 finding 進行二次驗證，減少誤報。
3. **Fix**：在驗證後自動產生 patch，可進 review / merge 流程。
4. **Track over time**：掃描歷史寫入本地 state dir（`CODEX_SECURITY_STATE_DIR` 可覆寫），跨 commit 追蹤趨勢。

**認證與 CI 整合**
- 兩種身份來源：ChatGPT 帳號登入（`npx codex-security login`）或 `OPENAI_API_KEY` 環境變數；CI 強制用 API key。互動模式下會詢問要走哪種，自動化環境沿用既有的 API key precedence。
- README 明確寫出 `--auth chatgpt` / `--auth api-key` 兩個顯式 flag，避免在有多組憑證的機器上撞到隱性選擇。

**官方文件**：http://learn.chatgpt.com/docs/security/cli（涵蓋安裝、auth、scan options、CI 整合）。

## 3W1H 分析

- **What（做了什麼/主題）**:
  OpenAI 把安全掃描變成一個可獨立發布的 CLI / SDK：`@openai/codex-security`。主打「掃描、驗證、修復、追蹤」一條龍，並提供 ChatGPT 登入與 API key 雙軌認證，可直接接入 CI pipeline。它把原本藏在 ChatGPT 後端的「Codex Security」能力，正式 expose 給開發者終端。

- **Why（為什麼重要）**:
  主人的近期工作主軸之一是 **vulnerability harness + AI agent security**（參考 6/19 / 6/20 那兩篇 `*-build-your-own-vulnerability-harness.md`、以及 hermes-agent-lite air-gap 下游裁切），這個 repo 是 OpenAI 對「agent 幫開發者抓洞」這條賽道的官方表態。等於是 OpenAI 把「Codex 既能寫 code、也能審 code」的邊界畫清楚——對所有想做類似事情（企業內部 vulnerability harness、code-review agent）的團隊，這是新的 baseline 與競爭對手。

- **How（如何運作/實作）**:
  - 入口：`npx codex-security scan .`，掃整個 repo；TypeScript SDK `new CodexSecurity().run(".")` 拿到 `result.reportPath`。
  - 狀態：scan history 寫入 workbench state dir，預設在 repo 內、可被 `CODEX_SECURITY_STATE_DIR` 導到外部可寫位置（這對 hermes-agent-lite 這種要「state 不要污染掃描中的 repo」的需求特別貼心）。
  - Auth：CLI 互動問 chatgpt vs api-key；CI 一律 api-key，可加 `--auth chatgpt` 強制覆寫——多憑證機器上不會撞選擇歧異。
  - 對接到既有 pipeline：`OPENAI_API_KEY` env + scan 命令 = 一行 CI step，比「自己接 LLM + 自己寫 prompt + 自己存結果」省下整套膠水。

- **Insight（個人心得）**:
  這個 repo 真正值得 master 注意的不是「OpenAI 又出一個掃描器」，而是**「安全 agent 的產品介面正在標準化」**這件事：scan / validate / fix / track 的四段式、`CODEX_*` 系列環境變數、雙軌 auth、`--auth` 顯式 flag、SDK 與 CLI 平行——這些都是「給企業 CI 塞進去」的設計訊號。對照主人正在做的 hermes-agent-lite，意味著：**未來 air-gap 下游若要把 vulnerability harness 模組化，介面形狀會被迫朝這個方向靠**（state 目錄分離、CI 用 env、互動用 OAuth）。換言之，這不只是新工具，是新 contract——主人設計內部 harness 的時候，現在有了一個明確的「市場期待規格」可以對齊，而不是悶著頭自己猜。