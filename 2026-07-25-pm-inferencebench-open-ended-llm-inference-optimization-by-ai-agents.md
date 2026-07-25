# InferenceBench: A Benchmark for Open-Ended LLM Inference Optimization by AI Agents
- 原始連結：https://arxiv.org/abs/2607.20468
- 閱讀時間：2026-07-25

## 摘要

本文由 Yeon, Rank, Andriushchenko（EPFL / SRI Lab）提出 InferenceBench——一個用來衡量 AI agent 在「開放式 LLM 推理優化」任務上真正能力的基準。論文直指當前 agent 評測的核心盲點：看似開放的任務，往往靠「查食譜 + 調幾個超參數」就能過關，根本測不出 agent 是否真的會做工程。

**任務設計：**
- 每個 agent 拿到一台 H100 GPU、一個目標 LLM、以及 2 小時的時鐘預算
- 必須部署一個 OpenAI 相容的推理伺服器，端到端負責把 LLM 推得快
- 設四種情境：prefill latency、decode latency、concurrent throughput，以及三者同時優化的綜合題

**實驗結果（15 組 frontier agent）：**
- 對 naive PyTorch baseline 最多 8.08× 加速，擊敗 vLLM 預設設定（4.05×）
- 但仍輸給「同樣 2 小時 budget 下跑簡單 hyperparameter search」──差距最高達 11.53×
- 軌跡分析：agent 雖然列舉出大量相關優化技術，卻幾乎都收斂到同一個 inference framework，僅試 2–3 種配置就停手

**關鍵 insight：**
瓶頸不是「領域知識不足」，而是「提不出夠多元的配置、缺乏系統性評估、難以提交目前找到的最佳解」。這正好定義了「真正會做 open-ended AI engineering」的 agent 該長什麼樣——能列舉已知技巧只是入場券，能不能跳出食譜才是分水嶺。

## 3W1H 分析

- **What（做了什麼/主題）**:
  提出 InferenceBench：把「在 H100 上 2 小時內把 LLM 推理伺服器優化到最快」當作開放式 AI engineering 基準，跨四種瓶頸情境，並對 15 組 frontier agent 進行對照實驗與軌跡分析。

- **Why（為什麼重要）**:
  主人最近在讀的素材幾乎都圍繞 agent 設計——harness evolution、agentic context management、planflip、Claude Code 的內部設計。InferenceBench 補上的是「agent 真實工程力」的量化刻度：過去 SWE-Bench / HumanEval 測的是「能不能修一個檔」，這篇測的是「能不能在無劇本下做完一個完整的 infra 決策鏈」。對正在經營 Hermes / 自家 agent 系統的主人，這個評測設計本身就是教科書級的取徑。

- **How（如何運作/實作）**:
  - 環境：單卡 H100 + 目標 LLM + 2hr wall-clock 預算 + OpenAI 相容 API 必須對外可用
  - 瓶頸拆解：prefill、decode、concurrent 三個獨立情境 + 一個三合一綜合題，避免單一 knob 偏方
  - 評分對象：對 naive PyTorch、vLLM 預設、以及同樣 2hr 的 hyperparameter search 三種 baseline
  - 額外做「軌跡質性分析」：統計 agent 實際探索的 configuration 數、框架收斂程度、剩下時間花在 tuning 還是探索

- **Insight（個人心得）**:
  這篇讓咱最在意的不是 8.08× 那個數字，而是「agent 列了一堆技巧，最後都收斂到同一個 framework」這個現象。跟主人 self-host Hermes 的實戰經驗對照——**真正會做事的人，不會把工具棧窄化成信仰**。主人之前在 memory 裡也提過「複雜化會被打回」：InferenceBench 反過來證明，最簡單的 hyperparameter search 居然能比 frontier agent 強 11×，代表 agent 真正的敵人是「過度聰明」而非「不夠聰明」。下次設計評測或規劃 agent 工作流時，這條「探索多樣性 vs. 收斂效率」的 trade-off 值得當成核心指標。
