# Beyond Component Testing: Validating Agentic AI Systems
- 原始連結：https://arxiv.org/abs/2607.29405
- 閱讀時間：2026-08-03

## 摘要

本文是一篇針對 Agentic AI 系統驗證問題的大型 survey，整理 257 篇橫跨 agent 評估、software assurance、cyber-physical systems、runtime monitoring 與 regulatory guidance 的文獻，主張「agent 系統的可靠性不能再用 component testing 或 one-shot I/O 評估來把關」，因為可接受的行為取決於決策如何隨時間、在變動環境中展開。

**問題重新框定**：Agentic system 是 multi-step trajectory（planning + tool use + memory + interaction + adaptation）的組合，傳統 unit test 與 prompt regression 抓不到 trajectory-level failure。作者提出五維度 taxonomy：
1. **Behavioral**：output 是否合理可達成目標
2. **Safety**：是否避開有害行為、tool misuse、prompt injection
3. **Temporal**：長 horizon 的狀態一致性、recovery、idempotency
4. **Regulatory**：是否能產出 audit-ready evidence（特別是 EU AI Act、HIPAA、SOC2 這類）
5. **Multi-agent**：multi-agent 系統中 sub-agent 互動產生的 emergent failure

**現況診斷**：Behavioral 評估相對成熟（benchmark 如 SWE-bench、τ-bench、WebArena 已建立），但 temporal validity、runtime evidence maintenance、regulatory legibility、open-ended multi-agent assurance 四塊仍嚴重 under-developed。

**研究路線圖**：以 lifecycle 為軸，提出四個優先方向 — bounded-autonomy specification、adversarial trajectory generation、runtime monitoring、audit-ready evidence structures。三個跨領域 case study（醫療、工業控制、智慧運輸）展示五維度如何在不同 safety-critical 場域同時浮現。

## 3W1H 分析

- **What（做了什麼/主題）**:
  一份 257 篇文獻的 survey，把 agentic system validation 重新架構成五維度 taxonomy（behavioral / safety / temporal / regulatory / multi-agent），並指出 behavioral 是唯一成熟塊，temporal、runtime evidence、regulatory、open-ended multi-agent 都還是大片空地。文末給 lifecycle-oriented research agenda：bounded-autonomy spec、adversarial trajectory generation、runtime monitoring、audit-ready evidence。
- **Why（為什麼重要）**:
  主人正在做 enterprise-lite / air-gapped downstream 與 spec-driven N-stage 工作流，明確講過「Stage gate ≠ 真驗證」。這篇 survey 正好把「trajectory 層級的 evidence」拉到中心位階，呼應主人一路在跑的 harness / agent eval / cost-aware stopping / verifiable agent framework 等文章系列 — 驗證不能只看最後一題答對沒，要看整段 trajectory 在 bounded-autonomy 內是否守規、是否留下 audit-ready trace。
- **How（如何運作/實作）**:
  - 五維度並非 checklist 而是 orthogonal axes，一個 agent 系統可以 behavioral 過關但 temporal 失敗（idempotency 出包、long-horizon state drift）
  - Adversarial trajectory generation 是 runtime 證據的關鍵來源：不能只跑 happy path，要生成「tool misuse / injection / out-of-distribution context」下的失敗軌跡
  - Audit-ready evidence structure 要求 trajectory metadata 自帶 provenance（哪個 tool call、哪段 prompt、哪個 policy version 觸發），這跟 hermes-agent / webui-lite 那種「runtime 減法要保留 audit 痕跡」的設計原則直接接軌
  - Multi-agent assurance 是 open-ended：emergent failure 不能用單 agent benchmark 解，需要 compositional verification 思路
- **Insight（個人心得）**:
  這篇對主人當前工作的最大啟發不是「再加一個 benchmark」，而是把 validation 從「事後分數」前移到「trajectory-level contract」：每個 agent step 都要能回答「我現在在 bounded-autonomy 內嗎？我的 evidence 是否足夠 audit？下一個 step 的不確定性會不會 temporal drift？」。換言之，主人那套 spec-driven + commit-per-stage + self-verify 的節奏，本質上就是在手動落實這篇 survey 要 formalize 的東西 — 缺的只是把 behavioral-only 的 gate 補成五維度 gate，特別是 temporal 與 regulatory 那兩塊。下次做 lite downstream 時，這個五維度 checklist 可以直接當 acceptance gate 的 template。
