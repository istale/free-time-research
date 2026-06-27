# How we built saga rollbacks for Cloudflare Workflows
- 原始連結：<https://blog.cloudflare.com/rollbacks-for-workflows/>
- 閱讀時間：2026-06-27

## 摘要
這篇 Cloudflare 工程文章講的是他們如何替 Workflows 補上 saga rollback，讓多步驟長流程在中途失敗時，不只知道哪裡壞掉，還能按可預期的順序執行補償動作。原文在這裡：<https://blog.cloudflare.com/rollbacks-for-workflows/>。

文章一開始就抓住 durable workflow 最麻煩的地方：步驟失敗不代表前面的外部副作用會自動消失。像跨銀行轉帳這種流程，就算第二步失敗，第一步扣款可能早就提交到外部系統了，所以需要的不是「回到舊狀態」，而是執行一個語意上的反向操作。Cloudflare 把這件事直接放進 `step.do()` 的設計裡，讓每個步驟可以在宣告 forward action 的同時，也宣告 rollback handler。

真正有技術含量的是他們沒有把 rollback 做成表面上很順眼、實際上會破壞 execution model 的 fluent API。文章解釋得很清楚：`step.do()` 在 Workflows 裡本來就是 durable unit，會立刻啟動、持久化狀態，還牽涉 Promise pipelining 與並行語意。如果改成 `step.do(...).rollback(...)` 這種鏈式 API，系統就得等到後面呼叫完成，才知道這個 step 的完整定義，連步驟何時開始都會變得不直觀。最後他們選擇把 rollback 當成 `step.do()` 的 options，這樣語意和執行模型才一致。

另一個很好的設計點是 rollback 的觸發與排序規則。不是每次 step error 都立刻 rollback，而是等整個 Workflow 確定要終止失敗時，才根據 durable step history 去決定哪些 step 需要補償。更重要的是，rollback 不是照完成順序倒著跑，而是照 `step-start` 順序反向執行。這解決了平行步驟常見的不可預測性，因為在 `Promise.all()` 類型場景裡，完成順序常常和啟動順序不同，但 rollback 必須有穩定可推理的規則。

底層實作也很值得注意。Workflows 不會把 rollback function 的原始程式碼當資料持久化，而是持久化 step history：哪些步驟開始了、完成了、是否註冊了 rollback、留下什麼 output。當 engine evict、crash 或 restart 時，系統透過 replay 重建 rollback handler 的 callable stub，但不會重跑已完成的 forward side effects。這個做法很實際，因為它把「可恢復性」建立在流程引擎已經擅長的 durable history 上，而不是要求應用層自己重新推理現場。

我覺得這篇的價值在於，它示範了怎麼把一個大家都懂概念、但很容易做得半套的模式，落成能在產品裡可靠運作的 API。很多系統談 rollback 只停在 `try/catch` 或交易語意，但真實世界的長流程不是同一個 process、也不是同一個 DB transaction。Cloudflare 這次是把 compensation 提升成一等公民，而且跟 retries、timeouts、logs、lifecycle events 放在同一套機制裡，這才是 durable orchestration 該有的味道。

## 3W1H 分析
- What（做了什麼/主題）: Cloudflare 為 Workflows 新增 saga rollback 機制，讓每個 `step.do()` 都能宣告補償邏輯，並在 Workflow 終止失敗時自動執行回滾。
- Why（為什麼重要）: 真正的多步驟流程常常跨外部系統，前面步驟一旦產生 side effect，就不能靠單純丟錯或重試解決。把 rollback 做成 durable runtime 的一部分，才能讓長流程具備工程上可依賴的失敗恢復能力。
- How（如何運作/實作）: 將 rollback handler 放入 `step.do()` options；Workflow 失敗時依 durable step history 判定可補償步驟；按 reverse `step-start` order 執行 rollback；若執行期間發生重啟，透過 replay 重建 handler stub，同時避免重做已完成的 forward side effects。
- Insight（個人心得）: 好的 workflow 平台不是只有「可以重試」，而是把補償、排序、恢復、語意邊界全部做清楚。這篇最值得學的不是 saga 名詞本身，而是 Cloudflare 對 API 語意與執行模型一致性的執著，這種細節才是平台能不能被長期信任的分水嶺。
