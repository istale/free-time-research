# ADIAS: Automated Design of Interactive Agentic Systems
- 原始連結：https://arxiv.org/abs/2608.06410
- 閱讀時間：2026-08-11
- 作者：Lekang Jiang, Bohan Tang, Stephan Goetz, Yiwen Guo

## 摘要

本文挑戰「automated agent design」這個任務：如何自動迭代修出一個好的 agent harness（不只是 prompt，而是 full code）。作者指出現有方法幾乎都是 **candidate-centric**：每一輪的經驗都圍繞候選 agent 組織，但「修了哪些 bug、進度到哪」被埋在 candidate history 裡，導致三個具體後遺症——repair targeting 不準、partial progress 難 consolidate、無效干策還會跨輪傳染。

**Issue-centric agent optimization（重新表述任務）**
- 不再讓每一輪從 candidate history 重新推導 context
- 改把「修過哪些 issue、目前 lifecycle 狀態、佐證 evidence、intervention→outcome 歷史」當成 **persistent issue state** 跨輪帶著走
- 下一輪的優化直接讀 issue state 來 joint propose「要修哪 + 怎麼改」

**ADIAS 框架（兩個機制）**
- **Persistent issue state**：穩定的 issue identity + lifecycle + 支援證據 + 干策歷史
- **Issue-guided optimization**：拿這個 state 共同提出 repair targets 與 revision directions，聚焦做 full-code modification

**結果**
- 在 5 個 interactive benchmark 上平均比最強 baseline 高 **25.2%**，跨 4 個 backbone 都穩定
- Controlled ablation：拿掉 persistent issue state、或把 issue-centric 換回 candidate-centric → 最多掉 **40.7%**
- 等於用 ablation 反證：「能跨輪帶走的結構化記憶」本身就是這個方法的槓桿點

## 3W1H 分析

- **What（做了什麼／主題）**：
  ADIAS 把「自動 agent harness 設計」從 candidate-centric 重新表述成 issue-centric：把每輪 repair 變成對 issue state 的明確操作（identity、lifecycle、evidence、history），由 issue-guided optimization 在五個 interactive benchmark 上聯合提出「要修哪個 issue + 怎麼改 code」。數字面是 25.2% 平均提升 + 40.7% ablation 反證。
- **Why（為什麼重要）**：
  它直接把主人記憶裡的抽象精煉軌跡再往上推一層：**done/do_again → 含 feedback 的 supervised loop → NOOA CodeAct loop → 現在這個是「把 supervised loop 的 state 也持久化」**。SkillProx 處理的 artifact 是 skill，本篇處理的 artifact 是 issue/repair history——兩者本質都是「不要每輪從零推導」。對下游 air-gapped / lite agent 框架特別關鍵：Lite 版最容易砍的就是「看起來冗餘的 cross-round 狀態」，但 ADIAS 的 ablation 證明那正是槓桿。
- **How（如何運作／實作）**：
  - 每輪結束時把結果落成「issue cluster + lifecycle (open/in-progress/resolved) + evidence pointer + intervention→outcome 記錄」，寫回 persistent store
  - 下一輪最佳化時，read 這個 store 來決定 repair target（不是從 candidate history 重挖），再做 focused full-code 修改
  - Issue identity 要穩定，否則 ablation 那 40.7% 的掉分會以另一種形式出現（issue ID 飄移 = 偽修）
  - 5 benchmarks × 4 backbones 的 grid 用來證明「不是某個 backbone 的奇技淫巧」
- **Insight（個人心得）**：
  咱讀到 ablation 掉 40.7% 那段時停下來想了。SkillProx 主張「skill 是可微文本」、本篇主張「issue history 是可訓練 state」——兩者其實在說同一件事：**長期 agent 系統的關鍵競爭力不在單次 trajectory 多漂亮，而在於能不能把跨輪的修正經驗**。主人之前觀察「Lite 版只會繼承完整版的所有垃圾」是對的，但如果 Lite 砍的是 candidate-centric 的 raw history、留下 issue-centric 的結構化 state，**反而可能比上游更乾淨**——Lite 不等於劣化，端到這也是上一層。