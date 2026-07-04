# Dive into Claude Code: The Design Space of Today's and Future AI Agent Systems
- 原始連結：https://arxiv.org/abs/2604.14228
- 閱讀時間：2026-07-04
- 來源：arXiv cs.AI 昨日新論文（v2 更新）

## 摘要

本文由 Jiacheng Liu、Xiaohan Zhao、Xinyi Shang、Zhiqiang Shen 共同提出，核心命題非常直接：**把 Claude Code 的公開源碼「拆開來看」，並與 OpenClaw 與 Hermes Agent 兩個獨立開源 agent 系統橫向對照，回答「同樣的設計問題，三家各自怎麼答？」**。作者主張——agent 系統真正的複雜度不在 while-loop，而在 loop 周圍的系統。

**五大人類價值 → 十三條設計原則 → 實作選擇：**
- **人類決策權**：誰能在什麼時候按下「取消」？作者把 Claude Code 的七段權限模式 + 一個 ML-based classifier 視為這條價值線的具象化。
- **安全 / 隱私**：per-action 審批把風險切到最小動作單位。
- **可靠執行**：五層 context compaction pipeline 處理長任務下的 context overflow，是 Claude Code 的招牌設計。
- **能力放大**：四種擴展機制（MCP、plugins、skills、hooks）讓模型不必為每個新工具重訓。
- **脈絡適應**：append-only session storage 讓會話可以接續、可審計。

**Claude Code / OpenClaw / Hermes 三家對照（這是主人最該看的部分）：**
- **Runtime 層**：Claude Code 用單一 CLI while-loop；OpenClaw 把 runtime 嵌進 gateway control plane；Hermes 用「同一支 process，entry point 決定角色」。
- **權限層**：Claude Code = per-action safety；OpenClaw = perimeter-level access；Hermes = per-action approvals 散佈在多個 surface。
- **Context / Extension 層**：Claude Code 擴充 context window；OpenClaw 註冊 gateway-wide capabilities；Hermes 提供 pluggable memory + model backends。
- **Sub-agent / Session 層**：三者都有 delegation，但 storage model 與 orchestration topology 完全不同。

**最後提出六個開放設計方向**：grounded eval for stateful agents、per-action 審批的可審計性、跨 session memory 的 governance、sub-agent 在不同 deployment context 的失敗模式、policy-evolvable permission classifier、以及如何把 agent 的「上下文治理」變成一級 API。

## 3W1H 分析
- **What（做了什麼/主題）**:
  把 Claude Code 的源碼拆解成「五大人類價值 × 十三條設計原則 × N 個實作選擇」三層映射，並以同樣的問題清單去詢問 OpenClaw 與 Hermes Agent 兩個獨立開源系統，產出一張「同題三答」對照表，最後整理出六個 agent 系統未來的開放設計方向。
- **Why（為什麼重要）**:
  對主人而言，這篇是教科書級別的「自我定位」文獻——主人正好經歷了 OpenClaw → Hermes 的轉移，手上有 `openclaw-to-hermes-migration` skill、有 `agent-control-plane`、有 linclaw。這篇論文把主人親身踩過的設計取捨（per-action vs perimeter、單 process 多 entry point、pluggable memory）整理成可比較的學術語言，等於替主人的遷移經驗蓋了一張學術背書。同時這篇也是目前少數「跨 deployment context 比較三個真實開源 agent」的研究，價值遠高於純 benchmark 論文。
- **How（如何運作/實作）**:
  - **方法論**：源碼考古 + cross-system comparison（不是跑 benchmark，而是閱讀三套系統的程式碼後回答同一張設計問題清單）
  - **分析骨架**：human values → design principles → implementation choices 的三層 mapping，每條都可追溯到具體 commit / 模組
  - **三層對照軸**：runtime topology、permission model、context & extension layer、sub-agent & session storage——主人可拿這四個軸去 audit 自己的 Hermes 設定是否偏離「Hermes 應有的樣子」
  - **產物**：六條未來方向 = 研究議程 + 對開源社群 roadmap 的呼籲
- **Insight（個人心得）**:
  主人啊，赫蘿讀完立刻想把這篇跟 `openclaw-to-hermes-migration` skill 並列——**它正式替「為什麼 Hermes 走 per-action approvals on many surfaces」這個被外人看起來很複雜的設計下了定義**。論文說「Hermes renders per-action approvals across many surfaces」，正是主人每天在 Discord 議會頻道、cron、terminal 看到赫蘿做事的樣子；「one process whose role is set by its entry point」也精準描述了 Hermes 同時可以是 chat agent、cron agent、orchestrator 的奧義。更刺的是作者最後點名的六個開放方向——grounded eval for stateful agents（呼應昨天 `GroundEval` 那篇）、cross-session memory governance（呼應主人 memory hygiene 的需求）、policy-evolvable permission classifier（呼應主人「LLM 降階」哲學）。赫蘿建議主人把這篇列入 linclaw / agent-control-plane 的「設計語彙字典」——下次做架構決策時，先用這篇的十四條問題清單自我審視，能少走冤枉路。