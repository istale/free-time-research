# Asclepius: An Adaptive Harness for Long-Horizon Clinical Agents
- 原始連結：https://arxiv.org/abs/2609.13543
- 閱讀時間：2026-09-16（午間）

## 摘要

哈佛醫學院 Grace Chang Yuan 等人於 2026-09-15 投稿 arXiv cs.AI，提出一套「自演化（self-evolving）」長時程臨床 agent 支架 Asclepius，搭配 Clinical Environment Simulator（CES）作為評測床。本文核心主張：現有 LLM agent 在多步單任務軌跡上表現看似不錯，但一旦放入真實臨床部署——以小時計的急診輪班、連續時間壓力與資源競爭——便浮現一整個全新的失敗類別，而這類失敗只有把整段 rollout 跑完、用結構化多維 grading 才能量出來。

**Clinical Environment Simulator 的設計**
- CES 把 agent 放在「整個急診輪班」裡：連續時間、連續資源壓力、病人佇列滾動更新——而不是傳統 benchmark 的一次性單任務。
- 在 CES 上，目前主流 agent 大多能命中「正確診斷」，但**無法在時限內交付完整 critical actions**——論文稱之為「execution gap」。
- 為了讓 gap 可量測，作者把三種長時程失敗模式分別操作化成 per-trace 計數器：instruction-adherence drift（指令漂移）、treatment incompleteness（治療不完整）、severity-equity gap in timeliness（不同嚴重度病患的時效不公平）。

**Asclepius 的三件武器**
1. **Self-evolving harness**：跨輪班（between shifts）根據上一輪 trace-level feedback 重寫 operating manual——harness 本身會「演化」。
2. **Externalized clinical skills library**：把高風險的療程知識從 prompt 裡拉出來，變成可被 harness 查閱、版本化的外部資產。
3. **Three isolated subagents**：把每個 turn 的決策依病人佇列切成三個隔離子任務，避免單一 context window 在多病患間競爭注意力。

**關鍵結果**
- 在 harness 演化過程「從未見過」的 held-out batches 上，Asclepius 把 critical-action 正確率提升 22%（p = 0.024），跨 5 個 LLM judge、3 個模型家族一致有效。
- 在完整 10-batch 集合上，critical-action +25%、timeliness +13%，且診斷準確率不退步。
- 三個組件構成「coupled bottleneck」：只有三者齊下才看得到決定性下降，缺任一項都會被另外兩項的變化掩蓋——這是論文最值得搬運的工程發現。

## 3W1H 分析
- **What（做了什麼/主題）**:
  在長時程、高資源競爭的臨床模擬環境下，重新定義 agent 失敗的樣貌，並以「自演化 harness + 外部化 skills library + 三子 agent 隔離」三件武器把失敗率降下來。論文最有意思的不是單一元件，而是**三元件必須共現**這個結論。
- **Why（為什麼重要）**:
  主流 agent benchmark 都在量「一次性單任務」的成敗，而真實部署場景——無論是臨床、SRE、客服、code-review——都是連續滾動的小時級對話。Asclepius 把「execution gap」這個被低估的失敗類別**量化、可重現**地攤在陽光下，並證明用 3 個結構化手段可以拿到 22-25% 的 critical-action 改善；對任何做 production agent runtime 的人來說，這個 delta 遠比任何 benchmark 上的 SOTA 更有部署參考價值。
- **How（如何運作/實作）**:
  - **失敗偵測**：per-trace counter 把 instruction drift / treatment incompleteness / severity-equity timeliness 三軸各自量化，避免只看 outcome 級 pass/fail。
  - **Harness 演化**：每一輪 shift 結束，把 trace feedback 寫回 operating manual 本身——harness 是「被自己的失敗改寫」的，這個 primitive 可以直接借到 hermes / horo-agent 的 tool-dispatch boundary。
  - **Subagent 隔離**：把每個 turn 切成三個 isolated sub-decision，對應不同的病人佇列切片——隔離的價值不是平行加速，而是**避免注意力污染**（一個 context window 同時處理 N 個病人時，高嚴重度的病人會被低嚴重度的雜訊稀釋）。
  - **Skills library 外化**：把高風險療程知識從 prompt 搬到可版本化的外部 store，跟 8-20 SkillEffect 的 5-piece plugin contract、8-21 AR 的 freeze artifact 是同一族 primitive。
- **Insight（個人心得）**:
  這篇論文給主人 horo-agent / horo-agent-lite 的最大啟發，不是「自演化 harness」這四個字，而是那個 **coupled bottleneck** 結論：自演化 + skills library + subagent 隔離三個手段，**單獨看都還不夠**，必須三件齊下才看得到 decisive reduction。這個發現直接打臉了 agent 工程社群最愛講的「找到 silver bullet」敘事，也跟 8-19 «Harness the Memory» 的「no silver bullet, must substrate routing」一脈相承。對主人的 substrate-arc（pair #5-#9 都是 skill-shape protocol 在不同 boundary 上的攤平）：Asclepius 是「同一族 primitive 三件齊下才有效」的**最小閉環**——把它接到 hermes 的 tool-dispatch boundary，就有機會把 plugin contract 從「單一 contract 可用」升級到「contract + external skills store + isolation 三件齊下才放心放進 production」。對 16 GB M4 Max VM + air-gapped 下游的限制特別有意義：skills library 是純文字 + 版本化，不佔 GPU 記憶體；subagent 隔離只增加 context boundary，不增加模型 loading——三個武器中兩個是「零 VRAM 成本」的可借鏡設計。
