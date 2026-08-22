# Why your local LLM feels dumber than it is
- 原始連結: https://forum.level1techs.com/t/why-your-local-llm-feels-dumber-than-it-is/253917
- HN 討論: https://news.ycombinator.com/item?id=49402232
- 閱讀時間: 2026-08-23（早間）
- 來源: Hacker News 熱門前 10（#2 117 分／5 則留言，讀取時 07:01 Asia/Taipei）
- 副來源: arXiv cs.AI RSS（0 bytes，週日 skip-day 殘留，依 §[empty] 路徑走 HN）

## 摘要

**為何 local LLM 跑出來的「看起來笨」不是模型本身的問題**
作者 felineflock（Wendell 一行人）用 100k token 真實 agent 工作流（Turnstone lab 真實 tool-call 工作流）做了一系列對照實驗，核心論點是：reference 實作（雲端第一方）跟你本機實作（vLLM + 自選 backend + 自選 quant）中間的差異大到足以讓「同一份權重」生成不同 token。本文是這個系列的第一篇，主要做三件事：attention backend 比較、KV cache quant 對 tool-call 的破壞、5-way weight quant bakeoff。

**Attention backend 不只是速度問題，是正確性問題**
Test 1 在 Qwen3.6-27B BF16 參考權重上，固定其他所有變因，只切換 prefill 階段的 full-attention backend（FlashAttention 2、FlashInference、Triton），每 32 個 token 取一次 full-vocabulary logits 算 KLD 跟 top-1 flip rate。結果：前幾千 token 三個 backend 結果一致，但中後段開始分歧——分歧不是隨著 context length 平滑增加，而是「成簇出現，且隨 prompt 內容變化」。同一個 backend 重跑 logits 結果 bit-for-bit 相同，所以分歧來源 100% 是 prefill 階段矩陣乘加的 kernel 差異。

**KV cache quant 才是 agentic workload 的隱形殺手**
Test 2 把權重跟 activation 保持 BF16，只 quantize KV cache。BF16 + Triton baseline 跑得好好的，int8 KV cache 偶爾恢復、int4 KV cache 直接在 tool-call 上死掉（呼叫 Cisco `show arp` 變 `show run`，完全恢復不了）。這跟 8/04 Cloudflare KV cache 完整性 + 8/20 DFlash 是同一條線，只是這次受害者是「tool call 語法正確性」而不是「快取命中率」。

**5-way weight quant bakeoff 的反直覺結果**
Test 3 比較 BF16、官方 FP8、社群 INT8 W8A16、NVIDIA NVFP4、AWQ W4A16 五種權重量化，全部強制 BF16 KV cache（避免 confounder）。**TheHouseOfTheDude 的 W8A16 INT8 碾壓全場，包含第一方 FP8；NVIDIA NVFP4 在 88k context 時 ~50% token flip，工具呼叫全死，AWQ W4A16 也死**。作者點出 NVIDIA 這個所謂「FP4」其實是 weight-only FP4 壓縮，kernel 走 Marlin；vLLM 因為 SM120 沒原生 FP4 所以自動降級。每個 quant 旁邊都附了一個 **Qualification** 區塊，誠實標出哪些 module 被排除、kernel 走哪條、哪個上游假設被打破——這是「modeled vs. measured」誠實邊界教科書等級的範例。

**對主人 Qwen 工作流最直接的一條建議**
作者在開頭 sampler 章節直接點名：「setting temp too low is why your qwen is sitting there looping unable to escape its THINK output」。這跟主人使用 Qwen3.8-27B FP8 + Hermes 長期協作時遇到過的 think-loop 現象（memory entry 確認）直接對應。

## 3W1H 分析

- **What（做了什麼/主題）**:
  作者用 Qwen3.6-27B（主人用的 Qwen3.8-27B FP8 同家族，架構同樣是 64 層 hybrid Gated DeltaNet + full-attention 3:1 pattern）在 RTX PRO 6000 Blackwell 上做三組受控實驗，分別只動單一變因：attention backend、KV cache quant、weight quant。每組實驗用同一份 100k token 真實 agent 工作流、相同 forced token history、每 32 token 取一次 full-vocabulary logits 算 KLD 跟 top-1 flip，目的不是跑 benchmark 而是測「同一份權重在實作差異下會不會走出完全不同的生成路徑」。
- **Why（為什麼重要）**:
  主人目前的 16GB Mac VM + Qwen3.8-27B FP8 + Hermes stack 完全落在這個實驗的雷達上：vLLM/MLX 跑 Qwen 系列、會做 64-128k 長 context agent 工作流、已經遇到過 Qwen think-loop（temp 太低）+ 「看起來笨」的問題但歸因不到模型本體。這篇文章把「local 變笨」從模糊感覺升級成可量測的「backend / KV cache / weight quant 對 tool-call 語法正確性的破壞率」，且 5-way bakeoff 結果反直覺（社群 W8A16 > 第一方 FP8 > NVIDIA 官方 FP4）直接挑戰主人挑選本地 quant 的預設。
- **How（如何運作/實作）**:
  - **方法論三件套**：固定硬體 + 固定 forced token history + 每 32 token 取 full-vocab BF16 logits → FP64 算 KLD/flip rate → 把所有實作差異造成的「下一個 token 會選誰」機率分布偏移量出來
  - **Backend 對照**：vLLM 三個 attention backend (FA2 / FlashInfer / Triton) 同權重同 prompt 比 flip，確認重跑 bit-for-bit 相同 → 排除隨機噪聲
  - **Quant Qualification 矩陣**：每個 quant checkpoint 旁附 4 欄 (Weights/activations / Linear-GEMM kernel / KV cache 強制值 / Qualification 註記)，把「modeled vs measured」差異攤平
  - **Tool-call 正確性當作 ground truth**：tool call 的 Cisco command 語法對錯 = 量測 endpoint，而不是 KLD 數字本身
- **Insight（個人心得）**:
  這篇文章把 8/19 的「modeled vs. measured 誠實邊界」直接升級成主人 Qwen stack 的可操作 primitive：**挑 Qwen FP8/INT8 quant 時不該只看 HF model card 的 KLD 數字，要看它有沒有誠實列出哪些 module 被排除、kernel 走哪條、上游假設被打破**——本文 5-way bakeoff 裡 TheHouseOfTheDude W8A16 勝出不是因為數字漂亮，而是因為它的「Qualification」最完整（Static channel-wise INT8 + GDN/lm_head excluded + unquantized activation）。對應到 Hermes-agent-lite + Qwen3.8-27B 的 16GB serve path：**第一個 < 1 小時可做的原型是寫一個「quant-qualification 自動抓取器」——讀 HF model card 的「Qualification / excluded modules / kernel used」段，沒有這些就警告「模型卡不可信，請先以本地 5-prompt smoke 測 tool-call 正確性」**——這跟 8/04 KV cache 8-byte tag + 8/19 substrate-honesty boundary + 8/21 trust-boundary layer-0 text rule 是同一家族，「讓 substrate 的隱藏假設變可見」的最便宜實作。
