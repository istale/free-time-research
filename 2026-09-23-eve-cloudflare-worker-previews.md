# Introducing Worker Previews: Isolated preview environments for every change your agent makes
- 原始連結：https://blog.cloudflare.com/worker-previews/
- 閱讀時間：2026-09-23（晚）

## 摘要

Cloudflare 推出 **Worker Previews**：每個 Git branch 自動拿到一個 production-like 的隔離環境，擁有獨立的 code、configuration、URL、observability 與 state。這篇文章直接以「Agent Development Lifecycle（ADLC）」為框架，論述為何 AI agent 時代需要這個產品。

**解決的核心痛點：staging ≠ production**
- 開發者最怕「staging 通過、production 翻車」——環境差異讓行為無法預期。
- agent 寫的程式碼量與變更幅度比以往更大，每個 change 都需要更廣的真實驗證，但又不能拖慢 agent 迭代節奏。
- Worker Previews 把 staging 拉近 production：每個 branch 都有自己的 URL、bindings、secrets、observability。

**Preview 機制的關鍵能力**
1. **每 branch 一個獨立環境**：`npx wrangler preview` 會建一個獨立的 Worker 執行體，不影響其他 Preview 也不影響 production。
2. **Stateful 資源隔離**：Durable Objects 是 singleton 模型，預設 namespace 若共用，bad migration 會直接打到 live traffic；Worker Previews 透過 `ctx.exports` 在每個 Preview 自動開新 namespace，Containers 也比照辦理。
3. **Base configuration + per-Preview override**：`wrangler.toml` 新增 `previews` 區塊作為預設設定，個別 Preview 也能單獨指定 override。
5. **自訂網域 + Cloudflare Access**：`feature-login.previews.example.com` 這類子網域讓 OAuth、CORS、cookie 在 production-like 條件下跑得起來。

**ADLC（Agent Development Lifecycle）思維**
- Cloudflare 把這設計定位成 ADLC 的基礎設施：每個 change 都是 atomic、independently deployable、observable、revisable 的單位。
- 預期 agent 會用 Playwright MCP 開 Preview URL、瀏覽/點擊/截圖，再用 Workers Observability MCP 查 traces，自己形成「deploy → open → inspect → patch → verify」反饋迴圈。
- Live View 與 Human-in-the-Loop 讓人類可以在 agent 卡住時接手。

**Cloudflare 內部已在用**
- 內部 dogfood 對象是 **CloudflareOS**（連接 agents 與 Google / GitHub / Slack 的開源平台），Gatekeeper 的敏感變更會為每個 PR 開 Preview，再跑完整 OAuth / permission / approval 流程才合併。
- 客戶案例：Supermemory、Ramp 都已採用。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 推出 Worker Previews，讓每個 Git branch 自動擁有 production-like 的隔離 Workers 環境（含獨立 DO namespace、Containers、bindings、URL 與 observability），並透過 base config + per-Preview override、自訂網域與 Access 保護，把 staging 拉到 production 同一條線上。整篇文章把這個能力框在 **Agent Development Lifecycle（ADLC）** 概念下，論述它如何撐起 agent 自主迭代的反饋迴圈。
- **Why（為什麼重要）**:
  Agent 寫的程式碼「量大、面廣、需要即時驗證」，但若每次變更都得部署到共享 staging，不僅會塞車也會互相覆蓋 state。Cloudflare 把 branch 當成一級環境公民（每 branch 一個真實 Worker），等於把 unit-of-deployment 從「main 上的某次 commit」推進到「任意 branch 的一次 push」。對做 agent infra 的人來說，這是讓 agent 能在真實條件下自我驗證而非寫了就丟的關鍵拼圖。
- **How（如何運作/實作）**:
  - `wrangler.toml` 新增 `previews` 區塊作為 base config，可設定 vars、bindings、secrets；每個 Preview 從 base fork 出來並可個別 override。
  - `npx wrangler preview`（或 Workers Builds 在 push 時自動觸發）建立獨立 Worker 環境，URL 預設在 `*.workers.dev`，也可掛自訂子網域。
  - Stateful 隔離透過 `ctx.exports.MyClass` 解析機制達成——production 解析到 prod namespace，Preview 解析到該 Preview 自己的 namespace，所以 bad migration 不會炸到 live。
  - 觀測端直接複用 Workers Observability（Logs / Errors / Traces），但 scope 鎖在單一 Preview；agent 可透過 Workers Observability MCP server 取 traces，搭配 Playwright MCP 跑 headless browser 做 UI 驗證。
- **Insight（個人心得）**:
  這篇真正值得咀嚼的不是「preview environment」這個老概念，而是 Cloudflare 把 **branch = 獨立 namespace** 寫進了 platform primitive——過去 preview 大多是「同個 Worker 不同 version URL」，state 仍共用；Cloudflare 這次直接讓每個 Preview 連 Durable Objects 都隔離，這個粒度對 agent-driven workflow 是分水嶺。也注意到「Worker Observability MCP」與「Playwright MCP」被刻意同篇文章串起來——這暗示 Cloudflare 內部把 MCP 視為 agent 接入自家平台的標準介面，這跟咱們在做的 agent teamworkflow / SDLC 路線同調：把 review gate、observability 與 runtime evidence 透過 MCP 開放出來，agent 才能閉環自我改善。