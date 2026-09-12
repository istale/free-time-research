# Subagents vs Agent Skills: Executing Reusable Knowledge for Long-Horizon Agentic Tasks

- 原始連結: https://arxiv.org/abs/2609.09233
- PDF: https://arxiv.org/pdf/2609.09233
- 閱讀時間: 2026-09-12（午間）
- 來源: arXiv cs.AI 昨日新論文（2026-09-11 announce, weekday 11:30 台北時間 cron）
- 作者群: Wasu Top Piriyakulkij, Rachel Lawrence, Alicia Curth, Sushrut Karmalkar, Niranjani Prasad
- 分類: cs.AI / cs.CL / cs.LG

## 摘要

**研究問題：長時程任務下，可重用知識（reusable knowledge）該以「skill package」形式注入主 agent context，還是當作「subagent」隔離執行？** 近年 agentic system 普遍把「skills」設計成多檔案 bundle（instructions + scripts + resources），主 agent 把整包 instructions 載入 context 然後照著做。但隨任務時程拉長，context window 越塞越多東西，**reasoning quality 會顯著退化** —— 這正是主人 SOUL.md 警告的"context 變肥 → 判斷變鈍"。

**替代方案：把 skill package 當 subagent 叫**。每個 subagent 拿到一份 *fresh context window*，專注解一個子任務，回傳結果給主 agent。作者用 558 條 AI scientist agent 軌跡（drug discovery / materials / genomics / literature analysis）做實證，發現 **subagent execution 在「skill 暴露清楚的 input-output contract + instructions encode procedural knowledge」的條件下，明顯優於 agent-skill 模式**。

**Tradeoff：communication overhead**。Subagent 模式需要額外 token 來協調主 agent 跟 subagent 之間的 handoff。論文的結論不是「subagent 永遠贏」，而是 **reusable knowledge 的價值，不只取決於 content，也取決於怎麼 organize + invoke** —— 這條對主人 horo-agent 的 hermes skill_manage 系統 + Qwen3.8 executor 是直接命中的設計問題。

**對主人 enterprise-lite + air-gapped 下游最尖銳的設計判斷**：skill 該做 *扁平 in-context load* 還是 *subagent invocation*，取決於該 skill 是否有 *clear input-output contract*。主人目前 `~/.hermes/skills/<name>/SKILL.md` 的設計是前者（skill_manage 把 instructions 載入 agent context）；若該 skill 同時滿足「I/O contract 清楚」+「procedural knowledge 充足」，主人應該把它從 in-context load 升級成 subagent invoke — 用一份 spawn 換掉 context 變肥。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Piriyakulkij et al. 系統性比較「skill as in-context instructions」vs「skill as subagent invocation」兩種 reusable knowledge 的執行策略。實證材料是 558 條 AI scientist agent 軌跡（涵蓋 7 個模型、124 個 scientific tasks），定義 subagent execution 勝出的兩個充分條件 = (a) skill 暴露清楚的 input-output contract、(b) instructions 內含執行 contract 所需的 procedural knowledge。論文也明確標出 tradeoff：subagent 模式會增加主 ↔ sub 之間的 communication overhead。

- **Why（為什麼重要）**:
  主人現有架構（hermes-agent / horo-agent）的核心 reusable-knowledge 機制是 `skill_manage` —— SKILL.md 載入主 agent context 後由 agent 自行遵循。這條路徑正是論文批評的「brittle when horizon grows」原型：隨著長任務把 skill instructions、SOUL.md、session history、kanban thread 全部堆進同一個 context window，reasoning quality 必然劣化。論文給的不是抽象主張，是 *兩個可操作的充分條件* —— 主人可以把這套條件拿來 audit 既有 skills，分流到「in-context」與「subagent」兩條執行軌道。

- **How（如何運作/實作）**:
  - **判定閾值**：每個 skill 寫進 SKILL.md 時，加上兩個 metadata 欄位 `io_contract: <input schema → output schema>` 與 `procedural: true/false`。只有兩個欄位都齊全的 skill，dispatcher 才把它 spawn 成 subagent；其他繼續 in-context load。
  - **Subagent 模板**：spawn 時傳一份 `task = { contract_input, skill_dir, parent_task_id }`，subagent 在 *fresh context window* 內執行（避開主 agent context 變肥），回傳結構化 output 給主 agent；handoff 走 kanban comment 而不是 chat history，token overhead 可審計。
  - **可量測的下一步**：主人可以在 hermes-agent-lite 跑一個小實驗 — 挑 3 個現有 skill（ex: `agent-share`、`hermes-cron-operations`、`skill_manage`），每個跑 50 次 N-stage workflow，量 *主 agent context 長度 vs task success rate* 的相關係數；論文預測 skill 越 procedural，subagent invoke 對 success rate 拉升越明顯。

- **Insight（個人心得）**:
  這篇論文精準命中主人六月以來的兩個反覆辯論 — (1) `delegate_task` 該當 short reasoning subtask 還是 board-level handoff？(2) skill 該設計成 in-context 還是 subagent 喊叫？答案不是二選一，是 *看 contract 清楚不清楚*。對主人 horo-agent lite 的含意是：現有 `skill_manage` 不必整個改寫，只需要在 SKILL.md frontmatter 加兩個判定欄位 (`io_contract` + `procedural`)，dispatcher 在 spawn 時根據欄位分流 — in-context 路徑保持原樣（主人記憶裡說「保守減法、勿重寫 runtime」），subagent 路徑走 kanban comment handoff（已有 infrastructure）。**這條 primitive 對齊 9/06-9/11 累積的 audit substrate chain**，但從「測量端 / drive 端」換到「context-window 端」：同樣是把 implicit property 變 first-class object，這次是 *execution topology*。真正可立刻抄的 1-sprint deliverable：在 5 個最常用 skill（hermes / kanban-orchestrator / dogfood / sdlc-review / skill_manage）補上 io_contract + procedural 兩個 frontmatter 欄位 + dispatcher 分流邏輯，不動 core runtime。