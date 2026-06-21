# Orchestrating AI Code Review at scale
- 原始連結：<https://blog.cloudflare.com/ai-code-review/>
- 閱讀時間：2026-06-21

## 摘要
這篇 Cloudflare 工程文章拆解了他們如何把 AI code review 放進實際的 CI 流程，而不是只停留在「把 diff 丟給模型問有沒有 bug」的玩具階段。原文在這裡：<https://blog.cloudflare.com/ai-code-review/>。

Cloudflare 的核心做法不是依賴單一巨大 prompt，而是把審查流程拆成多個專職 reviewer agent，由 coordinator 統整結果。實務上最多會同時啟動七種 reviewer，涵蓋 security、performance、code quality、documentation、release management 與內部工程規範檢查，最後再由協調代理去重、判斷嚴重度，輸出單一結構化 review comment。這種設計把「模型會說很多話」轉成「系統只回報值得處理的事」。

文章裡最有價值的部分是它沒有神化模型，而是強調 orchestration 與 platform engineering。整套系統採 plugin architecture，讓 VCS、AI provider、公司內部規範與 telemetry 各自解耦；OpenCode 只負責 agent 執行，外層則由 Cloudflare 自己的 CI-native orchestration 包起來。為了避免大型 merge request 觸發 Linux `ARG_MAX` 限制，他們甚至改用 `stdin` 傳 coordinator prompt，並用 JSONL 事件流接收結果。

另一個關鍵是「分級審查」。小型變更只跑 coordinator 加一位泛用 reviewer，中型變更增加 code quality 與 documentation reviewer，大型或高風險變更才全面啟動所有 specialist。這代表 AI review 真正要能落地，成本控制與審查深度必須一起設計，不能每次都用最貴、最慢、最完整的配置硬跑。

我也很喜歡他們替 `AGENTS.md` 做了專門 reviewer 的思路。Cloudflare 把 AI 指令文件視為會快速腐化的 operational artifact，當 repository 發生測試框架、CI/CD、build tool 或目錄結構等重大變化時，系統會提醒開發者同步更新 `AGENTS.md`。這其實是在補 agent 時代最容易被忽略的一塊：不是只有 code 會 drift，給 agent 的環境說明也會 drift。

## 3W1H 分析
- What（做了什麼/主題）: Cloudflare 分享他們如何把 AI code review 做成可在 GitLab CI 內大規模運行的多代理審查系統，並以 plugin 與 coordinator 架構整合不同 reviewer。
- Why（為什麼重要）: 多數 AI code review 失敗不是模型不夠聰明，而是流程太天真、訊號太吵、成本太高。這篇提供了真正能進入工程組織日常流程的設計藍圖。
- How（如何運作/實作）: 以 OpenCode 作為 agent runtime，外層用 CI-native orchestration 啟動 coordinator 與多個 specialist reviewer；plugin 負責組裝 VCS、模型、規範、追蹤與設定；再依變更規模做 reviewer tiering，最後輸出單一整合評論。
- Insight（個人心得）: 真正的護城河不是「會用 LLM 審 code」，而是把 agent 編排、成本分級、上下文治理與規範同步一起做掉。看完會更確信，未來工程團隊的競爭力很大一部分會來自 agent operating system，而不是單一模型選型。
