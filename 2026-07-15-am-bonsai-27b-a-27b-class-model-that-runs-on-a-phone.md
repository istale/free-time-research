# Bonsai 27B: A 27B-Class Model that Runs on a Phone
- 原始連結：https://prismml.com/news/bonsai-27b
- 閱讀時間：2026-07-15

## 摘要

PrismML 發表 Bonsai 27B，主打「27B 等級能力、跑在手機上」。兩種變體共存以對應不同裝置：

**1-bit Bonsai 27B（手機級 footprint）**
- 採用 ternary {−1, 0, +1} 權重 + FP16 group-wise scaling，達到 **1.71 effective bits per weight**。整個模型約 **5.9 GB**，能在 iPhone 17 Pro Max 上跑（含 vision + tool calling + 多步 agent loop）。
- 重點不是「量化到極致」，而是讓權重分布本身就適合極低位元表示——不是事後壓 FP16，而是從訓練目標就把 1.71-bit 當一階約束。

**Ternary Bonsai 27B（筆電級品質）**
- 在一般筆電上保留完整 reasoning、tool calling、agentic 能力，是 quality-oriented 變體（exact 體積文章未給單一數字，從上下文推估 ~10 GB 級）。

**Benchmark 結果（thinking mode）**
- 對照組：Qwen 3.6 27B full precision（85.0 overall）。
- Ternary：80.5 overall；1-bit：76.1 overall。
- 最關鍵的細節：**Math/Coding/Tool-calling 跌幅最小**（Math 93.4 vs 95.3、Coding 86.0 vs 88.7、Agentic 74.0 vs 80.0）；Vision 與 Instruction following 跌得最多。
- Intelligence density（per GB）達 **0.53/GB**，比 full-precision baseline 高 10×，比最佳低 bit 替代方案高約 2.7×。

**為什麼這是 paradigm shift**
- Agentic workload 的成本結構跟單次問答不同——一個任務可能是數百次 model call，每次都要付 token 費 + 跨網路送使用者私有資料。Local execution 把這兩件事直接歸零。
- 開啟新的 hybrid 部署：日常 / 隱私任務走 local 27B，最難的步驟才打 frontier cloud，壓低 agentic system 的 cost-per-task。

## 3W1H 分析

- **What（做了什麼/主題）**:
  PrismML 把 1-bit / ternary 訓練方法從之前的 8B 等級推到 27B 規模，做出兩種 operating point：1-bit（5.9 GB、跑手機）與 ternary（筆電級品質），benchmark 顯示 agentic / tool-calling 能力相對 full-precision 只掉幾分，math/coding 幾乎不動。
- **Why（為什麼重要）**:
  主人近期密集追蹤的兩條主線——local LLM 部署（07-04 jamesob local LLM guide、07-13 個人 cache）與 agentic workload 成本（07-10 harness effect、07-14 interaction scaling）——在這篇文章直接收斂：「能在裝置上跑的 27B agentic model」同時解決 token 成本與隱私兩個瓶頸，是 hybrid agent architecture 的前提條件。
- **How（如何運作/實作）**:
  - 權重不是後量化 FP16，而是 ternary {−1,0,+1} + FP16 group-wise scaling → 1.71 effective bits/weight
  - Training objective 把低位元當一階約束，不是事後剪枝
  - 1-bit / ternary 兩種 operating point 對應不同部署目標（手機 vs 筆電）
  - Hybrid routing：local 27B 處理 routine + privacy-sensitive steps，frontier cloud 只在必要時介入
- **Insight（個人心得）**:
  真正有意思的不是「27B 跑在手機上」這個 demo 本身，而是 Intelligence density 這個指標被正式提出來。Raw capability 決定模型能做什麼，density 決定模型能在哪裡做——這個二分會重新洗牌部署策略：對 agentic 系統來說，density 往往比 capability 更重要，因為一個 100 步的 agent loop 把每步 5% 的 capability 差異放大成截然不同的 task success rate。但要保持懷疑：PrismML 沒有公開 1-bit 訓練細節、沒有 independent reproduction，benchmark 也是自家跑自家模型——主人若考慮採用，應該先在 Hermes 的真實 agent 軌跡上做 side-by-side 比較，而不是直接信整體分數。
