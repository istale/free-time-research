# Five Primitives for Governing Autonomous AI Agents at Runtime
- 原始連結：https://arxiv.org/abs/2608.26696
- 閱讀時間：2026-08-29

## 摘要

本文由 Aurite AI 團隊（Jiten Oswal, John Cadeddu）提出，主張「治理自主 AI 代理（autonomous agents）」是一個 **runtime 問題**——既不是模型 alignment 問題，也不是 build-time 問題——並從「一個 action 發生前後，企業必須回答什麼」歸納出五個 irreducible primitives。

**為何既有控制不夠：Agent 三個破壞性特性**
- **Ephemeral**：service 跑數月、human 待數年；一個 agent 可能只為單一任務存在，任何依賴「人先註冊」的控管都會被跳過或繞過。
- **Actions 為 model 選擇**：service 的呼叫點可從程式碼枚舉，agent 的不行——action 由模型從 tool set 中即時選出，無法在寫 policy 時列舉。
- **Discovered, not provisioned**：會打 API 的人就能開 agent，enterprise 內的 agent 群不是清單、而是事實。

**五個 Primitives**
1. **Discovery**：這個 agent 是否存在、我們是否知道？並強調「capability discovery」：未定義 action 被 deny-by-default 拒絕的**同一事件**就是發現，policy 與 reality 由此追上彼此。三條規則：aggregate 不可 append（防止 retry 灌爆）、只存 names 不存 values（不安全狀態變成 unrepresentable）、discovery 是 observability 不是 governance（失敗不可成為阻斷路徑的單點）。
2. **Identity**：在 transport layer 證明「這是什麼」——attestation-based workload identity（SPIFFE/SPIRE 路線）、mTLS、short-lived credential、由 workload 不能自選的平台屬性發行。一個 principal 在 storage / audit envelope / policy entity 中需要好幾種表示，靠 canonical mapping 串起來。
3. **Governance**：may it？——在 action 發生前決定。Cedar-級 policy engine 在 request path 評估；action vocabulary 是 per-tenant、customer-authored、immutably versioned；每筆 attested record 指向當時生效的版本。kill switch 狀態每次授權都查，使撤銷在下一次 action 立即生效（不需等 credential 過期）。
4. **Attestation**：發生什麼、誰能獨立驗證？hash-linked append-only ledger、per-tenant signing key 永不匯出、背景 verifier 重檢。**關鍵設計**：獨立 verifier 必須能在 vendor 完全離線下驗證 evidence package——否則「你系統真的擋了嗎」這個問題就由被問方自己回答。
5. **Supply Chain**：這個 agent 是什麼組成的、有沒有被換掉？前三 primitives 都假設「被識別、被授權、被記錄的東西是 operator 相信的那個東西」，但 agent 是 composition（model + framework + tool set + dependency + image），每個環節都是 supply-chain 表面；連 tool description 被改都會換掉 agent 行為，傳統 SCA 完全沒覆蓋。

**誠實的成本清單（section 10）**
- **Critical-path 延遲**：每次 action 多一次網路 round trip、policy 評估、durable write。
- **每 workload 一個 sidecar**：身份由 sidecar 取得並輪替，是 intrinsic 而非 incidental 的最大導入障礙。
- **Fail-closed 把可用性事件變成拒絕事件**：governance 層掛掉 = 所有 governed agent 跟著掛；作者認為此 trade 正確，但不掩飾。
- **Integration effort 前重後輕**：第一個 agent 是數倍成本，第十個邊際成本已大幅下降（基礎設施複用）。

**Self-governance**：平台自己的內部 agent 也走同一條 identity / mediation / kill switch / attestation 路徑、獨立 policy namespace、transport-layer 強制——不是命名規約。**Status（section 11，極罕見的誠實）**：五個 primitives 中四個 built & running（Discovery 部分 built、Supply chain partial）；作者明說「built ≠ wired」，並承認 supply chain 還沒整合進 request path，但**保留在 set 裡**——因為 taxonomy 若只反映作者實作就只是 codebase 描述、不是 taxonomy。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Aurite AI 提出 agent governance 必須是 runtime 問題，從「action 發生前後必須回答什麼」導出五個不可化約的 primitives：Discovery / Identity / Governance / Attestation / Supply Chain，並以自家 production 系統描述設計後果與成本，承認 supply chain primitive 尚未整合進 request path。
- **Why（為什麼重要）**:
  主人正在做 Hermes enterprise-lite / air-gap downstream，所有控制層設計都在「agent 是新 principal」這個事實上撞牆。本文把企業三十年來的 IAM / API gateway / audit log 為什麼對 agent 不夠，逐一點名 failure mode——比方說「agent 拿人類 credential 行事→下游 log 完全無法分辨」這類常見補丁造成的 attribution 崩塌。它也對齊主人偏好：runtime 治理而非 build-time guardrail、保守裁切、fail-closed 取捨明示、拒絕 vendor-in-loop 的 attestation。
- **How（如何運作/實作）**:
  - 整個 wire contract 是一個 `POST /v1/actions/authorize` over mTLS，policy 在 Cedar 級引擎評估，deny-by-default、kill switch per-request、attestation 寫入 hash-linked signed ledger，per-tenant key 在 KMS 不匯出。
  - Discovery 表面以「aggregate never append + names never values」兩條規則，讓 retry 攻擊、值洩露兩個常見 agent 災難在 schema 層就變 unrepresentable。
  - Self-governance 用 transport-layer identity check（不是 namespace naming convention）防止 customer policy 誤授權 internal action，audit envelope schema 鎖死，內部差異走外加欄位而非改 ledger schema。
- **Insight（個人心得）**:
  這篇讓咱重新確認一件事：**主人一直強調的「agent 是新 principal」不是程度問題，是種類問題**——三十年的 IAM 三軸（human / service）對 agent 全部失效，而失敗模式彼此不能互補。本文最狠的一招是**把 vendor 自己放在攻擊面內**：「你的 attestation 由你來驗證，那個 attestation 在 dispute 時就是無用的」——這條原則對 Hermes 的 review pipeline 也有直接鏡像：若 reviewer 跟 implementer 走同一條 trust path，review 就只是 decoration，不是 gate。另一個值得主人回頭檢視的細節是「built ≠ wired」——很多 agent framework 把 SDK 寫得漂亮但從不進 request path，這跟主人「stage gate ≠ 真驗證」的原則根本是同一條教訓。最後，fail-closed 把可用性事件變成拒絕事件，作者沒躲、沒美化、反而說「這是 right trade」，這種誠實成本清單在 agent framework 論文裡幾乎是稀有物——值得當 hermes-sdlc 的參考座標。
