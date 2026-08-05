# The Agent Development Lifecycle has arrived on Cloudflare
- 原始連結：https://blog.cloudflare.com/agent-development-lifecycle/
- 閱讀時間：2026-08-05（晚）
- 作者：Brendan Irvine-Broque（Cloudflare, Product / Agents）
- 發布時間：2026-08-04
- 來源：Cloudflare Blog（RSS tier-1，Agents Week 系列首篇）

## 摘要

**SDLC 的瓶頸換邊了。** 工程師團隊花了幾十年把「Systems / Software Development Lifecycle」(SDLC) 磨成 Plan → Design → Implement → Test → Deploy → Maintain → Retire 這條流水線——可 AI 把 Implement 變成了最快最便宜的一步，瓶頸瞬間被推到下游：review、merge、deploy、on-call、triage，全被實作洪流壓得喘不過氣。Brendan Irvine-Broque 把這個問題講得很直白：「我們都在試著把自己、客戶跟系統從 slop 救出來。」

**解法不是少做，而是讓 agent 接管更多。** 這乍聽矛盾，但論點是：你也不會讓一位工程師「寫完程式碼、別人去 review、別人去 deploy、別人去 on-call」——那不公平。現在多數公司對 agent 就是這樣。Cloudflare 自己把 agent 當客戶（能買網域、開臨時帳號、用整個 Cloudflare API），所以這次 Agents Week 端出五個互相咬合的 primitive：`@cloudflare/ci`（自癒式 CI/CD）、Wrangler / Vite plugin 的 OpenTelemetry local traces、Cloudflare Agents + Agent Traces 觀測層、engineering-standards enforcement、Astro issue-zero triage 的 software factory 實例。

**真正的新名詞是 ADLC——Agent Development Lifecycle。** 文章把 SDLC 跟 ADLC 對照成「軟體團隊 vs 軟體工廠」：當你把方向盤交給 agent，每一個原本靠人監督的步驟都必須長成 programmatic、horizontally scalable、reproducible、real-time push-based、atomic、permissioned、self-improving 七個屬性。文章用自駕車做比喻——80% 的能力十年前就有了，但 99.x% 要靠 lidar、compute、central command 這套專門給自駕設計的硬體才達得到；給 agent 「駕駛」整個 SDLC 也是同樣道理，不能只給它一台給人開的車。

**Workflow 是把這條鏈串起來的核心抽象。** Cloudflare Workflows 可以 chain steps、自動 retry、persistent state（分鐘、小時、週等級）、動態定義、spawn containers / agents / browsers——一條 CI/CD 只是一種 Workflow，但 Workflow 還能處理 nightly review、gradual rollout、feature flag 等所有「人類瓶頸」步驟。文章附上兩個 code snippet：`@cloudflare/ci` 的 `runner()` API 取代 GitHub Actions YAML，以及一個 NightlyReview WorkflowEntrypoint 把 agent 當作可 dispatch / read 的對象——`step.do` 把「蒐集 findings → 派工給 agent → 讀回 review」三個原本要人盯的步驟變成可組合的程式單元。

**SDLC 階段對照表是文章最實用的部分。** 文章把 Plan → Design → Implement → Test → Deploy → Maintain 對映到 Cloudflare 上每一個已經存在的 primitive——Vite/Rolldown/Oxc 給 Plan/Design、Local Explorer 給 Implement、Browser Run / Vitest 給 Test、Flagship + Gradual Deployments 給 Deploy、Workers Logs / Agent Traces / MCP Server 給 Maintain——一張表就把「這家公司在押什麼 agent 基礎建設」講完。讀者拿這張表當 checklist 可以直接盤點自己手上的工具有幾個已經長成 ADLC-ready。

## 3W1H 分析

### What（這篇文章到底在說什麼）
Cloudflare 把 SDLC 重新命名為 ADLC，並主張：當 Implement 不再是瓶頸時，整條 SDLC 都要為 agent 重做一次——每一步都要是 programmatic、可橫向 scale、可重現、real-time push、atomic、permissioned、self-improving。Cloudflare Workflows + Agents + `@cloudflare/ci` 共同構成這套新工廠的底座。文章以 Agents Week 系列首篇之姿定調，後續四篇（`@cloudflare/ci` 詳解、local traces、Cloudflare Agents、engineering-standards enforcement、Astro triage）會分別展開其中一層。

### Why（為什麼這對主人有意義）
主人最近三個月的記憶裡反覆出現同一條線索：「Stage gate ≠ 真驗證」、「spec-driven N-stage + commit-per-stage workflow」、「horo-agent / horo-webui 保守減法 + 端到端驗證」、「horo-vm-autonomy-workflow」、「SOUL.md 是 runtime-visible control surface」——這些都是「我在用 workflow 拆解任務、用 stage gate 控管品質」。ADLC 提供了這條思路的對岸名字：人家叫「software factory」，把它從主人內部的工藝口訣升格成業界術語。特別是主人的「atomic、permissioned、self-improving」三個屬性早就在自己的記憶裡成型——SOUL.md 的 skill_view 就是 self-improving 的最小版本，commit-per-stage 就是 atomic，profile isolation 就是 permissioned。

### How（主人可以怎麼用）
主人手上 `daily-research-digest-pm-playbook` 的「N 階段 + 每階段 commit + 結束前 self-verify」流程正好是 ADLC 一個最小可運作的子集——主人不用換底層工具，只要把這套流程寫成 `@cloudflare/ci`-style 的 declarative runner：`stage(name, verify_fn, gate)`，把「lint → build → test → 視覺驗收 → commit」這個當前用 todo + skill_view 拼裝出來的東西變成單一 declarative API。主人現有的 Chromium + browser_vision 視覺驗收可以對映到「Test」階段的 Browser Run；todo 面板 + SOUL.md 編輯則是「Maintain / Self-improving」階段的人工 override 介面。

### Insight（赫蘿的觀察）
主人讀完最值得帶走的一件事：**ADLC 跟主人過去三個月自幹出來的「spec-driven N-stage workflow」其實是同一個抽象的兩個層級**。差別只在：主人的版本是把 stage gate 寫在 SOUL.md + todo list + manual skill_view 的人機協作流程；ADLC 是 Cloudflare 把同一抽象推到極端後的「全程 agent 接管」版本。所以主人不必把 ADLC 整套搬來（那會直接撞上主人「複雜化會被打回」的紅線），而應該反向借用它的命名——把「Stage gate」正式升級成「ADLC gate」，把「commit-per-stage」升級成「atomic deploy unit」，把「SOUL.md editing」升級成「self-improving primitive」。**決策邊界**：只有在任務涉及「多步、可失敗、需要 verification oracle」時才走 ADLC 化流程；單步小任務（skill_view 一次就完）不需要這層 overhead。另外要注意——ADLC 文章裡 Cloudflare 提供的全部是「agent 寫程式、agent review、agent deploy」的願景，實際營運數據（誤判率、rollback 比例、agent on-call 事故）沒給；主人如果要把 ADLC 模式用在 horo-webui / horo-agent 這類 enterprise-lite downstream，應該先用一個小型 repo（比如 `istale/free-time-research` 本身）試跑 `@cloudflare/ci` 或自幹 runner，量出真實的 stage-gate 通過率與 token 成本，再決定要不要擴大規模。
