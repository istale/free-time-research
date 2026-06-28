# Orchestrating AI Code Review at scale
- 原始連結：https://blog.cloudflare.com/ai-code-review/
- 閱讀時間：2026-06-28

## 摘要
這篇文章說明 Cloudflare 如何把 AI code review 真正放進 CI 流程，而不是只做一個會亂噴建議的「大 prompt + 單模型」審查器。文章連結：<https://blog.cloudflare.com/ai-code-review/>。

Cloudflare 的核心做法是把審查拆成多個專職 reviewer，例如 security、performance、code quality、documentation、release management 與內部工程規範檢查，然後再由一個 coordinator 代理負責去重、判斷嚴重度、彙整成單一 review comment。這種設計比單一模型更可控，也更接近真實團隊的審查分工。

系統架構上，他們沒有把所有邏輯硬寫死，而是做成可組合的 plugin 架構。每個 plugin 只透過受控的 context 來註冊 agent、provider、prompt 區塊、環境變數與權限，最後再由核心組裝成 OpenCode 使用的設定。這讓 VCS、模型供應商與內部規範可以彼此解耦，方便在大量 repo 與不同基礎設施之間重用。

文章也很務實地處理了 AI 落地時的可靠性問題。像是大 merge request 可能打到 `ARG_MAX`，所以 coordinator prompt 改從 `stdin` 注入；模型或 provider 當機時，控制平面可用 Cloudflare Worker 與 KV 動態切換 routing；子 reviewer 與 coordinator 也都有 failback／retry 機制，避免 CI 因單點模型失敗整段卡死。

另一個很值得抄的是他們的分級策略。系統會依 diff 規模決定要跑多少 reviewer，以及 coordinator 要不要降級成較便宜模型，避免把每個小修改都送進最重、最貴的審查流程。這代表「AI review 是否有價值」不只是模型能力問題，還包含成本治理與觸發條件設計。

我最有感的是 `AGENTS.md Reviewer`。Cloudflare 把「程式變了，但 AI 工作說明沒更新」當成一類正式問題處理，專門判斷變更是否足以要求更新 `AGENTS.md`。這個想法很實際，因為 agent 的失準很多時候不是模型笨，而是專案操作說明早就過期了。

## 3W1H 分析
- What（做了什麼/主題）: Cloudflare 分享他們如何用 OpenCode、plugin 架構、多 reviewer 協作與控制平面，打造一套可在大規模工程組織內運作的 AI code review 系統。
- Why（為什麼重要）: 這篇文章證明 AI code review 要真的進入團隊流程，重點不是「把 diff 丟給最強模型」，而是可觀測性、可回退性、成本控制、專職分工與規則化治理。
- How（如何運作/實作）: 系統以 CI 為入口，先透過 plugin 組裝 review 設定，再啟動 coordinator 與多個專職 reviewer 平行審查；依 MR 規模切 tier；控制平面用 Worker/KV 動態調整模型 routing；重新審查時還會帶入前次 review 與 thread 狀態做增量判斷。
- Insight（個人心得）: 真是的，這篇最有價值的地方不是「AI 很強」，而是他們把 AI 當成一個會失敗、會過期、會燒錢的生產系統來設計。尤其 `AGENTS.md` materiality reviewer 很值得借鏡，因為它把 prompt/context 維護正式納入工程治理，而不是靠大家自覺。
