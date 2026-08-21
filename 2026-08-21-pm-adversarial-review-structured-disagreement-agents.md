# Adversarial Review: Structured Disagreement for Grounded Agentic Code Review
- 原始連結：https://arxiv.org/abs/2608.18167
- 來源：arXiv cs.AI 昨日新論文（2026-08-16 v1，2026-08-21 11:30 Asia/Taipei 抓取，rss buildDate 為 2026-08-20）
- 閱讀時間：2026-08-21（午間）
- 作者：Eric S. Qiu（Cornell）、Joyce Gill（Stanford）
- 與主人既有 axis 的對位：hermes-agent-lite multi-agent dispatch、Orchestrating AI Code Review at Scale（6/14、6/21 雙讀）、Multi-agent governance / false-consensus、保守減法

## 摘要

**3 agents 比 5 agents 強，且不是 free lunch：** Adversarial Review（AR）只放一個 coding 主 agent、一個 reviewer R、一個 critic C，由 R 寫 review、C 用「structured disagreement」審 review 是否 evidence-grounded，artifact 本身在外圈才改，內圈只交換 review text。LiveCodeBench 上 AR 用 3 個 agent 擊敗 5-agent MARS baseline 拿最高 pass rate；SWE-bench Verified 上 AR 75.2% > MARS 72.6% > Zero-shot 71.6%，但 AR 花的 token 是 Zero-shot 的 **約 4.5×**。作者結論很乾淨：「cooperative code review does not require many agents or complex communication structures — it requires that disagreement be minimal, structured, and evidence-grounded。」

**整篇 paper 是「漸進式構造」，每一步都是對前一步失敗的測量回應，並且每一步都有對應 benchmark 數字支撐：** Zero-shot → Self-Refine → Single-reviewer → Two-reviewers → MARS（5 agents）→ AR（3 agents with R–C inner loop）。在 SWE-PRBench 上 naive AR 反過來變最差：F1 = 0.457（比 Single-reviewer / Two-reviewers / MARS 三個 ~0.50 都低）。作者拉出兩個 case study 找原因——Case A 是「reviewer 拆太多太細、judge 標 fabricated（F1 0.250）」；Case B 是「C yields to R，R 用 weak file-level argument rebuttal 即使錯也贏，看起來 structured disagreement 實際變 false agreement（F1 0.286，而 MARS 在同任務 0.667）」。

**修法只用 prompt 加一行 text constraint（要 R「明確 cite code 來反駁 C，否則保留 C 的 flag」），整個 R+C 結構、agent 數都沒動，F1 從 0.457 拉到 0.533——SWE-PRBench leaderboard 第一：** 這個 1-shot prompt iteration 本身就是論文的核心訊號：「minimal protocol structure + 嚴格 text-bounded evidence rule」就足以讓 3-agent AR 在 review quality 上擊敗 5-agent MARS。三個 benchmark 上 AR 都落在 cost–quality Pareto frontier 上，paper 自承 easy tasks 上 review loop 是 wasted computation，未來要 adaptive trigger。

**對主人的訊號：** 主人6/14跟6/21 都讀過「Orchestrating AI Code Review at Scale」，這篇 AR 是那條線的後續——而且結論跟主人「保守減法、agent 數不要堆疊」硬規則直接對齊。AR 的 inner loop 「freeze artifact + exchange review text only」是 hermes-agent-lite 完全能直接抄的 protocol shape：對應到 horo-agent 的多 agent workflow，不需要新 runtime，只要在現有 dispatch loop 上加一個 R–C inner round + 一條 evidence-grounded text constraint。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Adversarial Review（AR）是一個 3-agent 程式碼審查 protocol：main coding agent + reviewer R + critic C；artifact 在內圈被 freeze，只有 review text 在 R–C 之間往返（cap 5 rounds）；當 review 收斂或通過時才在外圈讓 main agent 改 artifact。protocol 是 inductive 地從 Zero-shot 構造出來，每一步都對前一步的失敗做測量驅動的回應；SWE-PRBench 上一個 single prompt iteration（text constraint）把 naive AR F1 從 0.457 拉到 0.533，跨 3 個 benchmark（LiveCodeBench、SWE-PRBench、SWE-bench Verified）都拿 leaderboard 第一。
- **Why（為什麼重要）**:
  多 agent code review 的主流方向有兩個極端：role-separated team 越疊越多 agent 拿到 diminishing returns；subagent 派工把 agent 當 passive tool 又拿不到合作好處。AR 證明還有第三條路：3 個 agent + 嚴格的 inner-loop 通訊邊界 + 一條 evidence-grounded 反駁規則，就能同時壓下通訊成本並打敗 5-agent MARS。對主人來說這呼應「conservative subtraction」精神：當下 AI 工程界開始堆 agent 數（5→10→20），AR 反過來證明「3 + 結構」勝過「5 + 平行」——這個結論對 hermes-agent-lite / horo-agent 的 multi-agent workflow 設計是直接的 empirical anchor。
- **How（如何運作/實作）**:
  - AR 三件式：main agent（寫 code）+ reviewer R（出 review）+ critic C（用「structured disagreement」審 R 的 review、要求 R 必須用 code 證據反駁 C），review 收斂或 cap 5 rounds 才放行 main agent 改 artifact
  - Naive AR 在 SWE-PRBench 失敗（F1 0.457），failure mode 是「C yields to R」+「over-decomposition」；修法是 prompt 加一行 text constraint（要 R cite code 反駁 C 否則保留 flag），R+C 結構 + agent 數完全沒動，F1 拉到 0.533
  - 跨 3 benchmark：LiveCodeBench AR 最高 pass rate；SWE-PRBench AR with text constraint = 0.533 最高；SWE-bench Verified AR 75.2% > MARS 72.6% > Zero-shot 71.6%；cost 端 AR 是 Zero-shot 的約 4.5×
  - Pareto frontier 全 3 benchmark 都成立，作者明確說 easy tasks review loop 是 wasted computation，未來方向是 adaptive trigger
- **Insight（赫蘿心得）**:
  AR 真正給主人帶走的不是「3 agents 比 5 agents 強」這個爽文結論，而是 **「inner loop 凍結 artifact、只交換 review text」這個 protocol shape**。horo-agent lite 目前 multi-agent dispatch 還沒有 formal inner/outer loop 分界，reviewer 跟 coder 的 message 跟 artifact edit 混在同一個 turn 裡，正是 SWE-PRBench 上「over-decomposition」跟「C yields to R」兩種 failure mode 的溫床。最便宜的 porting 路徑是：在現有 hermes-agent-lite dispatch loop 上定義兩個 ring buffer——inner ring 只允許 reviewer↔critic 來回 review text、cap 5 rounds；outer ring 才允許 main agent 拿 frozen review 去改 artifact。再把 SWE-PRBench 的 text constraint（evidence-grounded 反駁）做成 SKILL.md 裡一條 literal system prompt，整個 3-agent AR 就直接落進 `horo-agent` 而不用碰 agent loop / SSE / session schema 等主人禁止動的核心。論文還藏一個 bonus：AR 是用 Claude Code + Python orchestrator 跑的，已經證明 skill-style 包裝在 production-style coding agent 上有效——主人最近對 SkillEffect、SkillGate 連兩篇都讀，今天 AR 剛好把「skill 化的 protocol」這個 thread 連成第三節，方向一致。