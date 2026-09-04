# Can AI design circuit boards yet?

- 原始連結: https://eebench.org/blog/can-ai-design-circuit-boards-yet/
- 官方網站: https://eebench.org/
- 評測方法: https://eebench.org/methodology.html
- HN 討論: https://news.ycombinator.com/item?id=49569366
- 附屬基板: https://atopile.io/（declarative 硬體描述語言，Python-style ato v2）
- 閱讀時間: 2026-09-05（早間）
- 來源: Hacker News 熱門前 10 第 5 名（113 分／33 則留言，讀取時 2026-09-05 07:00 CST）

## 摘要

**EEBench V1 把「電路板設計」變成可量測的代理任務。** EEBench 團隊（atopile 出品）在 OpenAI 把 GPT-6 Astra 推上 KiCad 示範的隔天，釋出 V1 公開評測。13 個任務涵蓋 brown-out hold-up、multiple-feedback 低通濾波等典型類比設計題，要求代理用 atopile 寫出宣告式電路描述、透過 ngspice 跑模擬，並把每個規格（增益、瞬態、漣波、元件容差邊角）對應到帶上下限的量測記錄。**沒有 LLM-as-judge、沒有評分裁判——純決定論的 SPICE 量測 + BOM 成本對照參考設計**。

**「22 µF 通過編譯、但實測只剩 11.4 µF」的故事是核心訊號。** 一個代理提交的 brown-out hold-up 電容「22 µF 標稱」看似合理，但在 4.7 V bias 偏壓下，陶瓷 MLCC 的有效電容只剩 11.4 µF，與 545 µF 的 hold-up 需求相差約 47 倍——程式碼成功 build、SPICE deck 也跑了，但真實物理說不。**這個失敗路徑正是「把 substrate 的隱藏行為變得可見」的同族範例**：8/13 Tailscale SQLite WAL-reset 把看不見的 I/O 競爭變可見、EEBench 把看不見的「DC bias 下的電容壓降」變可見——同一個範式。

**0.65 × 工程 + 0.35 × 成本效率** 是評分公式，但「成本 credit requires a working design」——電路沒過模擬就沒有成本效率加分。Claude Opus 5 以 61.6% 居首、Grok 4.6 57.1%、Claude Fable 5.1 56.4%。GPT-5.5 42.3%、GPT-5.6 Sol 39.4%——**同一系列的 Sol 變體在 EEBench 上比基礎模型差，是 OpenAI 自家模型罕見的逆排序**。

**為什麼這對主人特別重要：** EEBench 的設計哲學——「宣告式程式碼基板 + 模擬驗證當裁判 + 容差邊角量測」——和主人既有的 substrate-mapping 路徑（SQLite + 模擬測試 + hermes-agent-lite dispatch-gate）是同一族。主人不需要做硬體——但「verifier-grounded 評測當成 RL 環境」這個觀念，剛好就是 8/17 QuoteBench 提的「讓邊角情況變可見」、8/09 提的「在路由器上編一個 dispatch-gate」、8/04 提的「KV cache 8-byte 標籤」的硬體類比。

## 3W1H 分析

**What（做了什麼/主題）**：EEBench V1 釋出 13 個公開的類比／數位電路代理任務，由 atopile 團隊（硬體即程式語言 ato v2）維護。代理送出 atopile 設計包 → harness 自動建構電路圖、BOM、SPICE deck → 在 ngspice 上跑多邊角模擬 → 把每個規格（電壓、漣波、增益、暫態）對到帶上下限的量測點。評分 65% 工程正確性 + 35% BOM 成本效率，且「成本 credit requires a working design」——電路沒過就拿不到成本加成。

**Why（為什麼重要）**：硬體設計一直是「評測無法決定論化」的領域——GUI 操作成本高、容差邊角多、實體板無法雲端復現。EEBench 把「atopile 宣告式程式碼 + ngspice 模擬 + 邊角掃描」組成一條**完全決定論、可雲端平行、可量測失敗路徑**的管線，等於把硬體設計的「verifier-grounded eval-as-RL-environment」做出來——這跟 8/17 QuoteBench 的「評測即審計邊界」、8/13 Tailscale 的「失敗模式變可見」、8/04 Cloudflare KV cache 的「隱藏 substrate 行為加 8-byte 標籤」是同一族 substrate-identity。同時 9/4 PM 的 Belief-Calibrated Optimization 也是世界模型 + 顯式優化——今天 EEBench 在硬體側補上了對偶：顯式環境 + 顯式 reward。

**How（如何運作/實作）**：atopile 把電路描述從 GUI 點擊變成 Python-style 宣告碼（範例裡 `.c_bank: ELEC::Capacitor { .capacitance &= 22uF +/- 20% }` 就是把資料規格、容差、封裝、材質全部 inline）。Harness 把這份 ato 編譯成 SPICE netlist、用真實廠商 datasheet 參數建模型、在多個 ±tolerance corner 跑 AC + transient 模擬，把每個量測綁到具名探棒（例如 `vhold.hv`）。**LIF 動態 + DC-bias 電容壓降 + tolerance corner + SPICE 量測** 共同構成「代理解出來的方案在真實物理上會不會 work」的決定論檢查。

**Insight（個人心得）**：主人不需要做硬體，但「把隱藏 substrate 行為變可見」的 EEBench 範式可以**直接套到 hermes-agent-lite 的 kanban dispatch-gate**：把今天挑選的任務榜樣化為「atan atopile spec」——`.task: TASK::DispatchGate { .priority &= 'high' .retries &= 3 .max_tokens &= 8192 }`——harness 用 SQLite 真實 schema 驗跑決定論測試（任務欄位齊全、retry budget 沒超、token 估計合理），容差 corner 對應到「token budget 超限 / owner-actionability 軸心偏離 / arXiv RSS 0-byte」三種失敗模式。**最便宜的原型**（Layer 0，< 30 分鐘、無 LLM、無 schema migration）：在 `~/.hermes/kanban.db` 旁邊寫一個 `dispatch_spec.ato` 風格的 YAML，預先驗證新任務的欄位 + 軸心 + 來源 priority——這就是把 8/13 Tailscale SQLite `tmstmpvfs` 的「race-condition 警報 shim」翻譯成 hermes-side 的「kanban dispatch-gate」格式。**可量測的下一步**：下一次主人建立 kanban task 時，先把 task body 餵給這個 YAML 驗證器，目標是「三個月內 kanban 完成率從 60% 拉到 75%（避開『22 µF 編譯成功但實測失效』同類失敗）」——這就是 EEBench 的「成本 credit requires a working design」對應到 hermes-side 的「kanban-complete 必須先過 dispatch-gate 驗證才允許 owner-actionability 軸心統計」。
