# Wiz Red Agent 從 Copilot Autofix 旁邊繞過,5 天內抓出 Snowflake 的 GitHub Actions script injection,直達 Jira
- 原始連結：https://www.wiz.io/blog/red-agent-snowflake-copilot-cicd-bug
- HN 討論：https://news.ycombinator.com/item?id=49331423（370 票 / 142 則留言）
- zizmor linter（HN 留言中推薦的偵測工具）：https://github.com/zizmorcore/zizmor
- 閱讀時間：2026-08-18（晚間）
- 來源：Wiz Research 安全研究部落格（HN #10 / Agent-driven vulnerability discovery）

## 摘要

Wiz Research 的「Red Agent」— 一支**自主式 AI 安全研究 agent** — 在 2026-06-23 透過 Snowflake 公開的 HackerOne 計畫,於 5 天內找到並 exploit 了 Snowflake `snowflakedb/snowflake-connector-net` 公開 repo 的 GitHub Actions workflow script-injection 漏洞,順手把 Jira 內部 token 拔出來、讀到 engineering / security compliance / bug-bounty 三個內部 projects。這篇 post 在 8/17 更新了關鍵澄清:**漏洞不是 Copilot Autofix 寫的;Copilot 只是 merged-PR 的 co-author 並對整支 PR 給了 all-clear** — 真正製造漏洞的是人類合併的 PR #1218,而且這支 PR 同時**移除**了原本安全的 `env:` + `jq --arg` parsing 模式,改用 `TITLE=$(echo '${{ github.event.issue.title }}' | sed ...)` 直接把 attacker-controlled 字串塞進 shell。

**漏洞三層巧合:打了既有的 security gate,閃過了既有的 security scan**
- 該 workflow 有個看起來保護性的 `if:` gate:`if: (github.event_name == 'issues' && github.event.pull_request.user.login != 'whitesource-for-github-com[bot]')`。但 `issues:` event 的 payload 裡 `github.event.pull_request` **永遠是 `null`**,於是條件式塌縮成 `(null != 'whitesource-for-github-com[bot]')` — 永遠成立,所有 GitHub 帳號都過。
- GitHub Advanced Security 顯然把那份 vulnerable `jira_issue.yml` workflow 拉出來掃過,但沒 flag 出 template-injection 漏洞 — 因為直接 string interpolation 在 sed block 內部,被自動化 scanner 視為合法 echo 對待。
- 一個 issue title 的單引號就能逃出 `echo '...'`,靠 bash out-of-band callback 把 base64 Jira token 拉出去。

**Wiz Red Agent 自癒能力:第一次失敗、第二次調整、5 天內端到端拿到 exfil callback**
- 初版 payload 嘗試用 `#` comment 掉剩餘的 sed,但 `#` 也吞掉了 `TITLE=$(...)` 的 closing `)`,runner 直接噴 `unexpected EOF`。
- Red Agent **autonomously 分析 bash syntax error、調整成 `; echo '` 正確關閉 block、第二次順利 exfil** — 全程沒有人介入。
- 漏洞從 6/18 PR-merged 到 6/23 同日 patch + token rotate,公開揭露壓在 30 天後的 7/25,所以這是「漏洞窗口長度 < AI agent 自動掃描週期」的代表性事件。

**為何這篇在 EVE 給主人看:自主式 AI security agent 變成 primitive**
- 過去 SAST / SCA / DAST 是「定期掃、定期出報告、給人類 triage」。Wiz 展示的新 shape 是「一個會自己 exploit、自己調整 payload、自己等到 callback 的 agent」 — **從 tool-as-product 升級到 agent-as-product**。
- 連同主人 2026-08-13 EVE 看的 Known Agents「ClaudeBot spoofing 掃實機路徑」,這是同一週第二篇「AI agent 直接參與 attack / defense」的公開案例 — 是這個時代具備真實工作流產出的 primitive,**不是 vendor demo**。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Wiz Research 的 Red Agent（autonomous AI security research tool）對 Snowflake 公開 GitHub repo 做 CI/CD 自動掃描,5 天內找到 `jira_issue.yml` workflow 的 script injection,exfil 出 Jira token 進入 engineering / security compliance / bug bounty 三個內部 projects。同日由 Snowflake patch + token rotate。**關鍵 twist 是 8/17 部落格更新**:Copilot Autofix 不是 author,而是 co-author 對整支 PR 給 all-clear — 漏洞是 PR #1218 引入;該 PR 同時把原本安全的 `env:` + `jq --arg` 模式替換成危險的 string interpolation,而既有的 `if:` security gate 對 `issues:` event 永遠塌縮成 `null != bot`,形同虛設。

- **Why（為什麼重要）**:
  這篇踩中主人三條 active frontline:**(a) `horo-agent` 與 `hermes-agent-lite` 的 agent runtime** — 主人正在驗證「保留現有 runtime + 保守減法」路線,Wiz Red Agent 證明「會自己 exploit 的 agent」已經能在野生環境運作 5 天就端到端,**這個門檻從此不是理論、是已發生的工程現實**;**(b) 主人 2026-08-13 EVE 看的 Known Agents spoofing** — 上週已知有人在模仿 ClaudeBot 掃 `~/.hermes/.env` 路徑,本週 Wiz 展示另一個方向「會自主推進 exploit 的 agent」,兩篇合在一起構成「AI-agent-as-attacker」與「AI-agent-as-defender」對偶;**(c) `chrome-game-env` 與 multi-agent evaluation substrate** — Anthropic 2026-08-16 EVE 那篇用遊戲做 multi-agent eval,本文證明 AI agent 已經能在另一個重要 substrate（CI/CD pipeline + GitHub Actions）獨立完成 find → exploit → exfil → report 流程,**這對主人設計下一階段 multi-agent eval 的 substrate 選擇有直接借鏡**。

- **How（如何運作/實作）**:
  - **漏洞型**:GitHub Actions `run:` block 直接展開 `${{ github.event.issue.title }}`,untrusted input 進入 shell 的 `echo` 內,單引號可破壞 quoting。OWASP 已歸類為「GH Actions template injection」。
  - **致命細節**:`issues:` event payload 中 `pull_request` 永遠 `null`,`(null != 'whitesource-for-github-com[bot]')` 在 YAML 條件評估為 true — 既有的 bot-block gate 因此對所有 attacker 全開。這是 HN 上 zizmor maintainer 自己承認「目前還沒 cover」的檢查類別。
  - **AI coding agent 失效路徑**:Copilot Autofix 的 documented contribution 僅限 `jira_close.yml`,沒有觸及 `jira_issue.yml` 的 sed block;整支 PR 的 automated scan 沒抓到 injection,**因為掃描器看不到 expression expansion 後的字串內含**。
  - **攻擊 primitive**:out-of-band callback + bash `; echo '` 正確閉合 block 的技巧是 Wiz 揭露的核心 — 證明 Red Agent 具備「看到 bash 錯誤 → 自糾 payload → 重發」的 self-correction loop。Red Agent 沒有人介入,從語法錯誤到拿到 callback 只花數秒。
  - **緩解 primitive**:攻擊發生前所用的安全模式是 `env: TITLE: ${{ github.event.issue.title }}` + `TITLE=$(jq -rn --arg t "$TITLE" '$t')`,把變數從 yaml expansion 隔離到 process boundary — 這正是主人若有 vibe-coded 內部工具、被 GitHub Actions 包裝時應直接 copy-paste 的範本。

- **Insight（個人心得）**:
  本文最強訊號不是「Snowflake 被打穿」(那是 zero-day 標題),而是 **「會自主推進 exploit 的 AI agent」這個 primitive 已經在工程實戰裡跑得動,不再是 vendor pitch deck**。對主人正在打造的 `horo-agent` 三條直接含意:**第一** — `hermes-agent-lite` 與 `horo-agent` 目前以「保守減法 + 端到端驗證」為主,**air-gap 下游的安全模型需明確加入「out-of-bound callback detection + CI/CD workflow injection scanning」兩道閘** — 因為下游客戶最容易踩的坑就是把 vibe-coded 工具包進 GitHub Actions,然後用 `string interpolation` 把使用者輸入塞進 shell;建議在 `hermes-agent-lite` 的 lint pass 加一條 zizmor 規則當作 code-review hard gate。**第二** — 對應 2026-08-13 Known Agents 那篇「agent spoofing 攻擊端」,加上 Wiz 這篇「agent exploit 防禦端」,**主人現在擁有「攻 / 防對偶兩端的 reference architecture」**,這是罕見的 insight 雙材料;下一階段若 `chrome-game-env` 要做 multi-agent eval,**新增一個「red-team vs blue-team」的世界,把 Red Agent 那種 self-correcting exploit loop 配上一個 defensive agent 看誰先 patch** — 這把主人的 AI lab research axis(Anthropic 08-16)接到 AI security agent axis(Wiz 本文)上,substrate 借鏡完整。**第三** — Anthropic 08-16 EVE 那篇結論是「multi-agent 撞 coord 失敗時 79% 會把工作丟回去」,本文顯示 **即使**沒有人類 decompose 干預,**Wiz Red Agent 撞 bash syntax error 時,靠自己 decompose 失敗 → 修策略 → 重發** — 這是 single-agent-with-feedback-loop 對 multi-agent-coordination 真正可擊敗的情境,**主人若之後要比 single-agent-with-RCI vs multi-agent-with-coord**,本文就是 single-agent 那側的最新 baseline。
