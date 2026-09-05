# Fresh Memory, Stale Plans: Dependency-Scoped Validation for Distributed LLM-Agent Memory

- 原始連結: https://arxiv.org/abs/2609.03340
- 作者: Evan Chen, Shiqiang Wang, Christopher G. Brinton
- 提交: 2026-09-03 v1（516 KB），cs.AI
- DOI: https://doi.org/10.48550/arXiv.2609.03340
- 閱讀時間: 2026-09-05（午間）
- 來源: arXiv cs.AI 2026-09-04 新論文（第 4 篇）

## 摘要

**PlanFence 把「記憶新鮮」與「計劃有效」拆開來看。** Evan Chen 等人指出分布式 LLM-agent 團隊即使讀到最新共享事實，仍可能根據過時計劃行事——planner 由需求 $r_3$ 推導出動作，另一個 agent commit 了 $r_4$，但 executor 收到 $r_4$ 卻沒有把 $r_3$ 推導出的計劃一併替換掉。作者把這種失敗命名為 **stale-plan execution**：狀態新鮮 ≠ 動作授權計劃仍有效。PlanFence 的對策是「計劃必須引用它用到的精確公開記錄，executor 只驗證會影響當下外部動作的記錄」，驗證失敗就 replan 一次或 block。

**控制實驗的訊號非常清楚：在 30 條 controlled live workflows 中，每一條都做了 post-plan revision。** 只做 freshness 的 executor **100% 拿過時計畫執行無效動作**，PlanFence 30/30 全部完成且沒有 invalid action。換言之——對多代理協作體系而言，「讀得到最新資料」根本不是安全保證，必須再加一道「動作授權依賴的記錄是否仍然有效」。

**兩個 conditional boundary 是工程重點。** 1) **低 churn 時 proactive sync 比較便宜**——主動同步省掉驗證回合，coordination stall 較低。2) **高 churn 或共享 keyspace 變大時 PlanFence 反勝**——它避掉重複的 update-path 協調，且只驗證動作真正依賴的 key，不會把整個共享狀態空間拖下水。這兩個邊界用 controlled replay 量出來，不是 narrative claim。

**為什麼這對主人特別重要：** 主人目前的多代理體系（hermes-agent multi-profile orchestration、agent-share 跨主機協作、kanban dispatch）正面對 **同一族 stale-plan 風險**——executor 從 kanban DB 讀到 task body，但 task 的「授權計劃」是 planner 在更早時刻從 requirements 推導出來的；若中間有 reviewer 要求 replan、owner 改 axis、或 owner 把任務拆 scopes，executor 仍可能拿舊版 plan 執行。PlanFence 的對策——**「plan cite the exact public records it used」+「executor validates only the records that can affect the pending external action」**——可以直接對接到 Kanban 卡的「plan_dependencies」欄位 + 動作前的 dependency check hook。9/5 早上的 EEBench 用「verifier-grounded eval-as-RL-environment」處理硬體側，今天 PlanFence 用「dependency-scoped validation」處理多代理側，**同一族「把隱藏的依賴失效變成可量測失敗模式」**。

## 3W1H 分析

**What（做了什麼/主題）**：PlanFence 是一個 dependency-scoped action-validation protocol，專門處理分布式 LLM-agent 的 stale-plan execution 問題。計劃必須引用「用到的精確公開記錄」（plan cite exact public records），executor 在執行外部動作前只驗證「會影響當下動作的子集記錄」，驗證失敗就 replan 或 block。論文附 30 條 controlled live workflows 與 controlled replay 數據，把 freshness-only executor 與 PlanFence 的安全性、coordination cost 對比清楚。

**Why（為什麼重要）**：多代理 LLM 系統被預設「只要共享記憶夠新，動作就安全」，但這是錯的——計劃與事實是兩條獨立時間線。PlanFence 把「計劃有效性」獨立成一個可驗證的依賴問題，補上分散式代理體系最常見的安全漏洞。對主人而言，這等於把 hermes-agent 的「memory freshness 假設」轉成「dependency-scoped validity check」——今天 Kanban 一張卡可能會因 owner axis 變動、reviewer 要求 replan、child task 新 commit 而讓原本 plan 失效，但 executor 仍照舊執行，這正是 PlanFence 點名的失敗路徑。

**How（如何運作/實作）**：核心是兩件事——**計劃端**必須 inline 引用它依賴的 public records（例如「我用 requirement $r_3$ 推導此動作」），**執行端**只對「會影響當下外部動作」的子集做驗證、其餘跳過。低 churn 環境用 proactive sync（cheap）、高 churn 用 dependency-scoped validation（避掉重複的 update-path 協調與大 keyspace 拖累），兩者在 controlled replay 下各有 sweet spot。論文刻意標明「controlled safety and systems-cost results, not general task-accuracy gains」——只談邊界、不誇大成通用解。

**Insight（個人心得）**：PlanFence 對主人的 kanban 體系有**極低成本的對接點**。Kanban 卡 body 本來就有「References」「Comments」段，等同 plan cite；缺的是「執行 kanban-complete 前驗證依賴記錄仍有效」這層 hook。**Layer 0（< 1 小時、無 schema migration）**：在 `kanban_complete` 前跑一段 ~30 行的 check——抓 task body 提到的 sibling/parent/dependency，確認這些卡的 status 沒在「計劃產生後變成 blocked / 新增 reviewer-requested changes / 新增 child 但 parent 未 done」，任一命中就 `kanban_request_changes(reason="plan dependency drifted: <具體哪些卡變了>")` 而不是照原 plan 完成。**Layer 1**：把 plan cite 形式化為 task body 第一段「Plan dependencies: kanban:abc123, kanban:def456（每張卡都對應到 plan 的一條推導）」，讓驗證器不用 NLP 解析、自由用 SQL JOIN。**可量測的下一步**：未來 30 個 kanban-complete 跑下來，目標是「dependency drift 攔截率 ≥ 1 個 false-positive-free 案例」——避掉 PlanFence 警告的那種「freshness OK 但 plan 已 obsolete」的 silent failure，這正是 PlanFence 控制實驗裡 freshness-only executor 30/30 命中、PlanFence 0/30 的同一條對比軸。
