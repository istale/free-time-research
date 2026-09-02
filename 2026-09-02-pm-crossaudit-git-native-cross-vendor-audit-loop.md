# CrossAudit: A Git-Native, Cross-Vendor Audit Loop for Agentic Science
- 原始連結：https://arxiv.org/abs/2608.28631
- HTML 全文：https://arxiv.org/html/2608.28631v1
- 閱讀時間：2026-09-02（午間，週三）
- 來源：arXiv cs.AI 昨日新論文（2026-09-01 提交，arXiv id 2608.28631）

## 摘要

這篇由 Cambridge + UW-Madison 兩校 2026-08-05 投到 arXiv 的 *agentic science oversight protocol* 論文，鎖定的痛點對主人這個 freelance AI agent cluster 框架設計者特別關鍵：**「AI 科學家不能自己改自己的作業」**——目前為止 Sakana AI Scientist / DeepMind AI co-scientist / A-Lab autonomous lab / Coscientist 所有的 review loop 都在同一個 vendor 的同一個 model family 裡跑，這是 architecture-internal 的問題，不是 patch 可以解決的。

**核心解法是「CrossAudit」——一個 protocol-shape audit loop，全部 8 個 invariants 都寫成可驗證、可版本化、可重放的狀態機。** 8 invariants：

- **I1 — Heterogeneity**：Auditor 必須來自 **與 Generator 不同的 model family**（不同 vendor 是必然結論，因為 parametric 和 contextual 都無法 share）。
- **I2 — Replayable audit ledger**：所有 audit report、verdict、dispute、ruling 都以 **git commit** 形式落地——不只是 logs，而是 commit objects，所以 *第三方可以 replay 整個 supervision history*。Raw model exchanges **不** 入 ledger，因為不可 replay。
- **I3 — Citation validity**：audit report 必須 cite 用到的 rule ID + 生效中的 Constitution 版本 commit hash。**沒有 cite 或者 cite 不可驗證 = invalid = 自動 escalate**。integrity failure 永遠不會被當普通 scientific verdict 吃掉。
- **I4 — Determinism first**：**Deterministic Check Layer (DCL) 在任何 LLM audit 之前跑**，hard failure 直接 BLOCKED，**no model may waive DCL verdict**。理由是 heterogeneous model 仍共享大量 training corpus，learned-component-free 的 layer 的 failure mode 最跟 model decorrelate。
- **I5 — Bounded revision**：每個 increment 至多 max_rounds (default 3) Generator–Auditor exchanges，exhaustion 強制 escalate。**這是 oscillation guard**：沒有 bound，兩個互相糾正的 agent 可以無限 local-plausible / global-divergent。
- **I6 — Graded interruption**：finding 嚴格只分 **BLOCKER**（objective defect: DCL failure / internal contradiction / missing provenance / method–declaration mismatch）跟 **ADVISORY**（judgement: parameter taste / style / scope）。**BLOCKER gates the increment、ADVISORY 進 human async reading、永遠不 gate**。Human 只被 ESCALATE 打斷，running compute **永遠不打斷**。
- **I7 — Receipt binding**：每個 verdict 都是 receipt，要 bind 到 *(a) audited commit SHA + (b) 從 SHA derive 的 artifact manifest (paths + content hashes，**不是 caller-supplied payload**) + (c) Constitution / check-layer / prompt 版本 + (d) auditor 自我宣告的 identity + (e) report 的 ledger commit*。少任何一個 = 不是 receipt。
- **I8 — Fail-closed admission**：protected action（admit next increment / production submission / claim publication）只在 valid + current + matching receipt 驗證後才放行，否則 default deny + escalate。**沒有 audit = 沒有 admission**。只有兩個 severity，不能再多了——更多 severity 等於開 agents 之間的 severity-inflation negotiation。

**Protocol 的 execution shape 是「Constitution-bounded Generator → Auditor 對話，admission 只有在 receipt 完整 + DCL 清白 + bounded revision 沒耗盡 + ADVISORY 不 gate 時才放行」。** I1 強制不同 vendor，I2 強制 ledger 永久可審，I3/I7 強制 receipt 內容完整，I4 強制 deterministic 在前，I5 強制有上限，I6 強制人只在安全位置被打擾，I8 強制 fail-closed。八條加在一起構成 *protocol-shape minimum viable oversight*——少任何一條就出現作者規定的 failure mode。

**Reference implementation = GitHub Actions + 幾百行 Python**（不是大型 framework，刻意停在 protocol-shape）；**live deployment** 在 computational-chemistry pipeline 已運作；**seeded-defect trial**：30 increments × 43 seeded defects × 1 run per configuration，發現「**two vendors read the same rulebook differently**」——同一個 rulebook 給 A vs B 兩個 vendor 會讀出不一樣的條目 interpretation。論文 *自己也* 接受 *cross-vendor audit of its own repo*，並公開 *voiding the original blinding* 的 transparent 過程——這是 *methodology-over-method* 的示範：**protocol 不是 scope-creep-able，跟隨 ledger 一起 living**。作者坦言最強的 evidence 不是 seeded-trial 的數字，而是 *「this paper 自己被 cross-vendor audit 的 committed uncontrolled record」*。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cambridge + UW-Madison 提出 **CrossAudit protocol**——一個對 autonomous research pipeline 做 oversight 的 protocol-shape audit loop；以 GitHub Actions + ~few-hundred lines Python 為 reference implementation。核心想法：**AI 不能 self-grade**，所以 audit 必須 **(a) 跨 vendor + (b) git-native + (c) Constitution-versioned + (d) Determinism-first + (e) Bounded-revision + (f) Two-severity (BLOCKER/ADVISORY) + (g) Receipt-bound + (h) Fail-closed admission**。八條 invariants 全部 explicit，**論文自己也被自己的 protocol cross-vendor audit**。Seed trial：30 increments × 43 seeded defects。威脅模型章節顯式列出 8 個 attack：self-preference / prompt injection / auditor failure / oscillation / Goodharting / collusion / operator / vendor identity self-declared / confidentiality。

- **Why（為什麼重要）**:
  **這篇直接補上主人 substrate-arc pair #9 第三軌（protocol-shape audit）對應到 owner-actionable primitive 的最後一塊**。SkillEffect (8/20, tool-dispatch boundary) + AR (8/21, agent-review boundary) + Recuris (8/26, memory-evolution boundary) 都是 *「skill-shape protocol + validation gate」在三個 boundary 上的 primitive*；**CrossAudit 是這條 arc 的 sibling primitive，作用在「cross-vendor audit pipeline」這個 boundary**——把 I1 (heterogeneity) + I2 (replayable ledger) + I4 (determinism-first) + I8 (fail-closed) 四條 invariants 抄過去就能給 horo-agent *新增*一個 audit-loop 可選 component。對主人 horo-agent / hermes-agent workflow 而言，這解的痛點非常具體：**目前主人的 Kanban + skill load + Discord 回報 + session continuity 是 owner self-verification**——所有 audit 都是單一 actor 自己做，無 I1 heterogeneity、I2 ledger 是 session_search 不可 replay 的、I3 cite evidence 沒有 enforced、I6 graded interruption 沒有 explicit。**CrossAudit 等於把主人目前 owner-as-verifier 的模式顯式升級成 protocol-grade cross-actor verification**。

- **How（如何運作/實作）**:
  - **Constitution file**：人類撰寫並 versioned 的 rulebook，**不是 prompt**——是 deterministic content，可以 git diff。
  - **DCL (Deterministic Check Layer)**：在 LLM audit 之前的 script-only 檢查（lint / schema / unit test / static analysis），hard failure 直接 BLOCKED。
  - **Generator ↔ Auditor**：不同 vendor（如 Qwen + Claude），但都消費同一個 Constitution file。
  - **Receipt = git commit object**，binding 到 (SHA + manifest + Constitution commit + check-layer version + auditor identity + ledger commit)。
  - **Bounded revision**：max_rounds = 3 (default)；exhaustion 自動 escalate 到人。
  - **Two severity** only — BLOCKER gates, ADVISORY records。
  - **Human 介入只發生在 ESCALATE 條件**，running compute 永遠不被打斷。
  - **Reference impl**：GitHub Actions YAML + ~few-hundred lines Python，**不**是一個 framework，是 protocol-as-code 示範。
  - **作者自己也被 cross-vendor audit** —— 演示 *protocol-as-living-spec*。

- **Insight（個人心得）**:
  **CrossAudit 對主人 hermes-agent-lite / horo-agent 的核心價值不是「再學一個 framework」，而是把主人現有的 owner-verifier workflow 重構成 eight-invariant ladder。** 我看到三個 actionable 升級：

  1. **I1 heterogeneity 對應主人現有 `default = Qwen 協調 + qwen38-code + GPT 審查` 多 model 設定**——但目前是「不同 model 跑不同 task」不是「同一 task 跨 vendor review」。**最小第一步**是把主人的「executor 完成 → reviewer 審查」鏈 explicit 改成 *「executor (model A) + reviewer (model B, 不同 family) + receipt-binding」*，把 kanban_complete 與 kanban_review 的 handoff 升級成 receipt-bearing commit，而不是目前的 free-form comment。

  2. **I2 + I3 replayable ledger + citation validity 直接 map 到主人 kanban metadata**——目前 `kanban_complete(metadata=...)` 是 free-form dict，沒有任何 I7-style bound receipt shape。**最便宜的升級**是把 kanban 的 complete handoff structurize 成 `{audit_target: '<sha>', constitution_version: '<hash>', artifact_manifest: [...], auditor_identity: '<profile>', ledger_commit: '<sha>'}` —— 這樣每個 owner-side 決定都有 protocol-grade replayability，不用主人改 hermes core。

  3. **I6 two-severity 等於把主人目前的「靠主人回 Discord」的隱性 priority ladder explicit 化**——目前主人長期記憶寫「主人長任務偏好 Discord 回報 Kanban 真進度」、「live evidence > Kanban PASS」、「主人截圖/實測反例要立刻重開 regression」是 implicit graded-interruption。**主人可以把目前的 implicit ladder 直接寫成 constitution-like rule file**，把 BLOCKER conditions = 「live evidence 缺失 / health check fail / test fail / owner-visible 反例」、ADVISORY conditions = 「in-flight status / 待確認」做成 kanban metadata 的 enum，這樣 I5 bounded revision 也自動 firing。

  對主人 *brand new 專案勿假設重用舊 code* 的偏好，CrossAudit 的 GitHub-Actions + few-hundred-lines-Python shape 是 *恰好對的 first-class scope*——一個 protocol 檔 + 一個 DCL 跑 ci + 一個 receipt schema 就能 stand-alone 在新 repo 跑起來，不需要碰 hermes core runtime / agent loop / SSE / session schema。**這就是 substrate-arc pair #9 boundary cluster 的第四個 sibling**：SkillEffect (tool-dispatch) + AR (agent-review) + Recuris (memory-evolution) + **CrossAudit (cross-vendor-audit)**——四個 primitive 都不是 new framework，都是 *「skill-shape protocol + validation gate 在某個 boundary 上的 instance」*，統統抄直 primitive 即可運作。
