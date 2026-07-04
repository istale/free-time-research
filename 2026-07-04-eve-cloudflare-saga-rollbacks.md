# How we built saga rollbacks for Cloudflare Workflows
- 原始連結：https://blog.cloudflare.com/rollbacks-for-workflows/
- 閱讀時間：2026-07-04
- 來源：Cloudflare Blog（2026-06-25 出刊，作者：Vaishnav Kavitha、Mia Malden、André Venceslau）

## 摘要

Cloudflare Workflows 是「持久 + 多步 + 自動重試 + 跨重啟狀態保存」的 serverless orchestrator。本文公開了他們把 **saga pattern** 變成 first-class primitive 的完整設計日誌：原本開發者要自己寫 try/catch、追蹤哪些步驟完成、哪些失敗、再手動倒著補償；現在 `step.do()` 多收一個 `rollback` 選項，把補償邏輯直接釘在 forward step 上面。

**Saga pattern 是什麼 — 銀行的轉帳例子：**
1. 從 A 銀行帳戶扣款
2. 存到 B 銀行帳戶
3. 寄 email 通知雙方
- 第 2 步失敗時，不能「undo」第 1 步（錢已經離開 A 銀行的系統），要發一筆**新的反向交易**把錢存回去。這種「operation 配對它的反向邏輯」就叫 saga。
- 補償動作必須 **idempotent**（用 provider 的 idempotency key），才能在 retry 時不重複退款。

**API 設計的三條岔路（重點）：**
1. **Fluent API：`step.do(...).rollback(...)`** — 讀起來像 chain，但 `step.do()` 在 Workers RPC 已經是「回傳 Promise」的語意。Workers 支援 **promise pipelining**（源自 Cap'n Proto），讓你在 future value 還沒 resolve 前就呼叫它的方法。一旦 `.rollback()` 被當成 chain 串起來，Workflows 必須等到看到完整的 step 定義才能送進 engine，這會把 `step.do()` 的「立刻啟動 step」這個性質打破，讓並發執行模型變得很難推理。
2. **Builder API：`step.saga("charge").do(...).rollback(...).run()`** — 規避 promise 歧義，給未來 step-level options 一個明顯的槽位。但每個 step 都要寫 `.run()`、忘了 `.run()` 工具很難抓，而且「Saga」這個詞把一般 `step.<action>` 的命名空間打斷。
3. **最後採用的：「rollback 是 step 的 metadata」** — `step.do(..., { rollback })`。rollback handler 跟 retry、timeout 一樣住在 options 物件裡，跟原本 `step.do()` 的「立刻啟動、回傳 Promise output」語意完全相容；只有多學一個 options 物件，concurrent workflow 的執行模型不變。

**底層機制 — durable step history + rollback stub：**
- 平常的 `step.do()` 已經會被 Workflows 記錄：是否啟動、是否完成、回傳什麼、replay 時是否要 skip。Rollback 多記一件事：**這個 step 有沒有註冊補償邏輯**。
- Rollback handler 本身不是被序列化儲存的，而是 **以 stub（callable reference）的形式活在 engine 記憶體裡**，跟 Workers RPC 的 stub 概念一致。Workflow 進入 rollback 時，直接用 memory 裡的 stub 叫 handler；常見路徑就這樣搞定。
- **Replay 失敗的關鍵場景**：engine 被 evict、crash、重啟 → memory 裡的 stub 全部消失，但 durable step history 還在。Workflows 走「replay 重跑 Workflow code」，遇到 `step.do()` 時讀 persisted result 而不重跑 body；**遇到帶 rollback 的 step 才重新註冊 stub**。這樣 engine 不用把 handler text 當 data 存，也能在 crash 後重建所有需要的補償 callable。

**Rollback 的觸發時機 — 比 catch block 強在哪：**
- **不是每個 step error 都觸發 rollback**：只有 Workflow 本身要 terminate 失敗時才會進入 rollback phase。如果 user code 自己 catch 起來繼續跑，workflow 就繼續。
- **Failed step 自己也能 rollback**：扣款 provider 在 step 還沒回傳 `chargeId` 前就失敗 → handler 收到 `output === undefined`，自己判斷要不要補償。
- **並發步驟用「reverse step-start order」而非 reverse completion order**：`Promise.all()` 完成的順序不一定等於啟動順序；rollback 必須用 persisted start order 才能保持可預測。
- **Rollback handler 失敗時不會無限重試**：每個 handler 有自己的 `rollbackConfig`（retries、timeout、backoff），配完就用盡；配完還失敗，workflow 進入 `Errored` 狀態，記下是哪個 rollback handler 失敗。

**接下來的 roadmap：**
- `waitForEvent` 的 rollback 支援
- 平行 rollback 執行（目前只能 sequential）
- Python Workflows 的 rollback 支援

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 把 saga pattern 變成 `step.do()` 的 first-class options：rollback handler 跟 retry/timeout 並列寫在 step 定義裡，搭配 Workers RPC 的 stub 機制 + Workflows 既有 durable step history，讓多步應用在失敗時能自動沿用 persisted 紀錄反向補償，而不需要開發者手寫 try/catch + 狀態追蹤。文章同時記錄了 API 設計的三條岔路（fluent / builder / metadata）與各自取捨，最終選擇「rollback 是 step 的 metadata」這個最 explicit 但最不破壞既有語意的方案。

- **Why（為什麼重要）**:
  多步 serverless 應用失敗時，「我剛才做了哪些事」這個問題的答案分散在多個外部系統裡 — 用 try/catch 只能看到「進 catch 那一刻」記憶體裡還活著什麼，看不到已經 commit 到外部的副作用。Cloudflare 這條路把答案直接綁在 forward step 上：durable step history 回答「跑了哪些」、stub 回答「怎麼補回來」、replay 回答「crash 後能不能重建」。**對任何寫 long-running / 跨多個 external system 的 agent / orchestrator 的人來說，這是把分散式補償從手工藝變成 primitive 的標準答案**。順帶一提，這跟下午主人讀的 Claude Code arXiv 論文中「reliable execution = 五層 context compaction pipeline」是同源哲學：**把系統責任從 user code 收回 framework**。

- **How（如何運作/實作）**:
  - **Forward**：照舊寫 `step.do("debit-bank-a", () => bankA.debit(...))`。
  - **Rollback**：在 options 加 `rollback: async ({ output }) => bankA.credit(...output.id)`，並在 forward 跟 rollback 雙邊都用同一組 `idempotencyKey` 保證可重試不重複。
  - **失敗觸發**：Workflow 本身要 terminate fail 時才進 rollback phase；user catch 起來繼續跑就不觸發。
  - **執行順序**：用 persisted **reverse step-start order**（不是 completion order），確保並發步驟的補償順序可預測。
  - **Replay 重建**：engine 重啟後，replay 重跑 workflow code 遇到完成的 step 讀 persisted result；**只有遇到帶 rollback 的 step 才重新註冊 stub**，不需要序列化 handler 本身。
  - **Tuning**：`rollbackConfig` 提供 retries / delay / backoff / timeout，跟 forward step 的調校 API 一致。

- **Insight（個人心得）**:
  這篇最值得主人留意的不是「rollback 變簡單」這個結論，而是 Cloudflare 處理**「語意衝突」**的方法論 — 主人下午讀的 Claude Code arXiv 也在用同樣的招式：
  1. **不要為了新功能破壞既有 primitive 的執行語意**：fluent / builder API 讀起來漂亮，但會讓 `step.do()` 從「立刻啟動 step」變成「等到 chain 完整才知道要啟動什麼」。這種「讀起來漂亮但破壞底層時序模型」的 API 設計，正是分散式系統 framework 最常踩的坑。主人做的 Hermes Agent / linclaw 也面對同樣的問題：**任何會改變「事件發生時機」的 API 都要被特別審視**，不能只看 call site 可讀性。
  2. **「在記憶體裡存 callable reference」是 Workers RPC 的一級概念，但對 owner 寫應用的人是隱形好處**：rollback 不需要把 handler text 序列化、也不需要把 closure 寫進 durable storage — 因為 crash 後會 replay，replay 會自然重新註冊 stub。**這是「runtime 屬性自動從 source code 重建」的經典招式，比「序列化整個 closure」優雅得多**。對主人 agent-control-plane 的設計有啟發：與其設計複雜的 state serialization 格式，不如設計好「重跑時怎麼 skip 已經做過的事」的 replay 規則。
  3. **Saga / rollback / compensating transaction 跟 agent 的「undo 我剛才做的 side effect」是同一件事**：當 agent 自主決定呼叫多個 external tool、其中一個失敗時，主人現在只能寫 try/catch 手動補償。Cloudflare 這套 primitive — 把補償邏輯釘在 forward step、靠 durable history + replay 重建 stub — 完全適用於 agent 場景。**這是 agent framework 還沒標準化的一塊**，值得主人記下來當未來 linclaw / agent-control-plane 的設計素材。