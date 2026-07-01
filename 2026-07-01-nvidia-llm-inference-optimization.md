# Mastering LLM Techniques: Inference Optimization
- 原始連結：https://developer.nvidia.com/blog/mastering-llm-techniques-inference-optimization/
- 閱讀時間：2026-07-01

## 摘要

本文由 NVIDIA 官方部落格出品，深入探討 LLM 推理（Inference）過程中最關鍵的效能挑戰與實務解法。內容涵蓋：

**LLM 推理的兩個階段：Prefill 與 Decode**
- **Prefill Phase**：處理輸入 tokens，計算 K/V 快取矩陣。這是矩陣-矩陣運算，平行化程度高，能充分飽和 GPU 運算資源。
- **Decode Phase**：自迴歸生成輸出 tokens，一次一個。這是矩陣-向量運算，屬於記憶體受限（memory-bound）操作——瓶頸在於資料從記憶體搬到 GPU 的速度，而非運算本身。

**核心優化技術：**
1. **Batching（批處理）**：一次處理多個請求，分攤權重載入成本。傳統 static batching 會被最長請求卡住，in-flight batching 可在序列完成後即時替補新請求，大幅提升 GPU 利用率。
2. **KV Caching**：將已計算過的 Key/Value 張量快取在 GPU 記憶體，避免每生成新 token 都得重新運算，大幅降低重複計算開銷。
3. **Model Parallelization**：將大模型分散到多 GPU 叢集，分攤運算與記憶體負載。
4. **Model Optimization**：權重量化、剪枝等技術減少模型大小與計算量。

**重要 insight**：Decode phase 是優化重點所在——因為每個新 token 都需要參考所有先前 tokens 的 K/V 狀態，導致記憶體 bandwidth 成為主要瓶頸，而非運算力。優化方向應著重在減少記憶體搬運與提升 GPU 利用率。

## 3W1H 分析
- **What（做了什麼/主題）**:
  NVIDIA 深度解析 LLM 推理的兩階段機制（Prefill + Decode），並系統性介紹 KV Caching、In-flight Batching、Model Parallelization 等優化技術的原理與取捨。
- **Why（為什麼重要）**:
  LLM 推理成本是持續產生的「 recurring cost 」，動輒數十億參數的模型若未優化，延遲與費用會成為部署瓶頸。最佳化與未最佳化之間可差距 5-10 倍成本與 3-5 倍延遲。
- **How（如何運作/實作）**:
  - Prefill 階段：矩陣-矩陣運算，平行化容易，GPU 運算飽和
  - Decode 階段：記憶體受限瓶頸，需靠 KV Cache 避免重複運算
  - In-flight batching：序列完成即釋放，馬上補新請求，避免閒置
  - 可搭配 NVIDIA TensorRT-LLM 開源 library 實作生產級優化
- **Insight（個人心得）**:
  Decode phase 的 memory-bound 特性是理解 LLM 推理優化的關鍵鑰匙。多數人focus在模型大小或參數數量，但實際上「記憶體带宽」才是隱形殺手。這個認知對於評估部署硬體需求、選擇量化策略、以及理解 vLLM/TensorRT-LLM 等推理引擎的設計決策，都有根本性的影響。
