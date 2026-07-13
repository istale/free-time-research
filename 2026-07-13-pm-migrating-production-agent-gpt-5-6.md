# Migrating a production AI agent to GPT-5.6
- 原始連結：https://ploy.ai/blog/migrating-a-production-ai-agent-to-gpt-5-6
- 閱讀時間：2026-07-13
- 作者：Lorenzo Gentile（Ploy 工程團隊）

## 摘要

Ploy 團隊記錄他們把生產環境的網站建構 agent 從 Claude Opus 4.8 切換到 GPT-5.6 Sol 的完整遷移歷程。原本「用同一個 SDK 換模型就好」的假設被實戰打臉——他們發現「模型」其實是一連串 provider-specific 的行為，整個 stack 都已悄悄對舊模型特化。

**Step 0：先修評測 harness，再相信數字**
- 他們的 eval suite 是「真實 agent 跑真實 fixture workspace」，涵蓋從「從零建首頁」到「驗證 clone 請求是否安全」。每個失敗案例要拉 trace 對照。
- 跨模型跑一輪才發現：harness 預設就對 incumbent 模型特化。例如 tool-call budget 是照 Opus 的 sequential 風格配的，GPT-5.6 平行呼叫直接撞穿；eval executor 不支援批次檔案讀取，Opus 很少用、GPT-5.6 大量用。約三分之一原始失敗其實是 harness 假設，不是模型行為。
- 還有一個隱形陷阱：dataset 沒設 minScore threshold 預設繼承 1.0，GPT-5.6 拿到 0.98 分被當失敗，Opus 個別檢查全過也被當失敗。「兩個都合理的設計方向，卻是同一個看不見的門檻」。

**整體效益：2.2× 更快、便宜 27%、品質不打折**
- 修正 harness 後的 redesign suite 數據：cost $3.06→$2.22、wall-clock 8m00s→3m42s、output tokens 33.0K→17.1K、visual score 0.936→0.970。
- GPT-5.6 寫程式明顯精簡——同一個配對案例，Opus 產出 17,957 字元 / 174 個 CSS 變數，GPT-5.6 只寫 2,508 字元 / 45 個變數，渲染品質還更好。
- 但設計偏向上「容易收斂到乾淨、網格化、稍顯 generic 的風格」，需要靠設計系統 steer。

**Step 1：Tool schema 設計要對付「模型會憑空發明參數值」**
- 他們的 code tool 有 25 個 top-level 參數（只有 action 必填）。Opus 只送用到的兩三個；GPT-5.6 每次送滿 25 個，沒用到的還會憑空填 `offset: 0`、`timeout: 120000`、`siteId: "00000000-..."`。問題是「憑空值」跟「真實意圖值」在外觀上無法區分——`offset: 0` 看起來就是合法參數；tool 兩種都回 success:true，模型根本不知道自己讀到空白檔。
- 結果：GPT-5.6 的檔案讀取有 52–64% 是空的，agent 用更多呼叫把事情做完但做得更差。
- Prompt 指令無效（「omit unused parameters」仍 25/25）。OpenAI strict mode 測了也一樣，而且會逼他們拿掉所有 schema 驗證。
- 真正有效的解法：在 provider boundary 做 schema transform——把 optional 屬性改寫成 required but nullable（`anyOf: [T, null]`），給模型一個「我沒用這個」的顯式表達方式；通過統一 seam 把 null 拿掉再驗證。檔案讀取空回應從 52% 降到 0%，同樣工作量少 30% tool calls。

**Step 2：Prompt caching 是兩個完全不同的設計哲學**
- Claude：用 `cache_control` 標記，整個 org 共享 prefix cache，命中率 92–96% 像背景設施。GPT-5.6：丟掉 partial-prefix matching，implicit caching 只對「整段 prompt」建立 entry，以最新一則訊息為 key。共用 29K 靜態 prefix 的新 conversation 直接 0% 命中。
- 設計上 GPT-5.6 改走 explicit：`prompt_cache_breakpoint` + 強制 `prompt_cache_key`。Key 是 cache identity 的一部分，相同 prompt 換 key 就是 0 命中。每個 key 約 15 req/min 後 OpenAI 會 fan-out 到獨立冷節點。
- 三種 key scope 取捨：per-conversation（首次命中 0%）、global（流量衝破 15 rpm 預算）、**per-workspace 是甜蜜點**——同 workspace 共用 entry、流量低。
- 修好之後：首次呼叫命中 0%→83.7%、uncached input tokens 降 28%、per-suite 成本降到 Opus 之下。他們強調「之前看到的整段成本差距全是 cache 設定問題，不是模型定價」。

**Step 3：Reasoning replay 要做成 self-contained**
- GPT-5.6 的 Responses API 預設用 server-side item reference replay 前期 reasoning；他們遇到對話中途出現 `Item 'rs_...' not found`。修法是設 `store: false`，讓 SDK 拿 encrypted reasoning content 改用自含 blob replay。副效果是 server-side reasoning 狀態在 loop 裡時，即使你送的 bytes 是 append-only，「有效 prompt」也可能在你不控制的地方改變。

## 3W1H 分析

- **What（做了什麼 / 主題）**:
  Ploy 工程團隊把生產 agent 從 Claude Opus 4.8 完整遷移到 GPT-5.6 Sol 的實戰紀錄：先修 eval harness 偏見、再修 tool schema（對付「發明參數值」）、再重建 prompt cache 策略（從 org-scoped implicit 改到 per-workspace explicit）、最後修 reasoning replay 的 self-contained 設計。四個步驟全部做完才把「看起來更好」的模型實際跑成 production default。
- **Why（為什麼重要）**:
  這是少數完整公開「同一家公司、同一個 agent 產品」跨模型遷移的成本／品質／工程落差數字——2.2× 速度、27% 成本下降不是空談。但更重要的是暴露了一個常被忽略的事實：**跨模型遷移不是 swap model string，而是要重做 eval、tool schema、caching、reasoning replay**。因為「模型」其實是 SDK + cache + tool 行為 + provider 預設的複合體，整個 stack 都會對 incumbent 默默特化。
- **How（如何運作 / 實作）**:
  - **Eval 公平性**：在跨模型比較前先 triage trace，把 harness 假設造成的失敗過濾掉；minScore threshold、parallel tool budget、batched file read 等都屬於「harness 偏見」要獨立修。
  - **Tool schema transform**：optional → required nullable (`anyOf: [T, null]`)；provider boundary 一個 seam strip null 再走原本驗證；保留下游 tool 程式碼不動。
  - **Cache key 設計**：scope key 到 workspace（不是 conversation、不是 global）；靜態 prefix 分層 breakpoint；接受「跨 workspace 共享 prefix 在 OpenAI 結構上做不到」的事實，把這個成本算進定價。
  - **Reasoning replay**：`store: false` + encrypted content blob，避開 server-side item reference 失效。
- **Insight（個人心得）**:
  這篇對主人近期讀的 agent harness 主題是最具體的「反面教材＋正解」。最值得記住的點：**「冷 cache 對比是比你的設定，不是比模型」**——把冷 cache 數字當模型成本差異會完全誤判遷移可行性。另一個是把 tool schema 當作「給模型的表達契約」而非靜態介面：當模型行為差異大到一定程度（會不會發明參數），下游整個 stack 都要為它改寫。這跟主人之前關注的 from-prompts-to-contracts 路線完全呼應——harness 不是「包住模型」的殼，而是跟模型共構的契約面。