# IBIB: A Protocol for Measuring Enterprise AI Systems by Serving Route, Not Model Identifier

- 原始連結: https://arxiv.org/abs/2609.10494
- PDF: https://arxiv.org/pdf/2609.10494
- 閱讀時間: 2026-09-10（午間）
- 來源: arXiv cs.AI 昨日新論文（2026-09-09 17:31:29Z，今日 9/10 weekday 11:30 台北時間 11:30 cron；昨日 cs.AI 107 篇中篩選；本篇從 12-candidate 評比中勝出）
- 作者群: Blake Stenstrom, Charangan Vasantharajan, Brian Sathianathan
- 分類: cs.CL（主）/ cs.AI / cs.LG
- 篇幅: 42 pages, 4 figures
- 與同日 AM 關係: AM = Quesma Qwen3.8 27B 量化基準（inference / cost-of-token substrate）; 本篇 = audit-methodology reliability（measure / verdict 來源 substrate）—— 與 9 月初 audit family 形成 4th-tier 同族 evidence layer
- 與近 5 日 PM 同族 reference: 9/02 CrossAudit / 9/06 Clean Engineering Unstable Measurement / 9/08 Trusting-Trust / 9/09 Audit Construction — 四篇都是「audit / verifier / measurement substrate」家族; 本篇是「**measurement 單位應該是 serving route 不是 model identifier**」的 enterprise-scale 確認, 把 9/06 的「model name not a frozen instrument」一階 + 9/09 的「audit construction envelope」二階升到 **「system not checkpoint」三階 enterprise frame**

## 摘要

**這篇是「Enterprises deploy systems, not checkpoints」——LLM capability 不是 model identifier 的 property,是 serving route (weights + precision + output contract + harness + tool-call parser) 五元組的 joint function。** Stenstrom 等提出 IB2 protocol, 對 11 個 enterprise AI 系統跑 128 locked tasks × 987 assertions (document / spreadsheet / chart / tool / database 五種工作), 三個 protocol primitive: **(a) gold-blind capability-binding preflight** — 路由必須先證明能 execute evaluation contract 才放任務進去, **(b) reliability-inclusive first-pass scoring** — failure 留分母、不支援的 capability 出分子, **(c) structurally score-blind adjudication** — 評審結構看不到分數。

**核心結果 1 — Capability availability 是可量測的,但不是 identifier-level 的 property:** 同一份 weights 兩個 single-route runs, **兩個後來都 fail 掉 finalized binding gate 的不同 predicate**, 第三個 run 在 fresh 狀態 pass 同一個 gate。**advertised identifier 兩個 limit 都暴露不出來**。換句話說: 你看到 model name X 拿到分數 80, 完全不能推論「X 在你公司能不能用」, 因為 route 的狀態會漂移, 而漂移方向不在 model name 的 label 裡。

**核心結果 2 — Discrimination 不是 uniform,ranks 是被 saturate 掉的可疑 artifact:** 七個 suite 中有四個在六系統帶下 saturate, 真正的 spread 幾乎全部來自「governed database work + multi-tab joins」。作者據此**不報 ranks,改報 interval-backed resolution groups**; nominal five-label output 的四個 cut 中,兩個 fail multiplicity adjustment —— 跟 9/09 audit 36 contrasts 全部不顯著的 preregistered null **同一個 fail mode**, 只是本篇把它拉到 enterprise benchmark 上量化。

**核心結果 3 — Serving-arm choice 把同一個 declared revision + precision 從 77.38 拉到 82.54, paired interval [0.11, 10.60]** —— 結論: **route 換了,同一份 checkpoint 報出來的數字可以差 5 分以上**。但作者誠實標出: 兩個 arm 差在 access mode / harness generation / serving tool-call parser; harness generation 是 **evaluator 的 property, 不是 endpoint 的 property** —— 這一條 disclaimer 跟 9/09 「audit 比 demographics 還活」是同一個深層 primitive: **measurement 結果是 instrument 的構造, 不是 instrument 試圖測的對象的 property**。

**核心結果 4 — Reliability inclusion 改變 conclusion, 不是 wording:** 把 failed responses 從分母拿掉, point ordering 翻轉。**這條直接打臉所有 benchmark 的「成功率排行」——分母定義本身就是 verdict**。 跟 9/09 的「first-position-bonus」、9/06 的「snapshot-identity drift」三篇合在一起讀, audit verdict 的不穩定性 = endpoint 抖 (noise floor) + construction 變 (signal source) + reliability 分母變 (scoring rule)。

**對主人 enterprise-lite + air-gapped 下游最 actionable 的 primitive: 把 measurement 單位從 model identifier 升到 (weights + serving snapshot + precision + output contract + harness) 五元組** —— 這條 frame 應該直接寫進 horo-agent 的 reviewer profile 配置, 讓任何 reviewer verdict 都要附「serving route hash + harness generation hash + reliability denominator rule」三項才合規, 而不只是 model name + serving snapshot 二元組。

## 3W1H 分析

**What（做了什麼/主題）:**
Stenstrom 等提出 IB2 protocol, 把 LLM capability measurement 從「advertised model identifier」移到「serving route 五元組」, 跨 11 個 enterprise 系統 × 128 locked tasks × 987 assertions 跑 reference instantiation, 公開 algorithm / classification table / request contract / manifest schema 四項 artifact, 但 reference corpus 保持 sealed —— 論文明確寫:「the procedure is the artifact, not the corpus」。四個 headline results: capability availability 在 identifier 層不可推論; 七個 suite 中四個 saturate, 真正 spread 在 governed DB + multi-tab joins; serving-arm choice 把同 checkpoint 從 77.38 推到 82.54 但 paired interval [0.11, 10.60]; reliability inclusion 翻轉 point ordering。

**Why（為什麼重要）:**
主人目前「`chatgpt-reviewer` profile」+「executor → 完成確認」的 audit chain, **正處在這篇 paper 直接打臉的高風險區** —— 因為主人 review prompt 結構跨任務 (executor agreement / spec conformance / diff score) 有顯著差異, 而 reviewer verdict 報上來時只有「GPT 系列 + 某 snapshot」這種 identifier 級資訊, 沒有 (weights + serving snapshot + precision + output contract + harness) 五元組的任何一條。**IBIB 證明: 即使你能寫出 model name X, 也完全不能推論 X 在你 enterprise stack 跑出來的 score 是 80 還是 77 —— 因為 route 在你看不到的地方漂**。更精準地說, 主人 kanban reviewer profile 的 verdict 是 「(model identifier) × (prompt structure) × (serving snapshot) × (output parser)」四元組的 joint function, 卻只報了第一項的 hash。論文最後一條 result 「reliability inclusion changes a conclusion, not its wording」直接打臉主人現在的 audit log 結構 —— 主人 audit log 只記 verdict pass/fail, 沒記 reliability denominator rule (failed responses 算不算分母), 所以同一份 verdict 在不同 reliability rule 下可能完全相反。

**How（如何運作/實作）:**
- **IB2 protocol 三層 primitive:** (a) gold-blind capability-binding preflight (路由先 prove contract capability 才放任務, **避免「endpoint 換了還以為是同模型」這種 silent drift**); (b) reliability-inclusive first-pass scoring (failure 留分母、不支援 capability 出分子); (c) structurally score-blind adjudication (評審結構看不到 score, **避免 review prompt 結構本身就是 verdict**—— 跟 9/09「audit construction envelope」同源)
- **Serving route 五元組:** weights + serving snapshot + precision + output contract + harness —— 主人 kanban reviewer profile 現在只 pin 第一項 + 第二項 (model name + serving snapshot), 完全漏掉 precision (quantization) / output contract (tool-call schema) / harness (reviewer prompt 結構) 三項。**這三項 missing 的後果就是 9/06 + 9/09 兩篇已經預警過的「同一個 model 不同 audit 給出完全相反 verdict」**
- **Manifest schema 的工程落地:** 論文 release 四項 artifact (algorithm / classification table / request contract / manifest schema), **主人最 actionable 的是 manifest schema —— 把五元組 hash 寫進 kanban comment + Discord 回報, 任何 hash drift 都自動拒絕 merge reviewer verdict**; 這條 primitive 對齊 9/06「snapshot-identity 四元組」、9/09「construction-pinned envelope」、今天本篇「serving-route 五元組」, 應該合併成 reviewer-config YAML < 100 行

**Insight（個人心得）:**
今天這篇是 **9/06 + 9/09 的 enterprise-scale 確認 + 9/02 CrossAudit 的方法論 closure** —— 9/06 證明「同一個 LLM judge 在不同 snapshot 下讀不回同一份東西」(一階 substrate noise), 9/09 證明「同一個 LLM judge 在不同 audit construction 下讀回完全相反 verdict」(二階 substrate signal), **本篇證明「同一個 model identifier 在不同 enterprise serving route 下報出 77 vs 82 的分數, paired interval [0.11, 10.60], advertised identifier 暴露不出這個 spread」(三階 substrate drift)**。 三篇要一起讀才能完整理解主人 kanban reviewer profile 的 audit substrate —— 一個是 endpoint 抖 (substrate noise floor), 一個是 audit construction 變 (substrate signal source), 一個是 serving route 換 (substrate drift source)。 **對主人 horo-agent lite 下游 + air-gapped 最 actionable 的 primitive 就是 reviewer 五元組 manifest hash**: 把 (weights + serving snapshot + precision + output contract + harness) 五項 hash 跟 review verdict 同位 pin 在 Kanban comment + audit log, 任何 hash drift 都自動拒絕 merge reviewer verdict 到 main, 理由是本篇 11 系統 × 128 tasks × 987 assertions 證明: **二元組 (model name + snapshot) 還不夠, 五元組才是 audit verdict 的真實錨點**。 具體可量測的下一步: 在 `~/.hermes/agents/` 下擴充現有 reviewer-config YAML, 加 `serving_route_hash` + `harness_generation_hash` + `reliability_denominator_rule` 三欄位 (跟既有的 `model_snapshot` + `audit_construction_hash` 同位), 每次 review 完成時把五項 hash 寫進 Kanban comment + Discord 回報, 任何 hash 漂移都自動 reject reviewer verdict。 這條 primitive 跟 8/4 armature (只驗輸出不驗 instrument)、9/06 snapshot-identity (驗 instrument 不驗 construction)、9/09 construction envelope (驗 construction 不驗 serving route) 合在一起, 形成 **完整 audit-anchor 五元組**: weights + serving snapshot + precision + output contract + harness, 主人 kanban reviewer profile 從 primitive-3 升級到 primitive-5 元化才算追上 9 月初這四篇的 headline finding。 **真正最尖銳的 takeaway**: 主人過去一直以為「model identifier 是 measurement 單位」是合理的工程實作, IBIB 用 11 系統 × 128 tasks 量化證明這是 enterprise 級 measurement error —— horo-agent reviewer profile 應該從今天起拒絕任何「只標 model name 不標 serving route」的 verdict, 因為這條 verdict 的 reliability interval 已經寬到 [0.11, 10.60], 比整個 9 月份 audit family 的 noise budget 還大。
