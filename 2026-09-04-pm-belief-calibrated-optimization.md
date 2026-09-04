# Belief-Calibrated Optimization: An Explicit World Model for Agentic Optimization
- 原始連結: https://arxiv.org/abs/2609.01861
- 閱讀時間: 2026-09-04（午間）
- 來源: arXiv cs.AI 昨日新論文（cs.AI 09-03 announce, RSS 命中 #9，288 篇今日更新）

## 摘要

**Scaffold-as-substrate primitive 的少見可移植樣本。** BCO 把「coding agent 改 scaffold」這件事從 coding-agent 內部的隱性 reasoning（每輪 call 才決定「這次為什麼改、改哪裡」）重構成「一份 persistent in-context document 作為 world model」——後續 call 看 scores + traces 也看 document，因此「為什麼改、改哪裡」這個 belief 變成可被讀、可被寫、可被下個 candidate 沿用的 substrate。這條 primitive 與主人 09-04 AM pick armature「coding agent as tool recommender」剛好對偶：AM 是 *audit 哪個 tool 會被 picked*，PM 是 *audit 哪個 scaffold edit 應該被 commit*，兩者都是把 coding agent 的「隱性 decision」變成可審計的 substrate。

**Methodology 比結論更值錢。** BCO 的方法論設計非常乾淨，給主人 enterprise-lite 銷售場合可直接拿來當 scaffolding case study：（a）matched control（唯一差別是有無 world model）、（b）held-out split（train passrate 比較用的 split 不是用來選 candidate 的 split）、（c）target-model swap（frozen model 換掉、scaffold 不動，量 BCO scaffold 是否仍然 lead）、（d）offline ablation（fresh predictor 拿 document / 拿 falsified-content 對照，驗證「document carries information in its content, not only in its form」）。四個實驗設計是 hermes-agent-lite / horo-agent 內部實驗的標準模板，比「在 5 個 benchmark 達到 SOTA」本身更有長期 reference value。

**Empirical envelope 紮實。** 5 個 benchmark（memory QA / tool-use QA / code-as-action app agents / terminal agents）含 held-out + target-model swap，例外只有「context-window overruns leave it unfinished」一條誠實披露——沒有 best-of-N、沒有 judge-model dependence、沒有 unusually large budgets，整個 envelope 是 *matched comparison on standard splits*。對主人 horo-agent 的 enterprise-lite demo 是可直接 reproduce 的最簡形式：5 task 集合、matched control、held-out split、target-model swap。

**Cross-tick substrate-arc continuity。** 8/20 PM SkillEffect 5-piece plugin contract（tool-dispatch boundary）+ 8/21 PM AR R–C inner loop（agent-review boundary）+ 8/26 PM Recuris 5-component loop（memory-evolution boundary）+ 09-04 AM armature scaffold-audit + **今天 PM BCO = scaffold-as-substrate with persistent world-model boundary**，構成 5 個 same-pattern primitive 散佈在 4 個不同的 boundaries + 1 個 process boundary。對主人 horo-agent lite 下游：每一個 primitive 都 *獨立 implementable*、都 *不需要改 core runtime*（agent loop / SSE / session schema）、都 *不需要 fork hermes-agent*——可以直接抄到 `horo-agent/primitives/` 目錄作為 Layer 0/1 plugin。

## 3W1H 分析

**What（做了什麼/主題）:**
BCO 是一個把「coding agent as optimizer」這個常見做法升級成「coding agent with persistent in-context world model」的方法。標準做法是 coding agent 每輪讀 score + trace 後自行 reasoning 出 belief（「這次是因為 X 所以失敗」、「改 Y 應該會 help」），然後編輯 source 產出新 candidate；BCO 把那個 belief 寫下來成 persistent document，並隨著新 candidate 評估不停修訂這份 document。document 內容 = 對「environment 會怎麼回應 edit」的當前理解（world model）。把它加入一個 otherwise standard loop 之後，BCO 在 5 個 benchmark（memory QA / tool-use QA / code-as-action app agents / terminal agents）達到比 matched control 更高的 train passrate。

**Why（為什麼重要）:**
主人 `horo-agent` 企業版的「audit-oracle」目前是 AM 09-04 armature 揭露的 *post-hoc recommender audit*（audit 已發生的 tool choice），而 BCO 給的是 *per-edit causal audit*（audit scaffolding change 的理由是否站得住）——兩者互補，是「agent decision 可審計性」這個 enterprise-lite positioning 的完整閉環。更重要的是，BCO 的方法論設計本身（matched control + held-out split + target-model swap + offline ablation）是 hermes-agent-lite 內部 benchmark pipeline 的 *標準模板*，可直接抄作 `hermes-agent-lite/evals/primitive_audit_template.md`，把現有 eval pipeline 升級成「decision-evidenced eval」而不是「metric-only eval」。

**How（如何運作/實作）:**
實作核心是 4 個 mechanism：（1）**persistent in-context document** = coding agent 的 world model，每輪迭代寫入與修訂，作為下輪的 substrate；（2）**matched control loop** = 唯一差別是沒有 document 的 baseline，用來 isolate world model 的因果貢獻；（3）**held-out split** = train passrate 比較用的 split 與 candidate selection split 隔離，避免 leak；（4）**target-model swap** = frozen model 換掉、scaffold 不動，驗證 BCO scaffold 對 frozen model 的 generalization；（5）**offline ablation** = fresh predictor 拿 document / 拿 falsified-content 對照，驗證 document *content* 是否真的有 information（form-only control）。整個 envelope 在 5 個 benchmark 上跑，excluded context-window overrun 案例誠實揭露。

**Insight（個人心得):**
BCO 的真正 actionable primitive 不是「寫個 document 讓 agent 看」這麼淺——而是 *「world model = 第四種 substrate」（input / output / scaffold / world model）* 這個分類學。對主人 horo-agent lite 來說，這意味著現有的 hermes-agent-lite substrate audit 從「input/output/scaffold 三層」升級到「四層」，而 world-model layer 是 *唯一可被「寫下來 + 被下個 call 讀」* 的 layer—— input 是 prompt-side 不可寫、output 是 response-side 不可回頭寫、scaffold 是程式碼層可寫但 compile-cycle 不可熱修，唯有 world model 是 *in-context persistent*，跨 call 跨 candidate 跨 model swap 都還在。**最便宜實作是 Layer 0 text rule + Layer 1 shell script：** 把 BCO 的「persistent world model document」寫成 `horo-agent/primitives/world_model_log.md`（格式：每輪一個 entry，含 belief / predicted response / actual response / delta），然後在 `hermes-agent-lite/runner.py` 的 optimizer loop 每輪把上一輪的 `world_model_log.md` inject 到 coding agent 的 context。第一個 enterprise demo 就能拿「scaffold + world model」雙層 audit 講古，比 09-04 AM 單層 audit 更難反駁。
