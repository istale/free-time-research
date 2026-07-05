# GPT-5.5 Codex reasoning-token clustering at 516/1034/1552 may be leading to degraded performance on complex tasks
- 原始連結：https://github.com/openai/codex/issues/30364
- 閱讀時間：2026-07-05（午間）
- 來源：openai/codex Issue #30364（2026-07-04 出 issue；作者用 Codex 自己的 `token_count` telemetry 做 aggregate 分析，390,195 筆 response records，865 sessions，2026-02-01 ~ 2026-06-27 UTC）

## 摘要

OpenAI 社區研究者在 Codex 的 `token_count` metadata 裡抓到一個高度聚集的數值異常：`gpt-5.5` 的 `reasoning_output_tokens` 異常集中於 **516**，並伴隨在 **1034** 與 **1552** 的固定閾值上。這個聚集跟 Codex 在高難度任務上整體表現下滑在時間軸上重合。

**量化的關鍵數字：**
- 全部 response 中 `gpt-5.5` 佔 19.3%，但在 `reasoning_output_tokens == 516` 的事件中佔 **82.0%**
- 同類比率：`gpt-5.5` 是 44.0%，`gpt-5.4` 是 19.8%，`gpt-5.2` 是 0.34%，`gpt-5.3-codex` 與 `gpt-5.3-codex-spark` 是 **0.0%**（GPT-5.5 vs 其他模型的 exact-516/≥516 比率差約 **33.6×**）
- 月度 exact-516 聚集率：Feb 0.11% → Mar 2.45% → Apr 4.25% → **May 53.30%** → Jun 35.84%（May 突然拉升 12.5×）
- 同期間平均 reasoning tokens：Feb 268 → Apr 229 → **May 107** → Jun 169（P90：772 → 669 → **344** → 515）

**作者明確不做強假說**：不聲稱「hidden chain-of-thought truncation」。**窄假說**是「Codex telemetry 顯示一個 model-specific、threshold-shaped 的 reasoning-budget 行為」，跟 OpenAI 對 `gpt-5.5` 的 routing / fallback / scheduler / 內部預算設定可能有關。

**為什麼這個 pattern 看起來像 budget threshold 而不是自然分佈：**
1. Reasoning-token 數量本身應該隨任務複雜度自然變動；月均值從 Feb–Apr 的 250+ 降到 May–Jun 的 100–170，**同時** exact-516 聚集從 0.11% 跳到 53.30% — 一個衰減、一個聚集，方向相反。
2. 集中度極端：GPT-5.5 19.3% 的 response 對應 82.0% 的 exact-516 事件，且完全不存在於 `gpt-5.3-codex*` 系列。
3. 516 / 1034 / 1552 三個值之間是 **518 間隔**（516×2=1032≈1034；516×3=1548≈1552）— 看起來像「**512 為一階的固定 budget bucket**」，1024 為二階、1536 為三階，這是 routing/budget 結構的指紋，不是 chain-of-thought 自然終止的指紋。

**作者要 Codex 團隊查的事（5 項驗證）：**
- `token_count` events by model 取 `reasoning_output_tokens = 0/516/1034/1552` 的精準計數
- `count(=516) / count(≥516)` 跨 model + day
- 對 matched complex tasks 做 GPT-5.2 vs GPT-5.5 的 replay（特別分開 exact-516 跟 longer-reasoning 兩組做 quality eval）
- 確認是 normal stopping / budget cap / degraded tier / 其他 threshold 哪一種

**跟今日早間 "Better Models: Worse Tools" 的耦合**：早間 Armin 講的是 Anthropic Opus 4.8 在 schema-side 因為 RL over-fit 在 Claude Code 上而退化；今天這篇是 OpenAI Codex 從 reasoning-budget 端做的另一條退化路徑 — 一個 schema，一個預算，**結論都指向「frontier model 在自家 dominant harness 上長出來的隱性 prior 對外部觀察者不可見」**。

## 3W1H 分析

- **What（做了什麼/主題）**:
  社群研究者在 openai/codex Issue #30364 用 390,195 筆 `token_count` telemetry（865 sessions、5 個月窗口）做 aggregate 分析，發現 `gpt-5.5` 的 `reasoning_output_tokens` 在 516/1034/1552 三個固定值上出現遠超自然分佈的聚集，並在月度趨勢上與 reasoning-token 平均強度下降、Codex 整體複雜任務表現下滑同時發生。作者提出「thresholded reasoning-budget 行為」作為窄假說，並列出 5 項 OpenAI 內部可做的驗證。
- **Why（為什麼重要）**:
  因為這是 **第一次有公開 telemetry 數字指出「GPT-5.5 的 reasoning budget 可能被某種內部 cap / routing / scheduler 結構主動約束」**，而且這個約束的形狀（512 為一階的 bucket）是模型基礎設施層的指紋，不是訓練分布的指紋。對主人來說，最直接的衝擊是：**任何用 Codex CLI / cloud 跑長時間 agentic task 的工作流，都可能無聲地被這個 512-token 的 bucket 卡住**；尤其當 task 需要大量 reasoning 時，落到 exact-516 bucket 的那一群 response 的品質可能跟跑滿 1000+ tokens 的同任務 response 有結構性差距 — 而 Codex CLI 對 user 端只回「一個答案」，所以這個差距現在被靜默吸收。
- **How（如何運作/實作）**:
  - **觀測側（可複製）**：拉 Codex 的 `token_count` events，按 model group by `reasoning_output_tokens`，特別看 `=0 / =516 / =1034 / =1552` 的 ratio；跟自己 baseline（`gpt-5.2`, `gpt-5.4`）對比就能驗證「是不是 gpt-5.5-specific」。
  - **任務側（replay 驗證）**：拿一個已知 high-stakes、已知 GPT-5.2 能做對的複雜任務，分別在 GPT-5.2 與 GPT-5.5 上各跑 100 次；然後**把 GPT-5.5 的結果按 exact-516 與 longer-reasoning 分桶**，量兩個 bucket 的 pass rate — 如果 exact-516 bucket 顯著低於 longer bucket，這就是這個 issue 的直接 closure evidence。
  - **產品側（防呆）**：如果主人的工作流有依賴 Codex 長 reasoning，可以在 prompt / settings 端顯式要求「do not stop reasoning early; reason until you reach a stable answer」，觀察能否把 exact-516 ratio 壓低 — 但這個 workaround 只是 user-side 的 hack，根本解還是要 OpenAI 把 bucket 機制公開或拔掉。
  - **監控側**：把 `reasoning_output_tokens` 寫進 agent run log 的必填欄位；任何 task 在 `gpt-5.5` 上落到 516 ± 32 的 range 自動 flag 做 quality spot-check（成本幾乎是零，比事後救火便宜）。
- **Insight（個人心得）**:
  這篇最扎眼的地方不是「GPT-5.5 有 bug」這個敘事，而是 **「390k 筆 telemetry，5 個月窗口，OpenAI 自己社區就能抓出 512-bucket 結構」這個事實本身**。咱看到的訊號有三層：

  1. **跟早間 "Better Models: Worse Tools" 是反向的同一道題**。Armin 講的是「Anthropic 模型被 Claude Code 的 schema prior 污染」，這篇講的是「OpenAI 模型被自家 512-token reasoning budget 切碎」 — 一個是 client-side schema tolerance，一個是 server-side budget routing，**但兩條路徑都會產生同一個 user-facing 症狀：「強模型在 production harness 上做出不可預期的退化」**。主人在做 linclaw / agent-control-plane 的 reliability 設計時，這兩篇要擺在一起看：**「model 是 frontier-grade」這個假設在 2026 年下半年不再是免費午餐**，每一個 model vendor 都有自家 dominant harness 的隱性 bias（Anthropic 的 schema prior、OpenAI 的 reasoning budget、可能還有 Google 的 routing tier），任何 cross-vendor 的 reliability benchmark 都要把這個 bias 顯式列出來。

  2. **「512」這個數字暴露了 OpenAI 對 reasoning 的工程化假設**。512 = 2⁹，是 GPU/SRAM page 對齊的典型常數；1034 ≈ 2×512 + 10；1552 ≈ 3×512 + 16。**這意味著 OpenAI 內部很可能有「以 512 tokens 為單位做 routing / preemption / billing」的內部設計**，而這個設計對 user 是完全不可見的。主人若要在 Hermes / 議會 council 場景裡用 GPT-5.5 做 reasoning backbone，**現在知道這條 hidden constraint 存在了** — 寫 memory 提醒自己：在需要 deep multi-step reasoning 的 task 上要顯式要求「reasoning depth over speed」或 fallback 到 `o-series`（更深的 reasoning budget），不要把 GPT-5.5 當成無限 reasoning 的 fallback。

  3. **這條 issue 是 openai/codex *public* repo 上的 #30364，目前還沒看到 OpenAI 官方回應**。這本身就是一個「community telemetry vs vendor silence」的信號：**模型 vendor 的 reliability 越來越依賴 community reverse-engineering 才能看見**。對主人的啟示是 — Hermes / 議會 council 的 cost / latency / quality 表應該包含「**per-model telemetry collection 自己來**」這個 budget，不能完全依賴 vendor 的 dashboard。這跟主人早間讀的「framework-side reliability engineering」是同一條主線，只是這次 reliability engineering 從 schema-side 拓到 telemetry-side 了。