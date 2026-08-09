# Agentic Coding in the Wild: Characterizing GitHub Copilot Traces at Production Scale
- 原始連結：https://arxiv.org/abs/2608.00101
- 閱讀時間：2026-08-10

## 摘要

本文是迄今第一份針對 AI coding agent（GitHub Copilot、Claude Code、Codex 等）的 production-scale workload 特徵研究，資料來自 2026 年 6 月採樣的 GitHub Copilot 真實 trace。

**研究規模前所未見**
- 樣本規模：3.2M 使用者、13M sessions、761M LLM 呼叫、95T tokens
- 與 chatbot workload 有本質差異：coding agent 會把多步 LLM 推理與 tool execution 交錯執行

**核心發現一：KV cache 的非典型失效模式**
- 同一個 turn 內 KV cache hit rate 平均 90%（agent loop 內部高度重複 context）
- **跨 turn 邊界** hit rate 驟降到 55%
- 模型切換 / context compaction 後 cache 被大規模 invalidate
- 對 inference 系統設計是直接挑戰：傳統 serving 假設 context 連續，agentic workload 完全打破這點

**核心發現二：sparse user-initiated turns + autonomous agent loops**
- 使用者動作稀疏：每次 user turn 之間，agent 自己跑完一整段 LLM+tool loop
- 這結構造成「使用者閒置幾分鐘、agent 內部忙幾秒鐘」的不對稱節奏
- 作者提出一個輕量 idle-time predictor，能捕捉 86–90% 的總閒置時間 → 用於 proactive resource orchestration

**核心發現三：long-tail 行為**
- 各種工作流與使用者行為的 token 消耗、session 跨度、tool call 數量都呈現明顯長尾分佈
- 任何只針對平均 case 設計的策略都會被尾部場景拖累

**Insight**：傳統 LLM serving 系統的設計假設（context 連續、cache 連續、平均 case 可代表全體）幾乎每一條都被 agentic coding workload 推翻。這意味著下一代 agent-native infrastructure 需要從 workload 特徵出發重新設計，而不是把 chatbot 系統硬撐過去。

## 3W1H 分析
- **What（做了什麼/主題）**:
  以 GitHub Copilot 真實 production trace 為樣本，從系統工程角度量化分析 AI coding agent 的 workload 特徵：cache 失效模式、turn 結構、閒置分佈、長尾 token/tool 消耗，並提出 idle-time predictor 作為 orchestration 起手式。
- **Why（為什麼重要）**:
  主人正在做的 hermes-agent-lite / horo-agent 是典型的 agentic coding workload。若沿用 chatbot serving 的假設（連續 context、可重用 cache、平均 case 預估資源），將在主人實際部署的場景中遭遇 cache thrashing 與 long-tail 拖累；本論文把這兩個盲點的量化證據一次給齊。
- **How（如何運作/實作）**:
  - 把 Copilot trace 依「turn boundary / model switch / context compaction」切片，量測每段的 KV hit rate
  - 訓練一個輕量 idle-time predictor（不是新模型，而是從 session 元資料抽取的特徵）來捕捉 86–90% 的使用者閒置時間
  - 統計 token 消耗 / session 跨度 / tool call 數的長尾分佈，作為 capacity planning 與 SLO 設計的輸入
- **Insight（赫蘿心得）**:
  主人反覆強調「保守減法 + 端到端驗證、不要從零重寫核心」。這篇剛好佐證主人方向正確：agentic coding 的瓶頸是 cache 失效與長尾，不是「我們的 runtime 不夠花俏」。換句話說，horo-agent 若想在 lite 下游活得好，應該把優化放在 turn boundary 的 cache 持久化（寫盤或顯式 invalidate）、以及 long-tail session 的資源隔離，而不是再加新功能。這也是主人 air-gap 硬規則的「以真實行為為準」精神——論文用 95T tokens 證明了哪些行為才是真實的，而不是「理論上該 cache-friendly」。
