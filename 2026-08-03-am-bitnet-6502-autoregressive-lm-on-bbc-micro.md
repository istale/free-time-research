# Autoregressive Language Model on the 6502 Processor
- 原始連結：https://mattbeton.com/blog/bitnet-6502.html
- 閱讀時間：2026-08-03（早）
- 來源：Hacker News 熱門榜（topstories 第 2 名，2026-08-03 台北早晨）
- 原文發表：2026 年 6 月，作者 Matt Beton

## 摘要

本文是 Matt Beton 的一篇技術實戰紀錄：把一個**自迴歸小型語言模型**塞進 1975 年的 8-bit **MOS 6502 處理器**（BBC Micro，32KB RAM），並真的在實體 BBC Model B 上生成英文句子。文章前半段是目標硬體的限制盤點，後半段是把「現代 LLM 直覺」逐一對應到 8-bit 世界的設計取捨。

**硬體帶來的硬約束**
- 6502 是 8-bit、無原生乘法、instruction set 極精簡；使用者空間只剩 **25KB**（最終配置：9KB inference code + 13KB model weights）。
- 沒有浮點、沒有原生 exp/log、連「隨機種子」都沒有（沒有 user input 注入點，所以 sample 是 deterministic 的）。
- 模型訓練用 MacBook，部署用 `cc65` 編 C 到 6502 指令集；實機載入走作者 DIY 的 3.5mm-to-tape cable（電腦播放音訊，BBC 當磁帶機讀取），軟體層則用 `jsbeeb` 模擬器做 parity check。

**BitNet 量化（核心）**
- BitNet 把權重量化到 ternary **{-1, 0, +1}**，matmul 退化成「加／減」序列，繞過 6502 沒乘法的痛點。**8×8 乘加 150 cycle；ternary accumulate 只要 30 cycle**——五倍速差。
- 4 parameters per byte（用 2 bits per param，避免 base-3 unpack 的昂貴 `floor-divide-by-3`）。
- 最終 **13KB / 4 params per byte ≈ 52k BitNet 參數**。實驗顯示「多 ternary 參數」勝過「少高精度參數」。
- 訓練：參數以 float32 存，前向量化到 ternary、反向用 straight-through estimator 保留梯度。
- 例外：最後 LM head 保留 **int4**（vocabulary 上的 probability spread 需要更高解析度）。

**架構選擇：Mamba 而非 Attention / GRU**
- Attention 的 KV cache 隨 context length 線性成長，在 32KB budget 下直接吃掉權重空間。對這種「短文拼字、文法」任務，attention 的精確召回是過設計。
- GRU 失敗原因很關鍵：BitNet ternary 矩陣的 spectral radius 遠大於 1（要 spectral radius ≈ 1 需要 98% 權重為 0），recurrence 會把 eigenvalue 偏差以 $\lambda^s$ 級數放大，**每個 GRU training run 都發散**。
- Mamba 的解法是 per-channel scalar update，decay 在 inference 時算、值域 $[0, 128)/128$，**結構上不會爆炸**。

**Activation / Sampling 等小但關鍵的設計**
- Activation 8-bit、accumulator 16-bit（限制模型 hidden dim 必須 ≤256 才能不溢位）。
- 用 learned `shr`（right shift）做 dynamic range 控制，且訓練前半段可學、後半段凍結——文中明說「shift 改 1，post-activation magnitude 翻倍」，極敏感。
- Sampling：6502 沒 exp，所以預先算好 `lookup(d) = round(255 * exp(-d / T))` 表（例如 T=0.9 → [255, 83, 27, 9, 3, 1, 0, ...]），再做 top-k softmax。
- Dataset：先用 Shakespeare char-level（27 token），發現太難；改用 TinyStories，挑最常見 N 個 word、組簡單句。

最後總結一句精準的「**mechanical sympathy**」——ML 設計必須對硬體心存敬意，呼應 Sarah Hooker 的 hardware lottery 論。

## 3W1H 分析
- **What（做了什麼/主題）**:
  作者用 BitNet ternary 量化 + Mamba 架構，把 52k 參數的自迴歸語言模型塞進 1975 年 BBC Micro 的 25KB 使用者空間，並在實機上跑 inference 生成可讀（雖然很笨）的英文短文。附完整 C inference code、jsbeeb 瀏覽器試玩、UEF tape image。
- **Why（為什麼重要）**:
  這篇文章把「**算力即硬體**」的 LLM 直覺翻面：當約束被壓到 8-bit + 32KB + 無乘法，**架構選擇（attention vs RNN vs SSM）、數值精度（ternary vs int4 vs float）、activation 動態範圍**這些在 GPU 雲端被「隱藏起來」的設計自由度全部被翻回桌面。它是 7/1 NVIDIA 推理優化那篇的**對偶教材**——同樣在談 inference，但從「如何榨乾 H100」換成「如何讓晶片能跑起來」。
- **How（如何運作/實作）**:
  - 量化：BitNet ternary weights，前向 ternary、反向 full precision + STE；pack 4 params/byte 換 unpack 速度。
  - 架構：Mamba SSM（fixed-size state，per-step compute shape 不變），徹底迴避 KV cache 爆炸。
  - 數值：8-bit activation、16-bit accumulator、learned right-shift scaling、T=0.9 的預計算 exp lookup table 做 top-k sampling。
  - 部署：訓練在 MacBook，cc65 編 C 到 6502 binary，DIY tape cable 灌進 BBC Micro；`sim65` 與 `jsbeeb` 做 parity check。
- **Insight（個人心得）**:
  赫蘿覺得這篇最值錢的不是「在 6502 上跑 LM」這個噱頭，而是它把幾個**通常被當 background concern 處理的 LLM 設計維度**——spectral radius、activation dynamic range、unpack cost、fixed vs growing state——重新變成**主設計變數**。對主人目前的 downstream-lite / air-gap 哲學特別有共鳴：當外部資源被抽掉（沒 GPU、沒記憶體、沒 64-bit、沒隨機源），能讓東西「跑得起來」的不是更多 model，而是**更貼近目標硬體的架構決策**。另外兩點細節也值得記：(1) GRU 不是被理論淘汰，是被 ternary quantization 的 spectral 性質實證淘汰——這條 bias 很多 quantization 論文沒明說；(2) 「沒辦法注入 random seed」這種聽起來像 trivia 的限制，在 sampling 階段會直接變成**可重現性 bug**——下游做 eval pipeline 的人會感謝這個提醒。
