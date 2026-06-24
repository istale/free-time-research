# An open-source spec for Codex orchestration: Symphony
- 原始連結：<https://openai.com/index/open-source-codex-orchestration-symphony/>
- 閱讀時間：2026-06-24

## 摘要
這篇 OpenAI 工程文章〈[An open-source spec for Codex orchestration: Symphony](https://openai.com/index/open-source-codex-orchestration-symphony/)〉談的不是單一 coding agent 怎麼更聰明，而是怎麼把整個 issue tracker 變成 agents 的協作控制面。OpenAI 團隊在把 Codex 當成正式工程同事之後，遇到的新瓶頸不再是寫程式本身，而是人類必須頻繁切換上下文、盯著 session 跑、追 CI、處理 rebase 與 merge 流程。Symphony 的核心做法，是把工作單位提升到 ticket/PR 層級，讓 orchestrator 在 devbox 上持續運行，從 issue 撿任務、派 agent 做實作、跑檢查、處理 review 與合併，形成一個「永不睡覺」的開發流水線。

文章裡最醒目的成果是：在部分 OpenAI 團隊中，導入 Symphony 後前三週 landed PR 數量提升了 500%。但作者認為更深層的變化不是 throughput，而是「探索成本」被壓低了。當工程師不再需要親自 supervision 每一個 Codex session，團隊就可以更便宜地丟出模糊想法、重構假設與產品實驗，讓 agent 先去試，行得通再保留，失敗也能反過來暴露文件、測試或 skill 的缺口。連產品經理與設計師都能直接在系統裡提需求，再等 agent 回傳包含工作成果與驗證材料的 review packet。

在實作哲學上，Symphony 也不是一套僵硬的 state machine。文章明確說早期那種只讓 agent「照單實作單一任務」的框架太保守，因為模型其實能處理更多責任，例如讀 review feedback、拆出後續 issue、關閉舊 PR、追 CI 狀態與整理報表。後來他們改成給 agent 高層目標與工具，而不是死板轉移規則；同時補強 `gh` CLI、CI log skill、端到端測試、QA smoke test 和文件，讓 agent 在更大的自治空間裡仍能穩定工作。這讓 Symphony 更像一個 objective-driven 的 orchestration spec，而不是一串硬編碼 workflow。

## 3W1H 分析
- What（做了什麼/主題）: 文章介紹 OpenAI 如何用開源規格 `Symphony` 把 issue tracker 轉成長時間運作的 agent orchestration 系統，讓 Codex 從接 ticket、做實作、追 CI 到處理 review 都能串成閉環。
- Why（為什麼重要）: 它點出多數團隊導入 agent 之後真正的瓶頸是 orchestration 與上下文切換，不是模型少幾分 benchmark。只要把人從大量 babysitting 裡解放出來，探索與交付的經濟性就會整個改變。
- How（如何運作/實作）: 用 issue/ticket 當工作入口，在持續運行的 orchestrator 上派發 agent 任務；讓 agent 擁有 `gh`、CI logs、測試與 QA 等工具；以高層 objective 取代僵硬狀態機，並把 review、rebase、merge 等後段流程也納入自動化。
- Insight（個人心得）: 真正成熟的 agent 系統，不該只停在「叫模型幫我寫 code」，而是要把 backlog、驗證、回饋與合併流程一起代理化。真是的，很多人還在比 prompt 技巧，結果 OpenAI 已經在比誰的 orchestration 能讓整個團隊少切上下文、多做高價值探索了。
