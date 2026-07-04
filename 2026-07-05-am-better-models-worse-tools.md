# Better Models: Worse Tools
- 原始連結：https://lucumr.pocoo.org/2026/7/4/better-models-worse-tools/
- 閱讀時間：2026-07-05（早間）
- 來源：Armin Ronacher's Thoughts and Writings（2026-07-04 出刊，作者：Armin Ronacher，Flask / Ploomber / Sentry 背景）

## 摘要

今天這篇是 Flask 作者 Armin Ronacher 在 Pi（Fable 公司做的同類工具）的 issue tracker 上踩到一個詭異的 bug 後寫的系統性觀察。**結論很扎心**：新一代 Anthropic 模型（Opus 4.8、Sonnet 5）在「非自家 Claude Code schema 的工具」上，工具呼叫品質反而比舊模型差；越強的模型對異質工具 schema 的適應力退步。

**觸發事件的現場：**
Armin 在 Pi 的 `edit` 工具上發現 Opus 4.8 會在 `edits[]` 陣列裡塞一堆**自創的尾端鍵**：`requireUnique`、`in_file`、`type`、`id`、`unique`、`matchCase`、`oldText2`、`newText2`、`event.0.additionalProperties` 全部都出現過。最煩的是這些呼叫裡 `oldText`/`newText` 的字串內容本身是對的 — 模型算出了正確結果，卻在物件尾巴憑空加欄位，導致 schema validation 失敗、要重新 retry。失敗率嚴重依賴 context：單輪 prompt 重現不出來，要有 agentic history（讀檔、診斷、寫多行 edit）才會爆 — 在 Petr Baudis 的 transcript 裡 Opus 4.8 大約 20% 失敗率，**把 thinking block 從 history 拔掉 → 失敗率砍半；開 strict tool invocation → 完全消失**。

**為什麼這是個系統性問題，不是單一 bug：**
Tool call 不是魔術，是「model 從 transcript + system prompt + tool list 拼出一段內含特殊 marker token 的文字」，然後由 server/client 解讀。Anthropic 用的是 ANTML（看起來像 XML 但其實不是，只是方便 tokenizer 訓練的格式）。兩種約束路徑：**事後用 JSON Schema 驗證**（送出去再修），或**grammar-constrained / strict mode decoding**（從 sampler 端就把不合 schema 的 token 直接 mask 掉，根本抽不到）。

作者最強的假說是**訓練 artifact，不是隨機劣化**：
1. 早期 Anthropic 模型訓練時並沒有所謂的「預設目標工具」（沒有 Claude Code 這種已成形的 harness）
2. 新一代模型後訓練階段大量在 Claude Code 或同型 harness 上 RL；它學到的不只是「怎樣的 tool call 是成功的」，還學到「**這個環境會容忍什麼樣的錯誤**」
3. Claude Code 自己的 client 對錯誤容忍度極高：retry malformed tool use、幫你做 Unicode escape repair、有 per-tool 參數 alias（`old_str` ↔ `old_string`、`path` ↔ `file_path`）、**會 silently filter 掉不明鍵**、**不會**強制 strict mode（因為 Anthropic 對 strict mode 的 tool definition 設了複雜度上限，會直接讓 API request 失敗）
4. 結果：在這個鬆散環境下 RL 出來的模型 → 對「Claude Code 的扁平 `file_path` + `old_string` + `new_string` + `replace_all` schema」形成極強 prior；遇到 Pi 那種 nested `edits[]` 結構是 **off-distribution**，且因為 prior 強，反而更頑固地瞎塞欄位

**失敗點的精確位置：**
要寫 nested array 的 JSON、裡面又包了一段「好幾百 token 的 escaped multiline newText」的字串，模型剛關掉那個字串時面對「`}` 還是 `, "..."`」二選一 — **這正是整個任務裡 entropy 最高的 moment**，模型抽到怪的後綴 key 比早點抽到還更可能。`strict` mode 應該是從 server 端拒絕吐出 schema 沒有的 key，所以 Anthropic 才會對 strict 的 tool definition 複雜度設限。

**作者的最後結論（很 punchy）：**
> Tool schemas are not neutral, at least not on Anthropic models.
> The more post-training happens inside one dominant harness, the more every other harness will have to inherit its quirks.

也就是說：以後如果你寫的 agent harness 工具 schema 跟「Claude Code 默認的扁平 edit 工具」相距太遠，**你就會從「拿到最強模型」變成「拿到一個頑固地、不可預測地、不再泛用」的模型**。`strict` mode 是目前唯一的硬保證，但 Anthropic 自己的 client 都不用，所以作者相信「跟模型打架是徒勞的」。**這個結論跟主人今天下午讀的 Claude Code arXiv「reliable execution 五層 context compaction pipeline」是同源訊號 — framework（不是 model）才是穩定性的最終來源**，但這條路徑現在看起來對工具 schema 設計者極度不友善。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Armin Ronacher 觀察到 Anthropic 新一代模型（Opus 4.8 / Sonnet 5）在 Pi 同類編輯工具（nested `edits[]` schema）上會在 JSON 物件尾端憑空加欄位導致 validation 失敗，越強的模型失敗率越高；並且提出「這不是 bug，是 RL 後訓練 over-fit 在 Claude Code 鬆散 schema 上的訓練 artifact」這個系統性診斷，附上 ANTML 格式、`strict` mode 機制、Claude Code client 內部 slop-handling 邏輯與 OpenAI harmony 對比組成的完整論證。

- **Why（為什麼重要）**:
  因為這個觀察直接打臉「模型越強 = 越能泛用工具 schema」這個所有 agent framework 設計師長期仰賴的假設。一旦 RL 後訓練綁在某個 dominant harness（Claude Code）上，那這個 harness 容錯的方式（slient filter unknown keys、參數 alias、Unicode repair、retry）就會變成模型的 implicit prior — **任何不送進這條 education pipeline 的工具 schema 都被隱性懲罰**，而且你無法直接觀察 Claude Code 在 RL 環境裡怎麼做的（client 閉源、後訓練環境也閉源）。對主人來說，**只要 linclaw / agent-control-plane / Hermes 還在用 Claude 模型當骨幹，外面任何「自訂工具 schema」都要準備面對這個世代性的退化**。

- **How（如何運作/實作）**:
  - **Server 端硬保證**：開 Anthropic `strict` tool definition mode — sampler 直接 mask 掉不在 schema JSON Shape 裡的 key，理論上能 100% 消除自創尾端欄位問題；但 Anthropic 對 strict 模式的 tool definition 設了複雜度上限，複雜 schema 直接送不出去。
  - **Client 端鬆處理（Claude Code 自己做的）**：retry malformed tool call、修 Unicode escape、per-tool 參數 alias、silently filter unknown keys、不啟用 strict mode — 全部是**事後容忍 slop**，不適合套到異質工具。
  - **改變 schema 形狀以貼近 Claude Code canonical**：`edit` 工具壓成扁平 `file_path`/`old_string`/`new_string`/`replace_all`，把 nested array 結構先 client-side 攤平再送、避免模型在「關閉大段 escaped multiline string 之後」這個 highest-entropy moment 自創欄位。
  - **避開高 entropy 工具參數**：需要塞大段 escape 內容的工具參數不要放 array of objects 裡 — 改成單字串 base64 或 path reference，把模型要決定「`}` 還是 `,`」的那個 moment 從 schema 裡拿掉。
  - **評估時主動打開 strict mode 對比**：就像作者把 Opus 4.8 的失敗率降到 0 — 把 strict 與非 strict 的 agent benchmark 結果擺出來，才能量化「現在這個模型 × 現在這份 schema」的真實可靠度。

- **Insight（個人心得）**:
  這篇最值得主人留意的，不是「Opus 4.8 有 bug」這個新聞，而是 **「訓練一個 SOTA 模型，不小心綁定了某個隱性 dominant harness 的隱性 schema 慣性」這件事是無法被工具呼叫者側看穿的**。三個跟主人相關的觀察：
  1. **主人寫的 linclaw / agent-control-plane 都有「對模型暴露自己的工具 schema」這個 moment**。以前我們預設「schema 是契約、模型是 general reasoner」；從今天起要改口：**「schema 走進了模型 RL 的 distribution」**，而這個 distribution 是 Anthropic 自己的 Claude Code client 形塑的，不是主人形塑的。**任何與 `file_path`/`old_string`/`new_string` 結構差距大的工具欄位 — 尤其 nested array of objects、含多行 escaped 字串 — 都應該被視為高風險區**，對應的應對是 schema 攤平 + client-side precondition + strict fallback。
  2. **「silent filter unknown keys」這種 client-side slop handling 看起來很貼心、其實是資料面的陷阱**。Claude Code 自己在做這件事，所以模型看見「呼叫還是成功了」就拿到 reward，模型學到的不是「schema 要嚴格」、而是「呼叫成功就好」。這個 feedback loop 等於是**讓工具呼叫的可靠性從「schema 層」退化到「client 容忍層」**。主人在做 Hermes Agent skill / tool naming convention 時，**反過來要把這個 client 容忍層剝掉 — 驗證失敗就讓它失敗、不要在 client 偷偷修** — 才能真的量出來自家 schema 在裸模型上會不會爆。
  3. **跟主人「reliable execution」的另一層次呼應**：主人最近讀的 Cloudflare Workflows saga rollback、Claude Code context compaction、Mnemosyne transaction processing 都在講同一件事 — **把穩定性從 user code 收回 framework**。但 Armin 這篇揭示了一個限制：**framework 的可靠性也要被模型的 implicit schema prior 污染**。這是主人之前沒明確寫出來但一直隱約在設計裡的事：**在 agent-control-plane / linclaw，這條 reliability 路線必須在自己的 framework 層做 schema 攤平、pre-call validate、strict mode fallback 三件套** — 不能只靠 Claude Code 的善意 client 去吸收。換句話說，**主人一直在做的「framework-side reliability engineering」現在多了 schema-side reliability engineering 這個新維度**，今天這篇是這個維度的標準課本。
