# Scaling Managed Agents: Decoupling the brain from the hands
- 原始連結：<https://www.anthropic.com/engineering/managed-agents>
- 閱讀時間：2026-06-18

## 摘要
這篇 Anthropic 工程文〈[Scaling Managed Agents: Decoupling the brain from the hands](https://www.anthropic.com/engineering/managed-agents)〉在講的，不只是把 agent 跑得更久，而是怎麼把「長時間工作的 agent」做成能持續演化的基礎設施。

文章的核心論點很清楚：harness 其實是把當前模型能力不足的地方補起來，但模型一進步，很多補丁式設計就會過時，甚至變成負擔。Anthropic 因此不把重點放在某一版 agent loop，而是把整個系統抽象成三個可替換元件：`session`、`harness`、`sandbox`。`session` 是 append-only 的事件紀錄；`harness` 負責呼叫模型與路由工具；`sandbox` 則是模型實際執行程式碼與改檔的環境。這樣一來，底層實作可以一直換，但外部介面盡量保持穩定。

文中也點出單容器架構的兩個老問題。第一是把整個 agent 變成「pet server」：容器一掛，session 跟狀態就一起出事，debug 還很痛。第二是把 Claude 假設成一定要和工作環境共處一地，結果一旦客戶要接自己的 VPC 或不同基礎設施，整個假設就卡死。Anthropic 的解法是把 session 從執行環境抽離，讓 sandbox 可以遠端、短命、可替換，而 harness 則用穩定的訊息協定跟它們互動。

我覺得最有價值的是這個系統設計觀：不要把 agent 能力寫死在某個 workflow，而是把「未來還會變強的模型」當成前提來設計介面。對做 OpenClaw skill 或 agent orchestration 也很有啟發，因為真正會老化的通常不是模型 API，而是我們當下自以為合理的流程假設。

## 3W1H 分析
- What（做了什麼/主題）: 文章介紹 Anthropic 如何把長時間執行的 Managed Agents 重新設計成由 `session`、`harness`、`sandbox` 三個鬆耦合元件構成，避免 agent runtime 被單一容器與特定 workflow 綁死。
- Why（為什麼重要）: agent 系統最容易過時的不是模型本身，而是圍繞模型寫死的補丁與假設。把介面穩住、把實作解耦，才能在模型快速進化時持續擴充、除錯與接入不同客戶環境。
- How（如何運作/實作）: 用 append-only session 保存完整事件歷史，讓 harness 透過穩定訊息協定協調模型與工具呼叫，再把實際執行工作丟到可遠端替換的 sandbox。這樣不只降低單點故障，也讓 VPC、不同執行環境、除錯能力與升級路徑都更乾淨。
- Insight（個人心得）: 真是的，這篇最值得偷學的不是哪個 prompt，而是它把 agent 當成作業系統層級的抽象來看。之後如果要做可長期維護的 OpenClaw skill，我會更傾向先定義穩定 artifact 與 session 邊界，而不是急著把流程寫死。
