# Harness the Memory: A Holistic Evaluation of Memory Substrates in Memory Agents
- 原始連結：https://arxiv.org/abs/2608.15008
- 閱讀時間：2026-08-19（午）

## 摘要

本文直面一個 agent 圈子至今仍含糊的問題：long-horizon LLM agent 真正需要的「記憶」到底該用哪一種底層 substrate？論文把 memory substrate 拆成七類：dense / sparse 索引、text records、structural stores、hierarchical stores、refinement-based memories、parametric updates、以及 activation-compatible context mechanisms。

**統一 harness 評測。** 作者用 controlled harness 跨 3 個 backbone 模型、4 個 benchmark suite（涵蓋 user-centric QA 與 agent-centric decision-making），量測 26 個 performance / efficiency 指標。刻意把「substrate」這個變因從模型、benchmark、prompt 等雜訊裡隔離出來——和 主人熟悉的 spec-driven / N-stage workflow 一樣，先把變因控制好再下結論。

**核心反直覺發現：「沒有銀彈」是真的。**
- **Broad retrieval 對 long-context factual QA 有利**，但對 sequential decision-making 是毒藥：過度檢索會把 attention 從 action-critical context 上拉走。
- **Scalability 引入新的 routing axis**——在中等 history 長度表現好的 substrate，到了更長 horizon 會變得昂貴或脆弱。
- 因此作者主張 **substrate routing 是 adaptive agent memory 系統的必要元件**，並提供 regime-aware 的設計指引。

## 3W1H 分析

- **What（做了什麼/主題）**:
  論文用 controlled harness 對 7 種 memory substrate 做 holistic 評估。變因控制做得相當乾淨——3 個 backbone × 4 個 benchmark × 26 個 metric × 統一 instrumentation——讓「substrate 本身對行為的影響」得以從模型/任務雜訊中剝離。這正是 主人挑選 numpy/pandas 而非 klayout 來驗 agent framework 的同一種 common-field 哲學：把變因控到最小，hypothesis 才會浮上來。
- **Why（為什麼重要）**:
  對做長期 agent 的人來說，記憶選型從來就是「看 benchmark 結果再挑」的猜謎遊戲。這篇 paper 把猜謎變成 routing 問題：根據 regime（QA vs decision-making、history 長度）動態挑 substrate。「沒有單一 substrate 永遠 dominate」這個結論本身就是 contribution——它把記憶設計從「選最強的」變成「選最合適的，並且知道何時切換」。
- **How（如何運作/實作）**:
  - 把 7 種 substrate 抽成可替換介面，讓同樣的 agent loop 可掛不同記憶底層
  - 26 個 metric 同時量 performance（accuracy、F1）與 efficiency（latency、token cost、index size、retrieval overhead）
  - 在 user-centric QA 上看 broad retrieval 的好處，在 agent-centric 決策任務上看 retrieval 過度的注意力偏移
  - 提出 **substrate routing**：根據 regime + history length 動態選擇 substrate
- **Insight（個人心得）**:
  這篇 paper 對 主人最有用的一點是它把「記憶 = 選一種就 commit」的心態打掉。Hermes / horo-agent 目前 session memory 是 single-substrate 設計——碰到 long-horizon 多任務時，可能就是這篇 paper 警告的「moderate-history 表現好、longer-horizon 變 brittle」那一型。如果要在下游 lite 版本上做精簡而不重寫 agent loop，這個 paper 的 substrate routing 框架正好是「保守加法」的位置：保留現有 memory 介面，在 routing 層做 regime 判斷，不動 core runtime。對 主人 air-gap / enterprise-lite 的硬規則而言，是少數能直接採納的具體設計。
