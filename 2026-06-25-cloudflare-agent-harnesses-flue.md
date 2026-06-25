# Bringing more agent harnesses and frameworks to Cloudflare, starting with Flue
- 原始連結：<https://blog.cloudflare.com/agents-platform-flue-sdk/>
- 閱讀時間：2026-06-25

## 摘要
這篇 Cloudflare 工程文〈[Bringing more agent harnesses and frameworks to Cloudflare, starting with Flue](https://blog.cloudflare.com/agents-platform-flue-sdk/)〉很準地點出一件事：2026 年的 agent 問題，已經不是「能不能跑起來」，而是「能不能在生產環境裡活下來」。文章把 agent 生態拆成三層：最上層是 **framework**（像 Flue，負責專案結構、CLI、整合與開發體驗），中間是 **harness**（像 Pi、Project Think，負責 agentic loop、工具呼叫、上下文管理），底層則是 **runtime/platform**（Cloudflare Agents SDK，提供 durable execution、狀態、儲存與執行環境）。這個分層很重要，因為它把「Agent 很聰明」和「Agent 系統很可靠」明確拆開了。

文章最有價值的部分，是把 production agent 會遇到的基礎設施問題講清楚。Cloudflare 認為，一個 agent turn 往往不是單次請求，而是可能持續數十秒到數分鐘的流程：串流輸出、工具呼叫、等待外部回應、甚至人工審批。這種流程在任何中斷後都不能直接從零再來，所以 Agents SDK 用 `runFiber()`、`stash()` 與 `onFiberRecovered()` 提供 checkpoint/recovery 機制，讓 agent 可以在重啟後知道自己斷在哪裡，接著決定要重建狀態、重播步驟，還是繼續往下走。這其實是把長回合 agent execution，當成分散式系統中的可恢復工作流來設計。

另一個很強的觀點，是「不要一直往模型身上塞更多工具，而是給它安全的程式執行能力」。文章主張當工具表面越長，模型越難選對工具，因此更好的模式是提供單一 code-execution 工具，讓模型自己寫 TypeScript/JavaScript 來組合 API。Cloudflare 用 `@cloudflare/codemode` 包 Dynamic Workers，把每段 LLM 產生的程式碼丟進獨立 Worker isolate 跑，兼顧安全、速度與成本。對只需要文字型檔案操作的 agent，則用 SQLite-backed 的虛擬工作區與 `@cloudflare/shell` 支撐 read/write/search/diff 等工作，只有真的需要 `npm install`、`git` 或編譯器時，才升級到完整 container。這種「先用輕量 primitive，必要時再進完整 OS」的分層，非常適合 agent 基礎設施。

## 3W1H 分析
- What（做了什麼/主題）: 文章介紹 Cloudflare 如何把 production agent 所需的 durable execution、動態程式執行、虛擬檔案系統與動態 workflow primitives，下沉到 Agents SDK，並由 Flue 這類 framework 與 Pi 這類 harness 向上承接。
- Why（為什麼重要）: 很多團隊把 agent 當成 prompt product，但真正卡住上線的其實是中斷恢復、工具安全、檔案系統、長流程持久化與外部能力整合。這篇文把這些「被 demo 掩蓋掉的工程現實」講得很完整。
- How（如何運作/實作）: 用 Fibers 做 turn 級 checkpoint/recovery；用 Dynamic Workers 跑 agent 產生的程式碼；用 Durable Object + SQLite 提供持久虛擬 workspace；用 Dynamic Workflows 支撐可重試、可睡眠、可等待事件的多步驟流程；再透過 bindings 接到瀏覽器、自動化、記憶、搜尋、容器與多模型推論能力。
- Insight（個人心得）: 這篇最值得記住的不是 Flue 本身，而是它揭示了 agent stack 正在標準化成「framework / harness / runtime」三層。真是的，很多人還在討論 prompt 該怎麼寫得更花，真正拉開差距的其實是底層 platform primitives 是否足夠耐操，能不能把 agent 從一次性展示品變成長時間運作的基礎設施。
