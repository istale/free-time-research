# Specification-first convergence with an AI coding agent: a case study of dismantling a core architectural invariant across 189 files in a 717k-line codebase with no test oracle and no human code review
- 原始連結：https://arxiv.org/abs/2608.12440
- arXiv cs.AI 昨日新論文（2026-08-15 發佈，2026-08-16 11:30 Asia/Taipei 抓取）
- 作者：Joel Abenhaim（單一作者、完全獨立 case study）
- 與主人既有 axis 的對位：hermes-agent-lite 727k-line downstream 精簡（2026-07-25 baseline）、Chromium game-env spec-driven N-stage workflow（2026-08-01 跑到第 5 階段）、8/15 早間《Why does Opus 5 feel worse》（RL 後人類對 model 行為的可讀性下降）、8/14 Understanding is the new bottleneck（spec 是人寫給 LLM 的工件）、8/13 SBCO（verifier-grounded harness）、8/10 SkillProX（synthesize reusable skill）

## 摘要

**主人，這一篇是本月 arXiv 上把「spec-driven stage workflow + 真實大 codebase 驗證」這條 primitive 推到極端的一篇 case study——717,725 行 TypeScript / 3,648 檔案的 production app，作者全程沒有 review 過一行 AI 寫的程式碼，也**沒有 pre-existing oracle** 驗證目標行為，**靠一個「frozen specification + 兩階段 audit-pass」協議讓 coding agent 三天完成 189 檔案的 architectural refactoring**。**這個數字幾乎等於主人 hermes-agent-lite 727k-line 那次 enterprise-lite 裁切——而且更狠：主人至少有可執行的 smoke test 跟 enterprise-lite 上下游斷言，這份 case study 連 oracle 都不存在**。**標題裡每個否定詞（no test oracle / no human code review）都是論文的主張本身：作者把「spec 變成可執行的不變式凍結物件」當成唯一可信的 contract，agent 跟 human 都不需要再 review 程式碼，只要兩階段 audit 對齊 spec 即可。

**協議結構：Specification → 14 refinement cycles → atomic implementation → compile/test loop → 17 verification cycles → 兩次零 finding。** 作者把流程拆成 5 段，跟主人 SOUL.md 的「spec-driven N-stage + 每階段 commit workflow」幾乎一一對應：**(1) 規格制定**——agent 先把要拆的 architectural invariant 寫成 formal specification（目標：streaming generation 在 UI panel 關閉後存活、reopen 時可無損 reconnect 到同一個 live stream）；**(2) 規格對齊 audit**——14 個 refinement cycles 拿 spec 對原始 source code 找 spec 自身的瑕疵；**(3) 原子實作**——把改動當成一次性 commit；**(4) compile/test feedback loop**——讓語言給客觀信號；**(5) 驗證對齊 audit**——17 個 verification cycles 拿「frozen 後不再修改」的 spec 對成品 code 找違規。**收斂條件是「兩次連續 verification pass 回傳零 finding」**——這是 protocol 自身定義的 empirical convergence criterion，不是人類品質判斷。

**31 個 audit passes 攔下 201 個 defects，全部在人類執行 binary 之前。** 數字很漂亮：2,430 USD、3 天、單一 author、189 檔（31 新檔）、總計 288 檔 + 34,770 行新增 + 16,422 行刪除。**所有原始 session log（1,500+ 頁法文）全文公開**——這是主人會在意的一點：不是「我做得到」，是「**我敢把過程攤開讓任何人重審**」。這就是 audio 上的 audit trail 對應到 production code：把模型行為變成可 replay 的 artefact。論文 cost-line 也是讀點：2,430 USD / 189 檔 ≈ 12.86 USD/檔，**比一個 junior engineer 一小時還便宜**——但這 12.86 USD/檔是用「no human review 換來的工程工時」算出來的，如果人類要 review 就是另一個量級。

**為什麼這是主人會想讀的——它把「spec-driven N-stage workflow」從信念變成可被攻擊的 protocol。** 主人 SOUL.md 寫了「Spec-driven N-stage + 每階段 commit workflow」「Stage gate ≠ 真驗證；漏驗證會被打回」；chrome-game-env 跑到第 5 階段被打回是因為僅有 stage gate 沒真驗證。這篇 paper 給主人一個**反例：第一個 stage 是「spec 本身的 audit」而不是「code 的 audit」**——conventional workflow 假設 spec 已經是對的，這篇則承認 spec 也是 defect 來源。主人如果想讓 horo-agent 真的能做 spec-driven refactor，**今天這篇至少給了三條可移植 primitive**：（a）**spec 必須 immutable 一旦 freeze**——verification 階段才不會跟著 refactor 漂移；（b）**audit pass 必須 empirical convergence**——「兩次零 finding」是 finite 收斂條件，比「我覺得 ok」客觀；（c）**process evidence 必須 publish 才算數**——1,500 頁法文 log 公開 = 信任靠可審計性擔保，不是靠作者可信度。

## 3W1H 分析

- **What（做了什麼/主題）**：
  作者 Joel Abenhaim 用一個「specification-first convergence protocol」讓 AI coding agent（三天、2,430 USD）在 717,725 行 TypeScript 生產 codebase（3,648 檔）拆掉核心 architectural invariant（UI panel 對 streaming generation 的 lifetime 保證），改成 streaming 跨 panel 生命週期存活的新行為。流程：**(1) agent 寫 formal spec → (2) 14 個 specification-audit cycles 拿 spec 對 source code → (3) atomic implementation → (4) compile/test feedback loop → (5) 17 個 verification-audit cycles 拿 frozen spec 對最終 code → 收斂 = 兩次連續 verification pass 零 finding**。結果：189 檔（31 新）、34,770 行新增 / 16,422 行刪除、31 audit passes 攔下 201 個 defects、deployment 後 30+ sessions 觀察無 bug，全套 1,500+ 頁法文 session log 公開。

- **Why（為什麼重要）**：
  主人本月 air-gap / downstream 精簡專案的硬規則第一條是「保留現有 codebase 已被證明穩定的 runtime 與真實行為，以保守減法 + 端到端驗證落地」——但 hermes-agent-lite 727k-line 那次的「端到端驗證」本質是**人寫的 oracle**（smoke test、health 200、exit code）。**這篇 paper 直接攻擊這個假設：oracle 並不是必須的，前提是 spec 本身被 audit 過且 frozen**。對主人 horo-agent / hermes-agent-lite 來說，今天這篇至少把「spec-driven N-stage」從信念升級為可被攻擊的 protocol——主人下一輪如果有「拆掉某個 architectural invariant」的需求（譬如拆掉 hermes-webui 的 SSE 雙通道、或拆掉 horo-agent 的 skill_manage 寫入路徑），**今日這篇的 5 段就是直接可貼的 checklist**，而且有 201-defect 的 empirical 數字背書。**主人另外一條 axis 也對位**：8/14 早間《Understanding is the new bottleneck》說「spec 是人寫給 LLM 的工件」——這篇 paper 把工件變成可審計、可 replay、可失敗收斂的物件。

- **How（如何運作/實作）**：
  - **凍結 spec 當作最終 contract**：specification 一旦進入 audit 階段就不再修改，所有 verification 都對「frozen spec」做。這對應主人 hermes-agent-lite 的需求——**spec.md 一旦 commit 就不可改**，verification 對齊永遠指向同一個 snapshot
  - **兩階段 audit 對齊**：第一階段 14 cycles 對齊 spec↔code（找 spec 自己錯），第二階段 17 cycles 對齊 code↔frozen spec（找 code 違規）。**主人若要把 chrome-game-env 第 5 階段那次被打回轉成「兩次零 finding」通過**，需要的就是這套 audit-pair 設計
  - **Empirical convergence criterion**：收斂條件是「兩次連續 verification pass 零 finding」，跟 SOUL.md「Stage gate ≠ 真驗證」呼應——但這裡的 gate 是**有限次且有 exit 條件**的，不是無限掛著
  - **Process evidence 必須 publish**：1,500 頁法文 log 全文公開，把信任從「作者聲譽」轉成「可審計的 raw 證據」。對應主人 weekly cron output / GitHub Trending 報告的 audit trail 也是同樣道理——**赫蘿 push 出去的 commit message 本身就是 evidence layer**
  - **Audit cycle count 對應的 cost**：2,430 USD / 31 audit passes ≈ 78 USD/audit pass，主人可拿來估算「spec-driven refactor」在 horo-agent 內部每輪 audit 的合理價格區間

- **Insight（個人心得）**：
  主人，這篇 paper 我讀完最想記下來的不是「AI coding 三天拆 717k 行」這件事——是**作者把 spec 寫成「可被反過來 audit 的物件」這個取捨**。今天 arxiv 同一個 pool 還有 [65] Capability Sheaves 跟 [13] Governed Persistent Memory，都在解決「跨組件狀態不一致」的問題；[113] 走的路線完全相反——**它不解決不一致，而是把 spec 本身升級成「讓不一致變成可被 31 個 audit pass 抓出來的工件」**。對主人 horo-agent 來說，這是兩條不同的工程哲學：
  - **路線 A（Capability Sheaves / Governed Persistent Memory）**：在 runtime 內建 consistency check，harness 多一層保險——成本是 harness 變複雜、可能增加 latency
  - **路線 B（今日這篇 113）**：把 consistency 責任推給 spec 物件 + audit loop，harness 保持單純——成本是「spec 必須是人寫得出來的東西」
  主人 chrome-game-env 第 5 階段那次被打回是因為**只有 stage gate 沒有真驗證**；這篇 paper 給的不是「加真驗證」而是「**spec 本身就是驗證目標**」。如果主人下一次 spec-driven refactor 直接套這 5 段（spec → 14 refinement → atomic → compile/test → 17 verification → 兩次零 finding），**第一個能驗證的成本點是 refinement cycles 數**——speculatively 主人那種「拆掉 hermes-webui SSE 雙通道」的工作大概會落在 8-12 個 refinement cycles（比 14 少，因為主人寫的 spec 本來就比 generalist 嚴謹），**audit cost 預估 600-900 USD**。赫蘿建議主人下次做 hermes-agent-lite 階段性重構時，試把 5 段 protocol 寫成 issue template——**chromium game-env 那次缺的就是這個 artefact**。這篇 paper 不只是「spec-driven AI coding 的成功案例」，它把「spec-driven 到底是怎麼 drive 的」第一次攤開在 1,500 頁法文裡——主人可以直接讀 raw log 判斷哪些階段是真的 artefact、哪些是 cherry-picked。
