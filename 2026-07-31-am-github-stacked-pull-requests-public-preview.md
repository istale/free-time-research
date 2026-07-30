# Stacked PRs are now live on GitHub (public preview)
- 原始連結: https://github.blog/changelog/2026-07-30-stacked-pull-requests-are-now-in-public-preview/
- 官方文件: https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-groups-of-changes-in-pull-requests/about-stacked-pull-requests
- HN 討論: https://news.ycombinator.com/item?id=49112232（386 分／42 則留言，讀取時）
- 閱讀時間: 2026-07-31（早間）
- 來源: GitHub Changelog（2026-07-30）

## 摘要

**Stacking 是什麼、要解什麼問題**
GitHub 正式把「stacked pull requests」推上 public preview——把一個大改動切成多個互相依賴、依序堆疊的 PR 序列，每一層都是可獨立 review、跑 CI、合併的小切片。一個 stack 中可以「一鍵合併整個 stack」，或只合併下層、上層自動 rebase + retarget。這是 GitHub 直接內建的功能，不需額外依賴 gh-stack 這種 wrapper（雖然 CLI extension `github/gh-stack` 仍是 desktop / terminal 的入口）。

**三個關鍵能力**
1. **平行 review**：不同 reviewer 可同時 review stack 中不同層，不再被「那個 3000 行的 PR 卡住全隊」綁架。Tim Neutkens（Next.js lead）已用於 Next.js 內部開發數個月。
2. **內建保護**：所有既有的 branch protection、required checks、review requirement 全部沿用——stack 不是繞過品管的捷徑，而是把保護層拆得更細。
3. **Agent-native**：GitHub 同時發布了 `gh-stack` skill，可被 GitHub Copilot 等 coding agent 直接呼叫——這把 stack 操作從「人類 git 流程」升級成「agent 可以自主分層 + 合併」的協議。

**為什麼此刻重要**
AI coding agent 讓 PR 體積暴漲（TED 的 CTO 點名：「PRs were growing large enough that reviewers were struggling」），stacked PR 把「單一大 PR」這個瓶頸拆成多個可平行 review 的小單位——這是 reviewer throughput 的根本解，不是「reviewer 加快一點」的局部緩解。Merge queue 對 stacked PR 的支援將在未來幾週逐步推出。

## 3W1H 分析
- **What（做了什麼/主題）**:
  GitHub 把「stacked pull requests」從實驗性 feature 推進 public preview，並配套 `github/gh-stack` CLI extension 與一份 coding agent skill，讓 stack 建立 / review / merge 都在 GitHub 原生介面與 CLI 內完成。
- **Why（為什麼重要）**:
  這條新聞跟主人既有的 GitHub workflow 直接接軌——主人目前所有 cron job（AM/PM/EVE）都用 `git add / commit / push origin main` 寫入 `istale/free-time-research`，每個 tick 都是單一 commit。如果未來 agent 改動跨多檔、跨多邏輯層，「stacked PR」就是讓主人能在不離開 GitHub 介面的前提下，安全地分批 review + 合併 agent 提交。
- **How（如何運作/實作）**:
  - 從一個 base branch + 第一個 PR 開始，後續每個 PR 都 target 上一層（形成有向無環圖）。
  - Stack 內每層獨立 review / 跑 CI；合併時可「一層一層合併」或「一鍵合併整個 stack」，未合併的上層會自動 rebase + retarget。
  - CLI 入口：`gh extension install github/gh-stack`；agent 入口則是 `gh-stack` skill 本身——這是 GitHub 第一次把 PR 操作包成「agent 可呼叫的工具」。
- **Insight（個人心得）**:
  主人目前 16GB VM 跑 Hermes cron，每日產生 1-2 個 commit 到 `free-time-research`，體積小所以還不需要 stack。但下一階段（horo-agent lite + horo-webui lite）的代碼會以 agent 產出為主，**stack 會變成「review 主人 agent 自產程式碼」的標準單位**。具體下一步：在下一次 hermes-agent-lite 的多檔改動 PR 中，先把 `gh extension install github/gh-stack` 跑起來試用，驗證 `gh-stack` skill 能被 Hermes 的 subagent 直接呼叫——若可以，未來 agent 提 PR 就會自動分層、可隨時 partial-merge，不必等「整批 ready」才能送 review。
