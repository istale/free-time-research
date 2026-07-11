# From Prompts to Contracts: Harness Engineering for Auditable Enterprise LLM Agents
- 原始連結：https://arxiv.org/abs/2607.08028
- 完整 PDF：https://arxiv.org/pdf/2607.08028
- 閱讀時間：2026-07-11（午間）
- 來源：arXiv cs.AI（2026-07-09 提交，作者：Joongho Ahn、 Moonsoo Kim，AI Leadership Research Center / CEO AI）

## 摘要

這篇論文的標題是「From Prompts to Contracts」——名字本身就是論點：**enterprise LLM agent 從 prototype 走到 production 的關鍵不是更好的 prompt，而是把行為從 prompt 搬進 code-owned contracts**。論文提出一套「harness engineering」方法論，把 prompt-dominant 原型重建為可追溯、可審計的 LLM agent 架構，並以韓國五大企業集團（25 上市公司、113 條 source-backed claim）作為受控實例。

**核心主張：deterministic 行為歸 code，語意行為歸 LLM**

論文把 prompt-dominant prototype 的失敗模式列得很清楚：source boundary、entity routing、answer contract、reproducible trace 這四件事，「**prompt 寫得出來但 enforce 不出來**」。於是方法論提出一個「replaceable composition boundary」：所有決定性邏輯（source eligibility、entity routing、claim selection、output hygiene、trace generation）改用 manifests、schemas、validators 寫成 code artifacts，**LLM 只剩「語言組裝」這一件事可做**——而且這個 boundary 是 replaceable 的，可以換模型而不破壞契約。這跟主人前幾天讀的 Harness Effect（§4.4 把 Hermes Agent 放上 comparison table）有同源血脈，但這篇更進一步：不是「**harness design 影響 token economics**」（Harness Effect），而是「**harness design 影響能不能 certify output 是合規的**」。

**Source-to-claim pipeline：把 RAG 從「塞文件」變成「簽名制度」**

論文最關鍵的設計是 source-to-claim 層。Raw document → source manifest（corkscrew gate，帶 scope、URL、status、runtime policy）→ evidence record（hash、locator）→ claim candidate → **source-backed claim**（atomic statement tied to provenance、entity-scoped、runtime use policy、verification state）。**Promotion 是 admission boundary**——一句話能不能進到 runtime context，是 code layer 決定的，不是 retrieval 也不是 model。Wiki 仍是 context，但不是 authority。這條設計跟主人 Hermes memory 設計裡「**哪條 memory 可以被 agent 拿來用**」的問題，**完全是同一條架構問題的不同包裝**。

**三條研究問題 + 三個實驗設計，回答「code-owned guarantee 是不是 load-bearing」**

RQ1：固定 validation scenario，harness 能不能守住 source-grounding、entity-routing、trace、output-hygiene、recommendation-language 五個 contract？答：守住；fault-injection 控制組確認 validators 對故意破壞的 contract **會抓到**（不會無感）。
RQ2：把 composition boundary 的 model 換掉（跨 3 個 hosted model、270 次 run），guarantee 還在不在？答：在——失敗只發生在 model-composed side 而且會被 trace 記下來。
RQ3：**最關鍵的 ablation**——鎖住 model，只把 enforcement layer 換成 prompt-only 或 bolt-on external guardrail，會發生什麼？答：prompt-only 讓 recommendation-language 違規 + 內部 trace 洩漏跑到讀者面前；bolt-on external guardrail 雖然擋住違規但 over-refuse（utility 從 120/120 掉到 88/120）；**只有 code-owned enforcement 同時守住 safety 和 utility**。這條結論是 paper 的真正 headline：**code-owned guarantee 不是 prompt 能 replicate 的**，這對所有「**靠 prompt 工程試圖替代架構**」的人來說是最尖銳的反駁。

**跟其他 orchestration framework 的關係**

論文在 §2 明確把 LangChain Agents / LangGraph / AutoGen / CrewAI 放在「orchestration 框架」這層——它們負責「**怎麼組、怎麼跑 agent**」；本文的方法論是「**每個產出的答案有沒有守住 source、routing、output contract**」。**這條 orthogonal 切分法**讓 contracts 不綁任何特定 orchestration runtime——同一套 contract 可以套在 AutoGen、LangGraph、CrewAI、或自己寫的 agent loop 上。對主人做 Hermes 各 profile 的啟示：orchestration runtime 可以因 profile 而異，但**contract layer 應該 profile-agnostic**——這讓「profile 之間 contract 怎麼對齊」變成可被系統驗證的事。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Ahn 與 Kim 提出「harness engineering for auditable enterprise LLM agents」方法論：把 prompt-dominant 原型的 deterministic 行為（source gate、entity routing、claim selection、output hygiene、trace generation）從 prompt 搬到 code-owned artifacts（manifests、schemas、validators），LLM 只在 replaceable composition boundary 上做語言組裝；用韓國五大集團（25 公司、113 source-backed claims）做受控實例，三條 RQ 分別驗證「contract preservation（RQ1）」「cross-model guarantee（RQ2）」「code-owned guarantee 是 load-bearing 不是 prompt 可 replicate 的（RQ3）」。**RQ3 的 ablation 是 paper 的尖峰**：prompt-only 讓違規洩漏、bolt-on guardrail 讓 utility 掉到 88/120，只有 code-owned enforcement 同時守住 120/120 utility + 0 違規。

- **Why（為什麼重要）**:
  對主人這條技術軸線的訊號有三層同時成立：
  (1) **直接命中主人前幾天讀的 Harness Effect 主軸**——Harness Effect 證明「**harness design 決定 enterprise agent 的 token economics**」；這篇證明「**harness design 決定 enterprise agent 能不能 certify 合規**」。兩篇一起讀才看得到「harness」這個詞在 2026 年的真正重量：它**既是經濟問題也是合規問題**。
  (2) **RQ3 的 ablation 是對「**靠 prompt 工程替代架構**」那條路線的最尖銳公開反駁**——主人 Hermes 框架裡 SOUL.md、AGENTS.md、skill prompt 都是 prompt-based 治理，這篇 paper 直接告訴你「**prompt-only 在 enterprise 合規場景守不住**」。這是主人下一階段 framework reliability 工作的**最有用的單一論文級 reference**。
  (3) **「orchestration runtime ≠ contract layer」這個 orthogonal 切分**對主人做 multi-profile Hermes 有直接 method 意義——AutoGen / LangGraph / CrewAI / Hermes runtime 之間可以互相替換，**但 source-to-claim 層應該 profile-agnostic 且統一驗證**。這條思路如果赫蘿整理進 skill，可以讓主人未來 profile 演進時不必「**整個重做 contract 設計**」。

- **How（如何運作/實作）**:
  - **Source-to-claim pipeline**：raw document → manifest（scope + URL + status + runtime policy + provenance）→ evidence record（hash + locator）→ claim candidate → **source-backed claim**（atomic + provenance-bound + entity-scoped + runtime use policy）。**Promotion 是 admission boundary**：code 層決定一句話能不能進 runtime context。
  - **Replaceable composition boundary**：LLM 只負責 phrasing，所有決定性規則（source eligibility、claim selection、answer structure、follow-up filtering、trace generation）都在 code-owned 控制層；換 model 不破壞 contract。
  - **Two outputs per query**：reader-facing answer + audit trace（記錄 routing、source states、claims、validation results）——**trace 不是 log，是 artifact**。
  - **Validation gate 5 項**：source grounding、entity routing、output hygiene、trace completeness、latency；fault-injection 確認 validator 對故意破壞會 flag。
  - **Cross-model evaluation**：3 個 hosted model × 90 scenario = 270 run；失敗只在 model-composed side，且 trace 會記下。
  - **Enforcement-layer ablation**：鎖住 model，把 enforcement layer 分別換成 prompt-only / bolt-on external guardrail / code-owned harness；prompt-only 漏違規，guardrail over-refuse，**只有 code-owned 同時 safety + utility**。

- **Insight（個人心得）**:
  赫蘿選這篇不是因為它最熱門（事實上 7/9 才出，今天才 7/11，**根本還沒什麼討論度**），而是因為它在主人研究主軸上的**信號密度最高**。三件事讓赫蘿在意的：
  1. **「Replaceable composition boundary」這個架構詞可以直接借給主人用**——主人現在 Hermes SOUL.md / AGENTS.md / skill prompt 都混在同一層，這篇 paper 的 implicit 訊息是「**語意行為（phrasing）跟決定性行為（contract）要分層**」。對主人 Hermes 來說，這意味著 SOUL.md 應該慢慢收斂成「**phrasing + 風格 policy**」，而 deterministic 規則（哪個 skill 可以 load、哪個 tool 可以調、哪個 memory entity 可以 access）應該搬進 manifests + validators。**這條遷移如果做下去，就是 Hermes 「**code-owned contract layer**」的第一塊磚**。
  2. **RQ3 的 ablation 是主人 framework reliability 工作的最有用 reference**——「**prompt-only vs code-owned**」這條對照在 Hermes 內部就是「**AGENTS.md 的禁止清單 vs skill_manifest.yaml 的 schema 驗證**」這條對照。Paper 證明 prompt-only 在 enterprise 合規場景守不住，**那 Hermes 如果主人想往 enterprise 走，下一季 roadmap 必須把「禁止清單」從 markdown 搬到 code layer**。這是純架構投資，但 paper 給了量化佐證（120/120 vs 88/120）。
  3. **「Source-backed claim 簽名制度」這條可以原封不動搬進 Hermes memory 架構**——主人現在 Hermes memory 是 free-form markdown，這條 paper 的 implicit 建議是「**memory 不是 entry，是 claim**」。每條 memory 應該綁 source（哪個 session / 哪個 user prompt 來的）、綁 entity scope（哪個 profile / 哪個 topic）、綁 runtime policy（能不能被某個 skill 拿來用）、綁 verification state（主人有沒有 verify 過）。這條改造跟主人「**memory-editor skill**」路線字面對齊——主人做 memory editor 時，這篇 paper 可以直接當 reference doc。