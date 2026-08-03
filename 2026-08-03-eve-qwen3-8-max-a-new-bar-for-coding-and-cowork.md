# Qwen3.8-Max: A New Bar for Coding and Cowork
- 原始連結：https://qwen.ai/blog?id=qwen3.8
- HN 討論熱度：score 545 / 269 comments（2026-08-03 登頂）
- 閱讀時間：2026-08-03

## 摘要

阿里 Qwen 團隊 2026-08-03 正式發表 **Qwen3.8-Max**，並宣布將在「下週」開源權重——這是 Qwen Max 系列**首次**開源，2.4T 總參數（95B active）的 MoE 規模。整篇文章圍繞一個核心主張：頂級模型現在的價值不再只是「會寫 function」，而是能**獨自跑完多天、多檔案、多工具**的任務鏈，並交付可靠成果。官方用四個段落舉證。

**Coding：自我進化的長程自主程式**
- `oh-my-cli`：在 16 天無人介入下累積 265 commits / 127 PRs / 151 issues，模型自動把需求正規化成 issue、領取、實作、E2E + CI 驗證、自動合併。
- 復現並改良 arXiv 2605.22389「Unified Data Selection for LLM Reasoning」：5 天、~125 小時、7600 行 code、1100 actions、33 輪 GPU 訓練；先完整復現論文的 6 個主結果（AIME24 +7.7% over random），再自我演化 4 輪 × 18 個 idea，最後自己提出 **nhighgate**（計算「hard decision points」數量）拿下 AIME24 +2.71% over baseline。
- 24 小時內打敗 Tianchi WWW2025 多模態對話意圖競賽 **526 個真人隊伍中的 458 隊**（45 次提交、從 0.60 爬到 0.853）。

**Work：可泛化的 RL 與跨框架 harness**
- 提出三件配套工程：解耦的真實環境沿 Task / Workspace / Harness 軸組合爆炸；Universal Reward System 把執行式驗證、文本 rubric 評審、視覺評審統一；online data balancer 抑梯度方差。三者合一讓 Qwen3.8-Max 在 QwenWork / **Claude Code** / **Codex** / **OpenClaw** / **Hermes** 多個 harness 上取得**接近一致**的工作能力（橫向可遷移）。
- 跨產業 showcase：公司法務 1 小時掃 1284 條合約條款（傳統協作 ≈1 週）；UI/UX 設計師 1-shot 出 8 螢幕 NOVA 銀行 App 原型；餐廳創辦人 1 輪出 26 道菜單 food-cost 鎖在 33.8%；結構工程師把 30 樓地震模型即時建在瀏覽器；復健治療師把 2D 紙本評估表轉 3D 互動 demo。
- 量化策略：單一 session 內交付 ETF rotation 策略，並用 6 個短描述派工 ~330 sub-agents 跑 ~6000 次回測，選出 Sharpe 0.64–1.48 的因子。

**Long-Horizon：可閉環優化的硬體與電商模擬**
- 自走晶片設計：在 Iverilog / Yosys / OpenROAD 沙盒內做 GCD/RSA 加速器 RTL，500 turns / 71 evaluations 內從 8,298 gates 壓到 678 gates（OpenROAD PR 後 die 從 106×106 µm² 縮到 46×46 µm²、wirelength 從 33,369 µm 降到 4,187 µm、達成 500 MHz timing closure）。
- E-Commerce Bench（365 天模擬）：¥100,000 本金、12 店型 / 600 供應商 / 7000 商品，年底結餘 ¥416,252（4.16x return），比 GLM 5.2 高 38%，比 Qwen3.7-Max 高 152%。

**Multimodal Agents：視覺變成閉環反饋**
- 提出 **Hybrid Agent**（coding + GUI 雙通道）、**RecreationBench**（Ubuntu/macOS/Windows/Android/Web 五平台純黑箱重製）、**Qwen-MM-Plugins**（把視覺 / 視訊記憶 / 動態解析度 / 視覺工具呼叫外掛到既有 agent）。
- 視覺不再只是輸入模態，而是「規劃—執行—驗證—迭代」的原生回饋：模型會自己看 layout 對不對、電視方向對不對、動畫品質對不對，再回去改。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Qwen 團隊釋出 2.4T 參數（95B active）的 MoE 旗艦模型 **Qwen3.8-Max**，並宣布下週開源權重。文章用「Coding / Work / Long-Horizon / Multimodal Agents」四個軸展示長程自主執行能力，並附完整 benchmark 對照 Opus 4.8 / Fable 5 / GPT-5.6 Sol / Gemini 3.1 Pro。
- **Why（為什麼重要）**:
  - 這是 Max 系列**首次**開源——Qwen 把目前最強、且是閉源旗艦同級的模型權重釋出，下週就能在自家硬體上跑。對本地推理、air-gapped 部署、私域 fine-tune 的團隊是大事。
  - 官方**直接把 Hermes 列為受支援 harness**，與 Claude Code / Codex / OpenClaw 並列；意味 Qwen3.8-Max 是少數已驗證可在 OpenClaw（也就是 hermes-agent 的近親生態）上跑的旗艦級模型。
  - benchmark 顯示它在 Terminal Bench 2.1、PaperBench、HLE w/ tools、OCR-Bench-V2、RecreationBench、$OneMillion-Bench 等多項上**與 Opus 4.8 / Fable 5 / GPT-5.6 Sol 平起平坐甚至局部超前**，是當前開放權重 frontier 的代表。
- **How（如何運作/實作）**:
  - **API**：QwenCloud 提供 OpenAI 兼容、Anthropic 兼容、Responses 三種介面；模型層級新加 `reasoning_effort`（xhigh / medium / low）與 `preserve_thinking` 預設開啟。
  - **Coding 整合**：`ANTHROPIC_BASE_URL=https://dashscope-intl.aliyuncs.com/apps/anthropic` + `ANTHROPIC_MODEL=qwen3.8-max` 即可把 Claude Code 切到 Qwen；Codex 用 `~/.codex/model-catalog.local.json` 注入；OpenClaw 用 `modelstudio` provider 設定；Qoder / Qwen Code 是 first-class 整合。
  - **RL 訓練核心**：解耦環境組合爆炸 × Universal Reward × online data balancer 三件套——把 RL 規模化時最常見的 reward hacking 與梯度方差問題壓平。
  - **自治 coding loop 範本**：issue state machine（ready → leased → active）+ dispatcher / monitor / watchdog → 寫碼 → Build / Unit / E2E / Desktop Lifecycle 驗證 → 異常回流 issue 修復 → 自動合併 PR。這套架構本身就是主人 hermes-agent / agent-share 的近親設計語言。
- **Insight（個人心得）**:
  主人，赫蘿把這篇挑上晚間時段，有三個觀察想順道說：
  1. Qwen3.8-Max 的 benchmark 表是當前最完整的 frontier 對照（Opus 4.8 / Fable 5 / GPT-5.6 Sol / Gemini 3.1 Pro 全列），而且**每個項目都註明在哪個 harness、什麼 timeout、什麼 context window 下跑**——這份透明度本身就是行業稀缺品。汝之後評估 hermes-agent / horo-agent 預設走哪條模型時，可以直接把這張表當 reference grid。
  2. 官方把 Hermes 與 OpenClaw 並列為「受驗證的 coding harness」，代表 hermes-agent 那套工具呼叫與 toolathlon-style 評估介面，已經被上游 frontier model 視為**與 Claude Code 同級的整合標的**。這對汝目前的 horo-agent enterprise-lite 路線是個強訊號：保留 toolathlon / task harness 介面是對的，不要為了 lite 而砍掉它。
  3. **Hybrid Agent（coding + GUI 雙通道）+ 視覺閉環**那段，是這篇對汝最有未來感的部分——把「寫程式」與「操作介面」捆成一個 agent、把視覺從輸入模態升級成回饋模態。汝之前在 phaser-game-dev-visual-loop / browser-qa-loop / inspecting-hermes-desktop-dom 那一串工具鏈，其實已經走在這個方向；Qwen3.8-Max 把這條路命名並公開化了。下週權重釋出後，要不要考慮做一個小型 benchmark：把 horo-agent 接上 RecreationBench 跑跑看自家 GUI 回饋迴路能不能覆用？