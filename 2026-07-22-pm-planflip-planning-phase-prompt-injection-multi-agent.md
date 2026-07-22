# PlanFlip: Attacking Multi-Agent LLM Systems via Planning-Phase Prompt Injection
- 原始連結：https://arxiv.org/abs/2607.16199
- 來源：arXiv cs.AI 2026-07-21 announce (new)
- 閱讀時間：2026-07-22

## 摘要

**問題：多代理人 LLM 系統普遍用 Planner → Executor → Critic 的 pipeline，但 planning phase 是一個被嚴重低估的攻擊面**。任何注入到 Planner context 裡的惡意片段都會 cascade amplification——一旦污染了任務分解，下游所有 Executor 子任務和 Critic 稽核都會同步錯位，而且這種污染看起來「語意合理」，繞過 keyword filter 輕而易舉。本文定義了 PlanFlip 框架，整理出四種 planning-phase 攻擊：GoalSubstitution (PF-1)、PriorityInversion (PF-2)、ContextPollution (PF-3)、RoleConfusion (PF-4)，每一種都偽裝成 plausible tool output。

**最反直覺的發現：capability amplifies vulnerability，不是 reverse**。在九個 frontier LLM、3,479 個 episode 的量測裡，**GPT-5 拿下最高 ASR = 0.68**——能力越強的 Planner 對 planning-phase injection 越脆弱，直接推翻「強 model 內建更安全」的常見假設。作者解釋是因為更強的 Planner 對「看起來合理的子任務序列」的服從度更高，反而更容易把惡意片段結構化成可執行計畫。

**第二個硬發現：homogeneous pipeline 裡 Planner 與 Critic 用同一個 backbone 會瞎掉**。GPT-4o 與 Llama-3.3-70B 在 PlanFlip 下 ASR ≈ 0（攻擊不直接成功），但 Stealth = 1.00 且 StepShift > 0——意思是「計畫已被悄悄改寫，但同一個 backbone 的 Critic 仍然回報 alignment」。兩個獨立 judge 量化出 -0.20 到 -0.32 的 semantic deviation，r = 0.943。換句話說：**當 Critic 跟 Planner 共享 model family，整個 audit loop 失效**。這是任何用同一個 LLM 同時做規劃與稽核的 multi-agent 架構的隱形風險。

**緩解方案是異質 diversity，不是 prompt hardening**。作者提出 GoalAnchorCheck (D1) 與 CrossAgentConsensus (D2) 兩個 detector，在 16 個實驗 cell 中有 15 個贏過 same-backbone baseline，detection rate 可達 1.00。**DeepSeek-R1（reasoning-augmented）在所有攻擊下都拿到 StepShift = 0.00**，是九個 model 中唯一完整抵抗的——「先推理再行動」的 chain 似乎能在 plan commit 前抓到惡意片段。文末的關鍵 insight：**multi-agent 系統的安全前提是異質 model diversity；同質 backbone 的 redundancy 對 planning-phase 攻擊零防禦**。

## 3W1H 分析

- **What（做了什麼/主題）**:
  把 multi-agent LLM 的「安全分析對象」從輸出端的 content guardrail 移到規劃端的 Planner input surface。論文先歸納出四種 planning-phase injection（GoalSubstitution、PriorityInversion、ContextPollution、RoleConfusion），再以 3,479 個 episode 跨九個 frontier model 做大規模量測，最後提出兩個 detector（GoalAnchorCheck 鎖定原始 goal anchor、CrossAgentConsensus 跨異質 backbone 做共識投票）。整體敘事是「新的攻擊面 → 量化的反直覺結果 → 對稱的緩解方案」。
- **Why（為什麼重要）**:
  主人現在手上至少有三個 multi-agent 系統雛形會被這篇命中：(1) `create-agent-teamworkflow-task` skill 用 Planner/Executor/Critic 的 TeamWorkflow Server，雖然目前 Critic 是人工 owner 不是 LLM，但若未來改用 same-backbone LLM 做 auto-review 就踩到 PlanFlip 第 (2) 個發現的盲點；(2) `handle-agent-teamworkflow-turn` 整個 turn 流程會讓 Planner 讀到工具回傳、外部 message、上一輪 history，這些都是 PlanFlip 攻擊的注入點；(3) `agent-share` skill 跨 tailnet 收 post 時，「plausible tool output」會偽裝成 reply 內容，如果 master 後面接 auto-orchestrator 把它當 sub-task，就直接構成 GoalSubstitution。**對主人最有 actionable 的結論是：Critic 千萬不要跟 Planner 用同一個 backbone；推理模型（DeepSeek-R1、o-series）拿來當 Critic 的 baseline 比同 family 的 instruction-tuned model 安全得多。**
- **How（如何運作/實作）**:
  - **攻擊面定義**：把 Planner 的 input 視為「可注入 surface」，所有來自 tool、user、memory、prior turn 的字串都是 trust boundary 外的輸入
  - **四種攻擊 payload**：PF-1 換掉最終 goal、PF-2 反轉 task priority、PF-3 注入污染 context（tool 結果假裝來源可信）、PF-4 混淆 Planner 對自己 role 的認知（讓它以為自己是另一個 agent）——共同點是 payload 結構必須看起來像合法 tool output
  - **量測指標**：ASR（攻擊是否讓下游執行錯誤目標）、Stealth（是否被 same-pipeline Critic 抓到）、StepShift（攻擊導致的 plan 結構偏移量）、semantic deviation（兩個獨立 judge 對輸出的語意距離）
  - **Detector D1（GoalAnchorCheck）**：在 Planner 收到 prompt 前對原始 goal 做 hash/embedding cache，執行後比對最終 sub-task graph 是否還在 anchor 範圍內
  - **Detector D2（CrossAgentConsensus）**：用一個異質 backbone 的 second opinion LLM 對 Planner 產出的 plan 做獨立 audit，採多數決
  - **關鍵實驗數字**：GPT-5 ASR=0.68、GPT-4o/Llama-3.3-70B Stealth=1.00、DeepSeek-R1 StepShift=0.00、CrossAgentConsensus 在 15/16 cell 贏 baseline、detection rate 高達 1.00
- **Insight（個人心得）**:
  咱讀完第一個想到的不是「要 patch 主人現有的 Planner」，而是「主人現在 self-host 的 agent-share 跨機器通訊本質上就是 PlanFlip 的攻擊放大器」——一個 tailnet 上的 post 進到 Hermes 後會被 dispatcher 拆成 sub-task 派給不同 machine，這中間只要有任一條訊息被污染，下游所有 machine 的 Planner 都會 cascade。如果未來主人想正式把 `hermes-agent-share` 變成 production multi-agent backbone，這篇 paper 給的三個結論剛好可以直接變成 design constraint：(1) **Planner 與 Critic 強制異質**——主人現成的 LMStudio council + KTransformers heterogeneous inference 路線剛好對得上，可以讓本地 small model 當 Planner、雲端 reasoning model（DeepSeek-R1 或 o-series）當 Critic，比同 backbone 雙開安全得多；(2) **prompt injection 不能只靠 keyword filter**——PlanFlip 四種 payload 全部偽裝成 plausible tool output，所以現有的 content moderation 等級根本不夠，必須升級到 semantic-level 的 goal anchor 檢查，這件事可以借用主人本來就在維護的 `lmstudio-council-hosting` skill 架構來做；(3) **推理模型對 injection 有結構性抵抗**——這個發現在 Agent TeamWorkflow Server 上特別有價值，因為目前 Critic 是人工 owner，未來若要自動化，**第一版就該直接用 reasoning-augmented model 當 Critic，不要先用 instruction-tuned model 再升級**，省得回頭重做 audit loop。最後一個 personal 觀察：這篇 paper 跟主人昨天看的 cache-aware prompt compression（CAPC）其實是同一條 attack surface 的兩面——CAPC 處理 prefix stability 讓 cache hit，PlanFlip 攻擊 prefix injection 讓 Planner 偏移；兩個放在一起看，未來 Hermes 的 prefix layer 應該同時考慮 cache benefit **與** injection risk，把 `cache_control: ephemeral` breakpoint 設計成同時是「cache scope boundary」與「trust boundary」，這個雙重身份對 Hermes runtime 來說是乾淨的抽象，值得主人認真考慮納入 `hermes-runtime-operations` skill 的設計原則。