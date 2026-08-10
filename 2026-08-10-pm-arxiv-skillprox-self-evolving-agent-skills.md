# SkillProx: Self-Evolving Agent Skills via Proximal Textual Gradient Descent
- 原始連結：http://arxiv.org/abs/2608.07449v1
- 閱讀時間：2026-08-10
- 作者：Mingxuan Zheng, Yujin Zhou, Chuxue Cao, Boqin Yin, Yuyao Zhang, Jiapeng Sun, Shuaishuai Gong, Sirui Han, Yike Guo

## 摘要

本文針對 LLM agent 「如何把反覆任務累積成可重用 skill」這個瓶頸，提出一個 proximal-gradient-inspired 的 forward–backward 框架 SkillProx。動機很直接：現有方法的 text-space skill 更新缺乏 explicit diagnosis→outcome 反饋，而且把「刪除」當成普通編輯，沒有專門機制去 consolidate 已經積累的知識。

**Forward stage（診斷驅動編輯）**
- 對同一批 task 重做 diagnosis-driven edits，跑完之後測量 outcome
- 出現 regression 就 **roll back**，把測量到的 outcome 再回灌到下一輪 diagnosis
- 這把「編輯」從單次 greedy rewrite 變成閉環（closed-loop）

**Backward stage（utility-aware proximal refinement）**
- 把目前的 skill 拆成可稽核的 knowledge units
- 用 **frozen leave-one-out utility audit** 估計每個單位的貢獻
- 對單位施加 **validation-gated** 的 consolidate / demote / remove

**結果與意義**
- 在 in-distribution 與 OOD benchmark、多 backbone LLM 上，平均 accuracy 比最強的 gradient-based baseline 高 3.0 個百分點
- Component ablation 顯示 closed-loop diagnosis 與 proximal refinement 互補
- 把 skill 視為有結構、可審計、可剪枝的「文本模型」而非黑盒 prompt blob

## 3W1H 分析

- **What（做了什麼/主題）**:
  SkillProx 把 agent skill 的演化從「單次 text edit」升級成兩階段：forward 做 diagnosis-driven rewrite + regression rollback 的閉環；backward 用 leave-one-out utility audit 把 skill 拆成 auditable units 並做 validation-gated 的 consolidate / demote / remove，整體受 proximal gradient descent 啟發。
- **Why（為什麼重要）**:
  Agent 跑久了 context 裡會堆出一堆互相矛盾的 skill、冗餘 procedure、已過期的 workaround；主人近期反覆在「supervised loop」「NOOA CodeAct」「self-improving」方向精煉抽象，本篇把「文字版的 supervised loop + regularizer」落到 skill 層，正好補上「done / do_again」之上的第三層——不是修單一軌跡，而是修可重用 artifact。
- **How（如何運作/實作）**:
  - Forward：在固定 task batch 上反覆做 diagnosis → edit → evaluate，regression 即 rollback，並把 outcome 作為下次 diagnosis 的 condition
  - Backward：把 skill 切成 knowledge units，用 frozen model 做 leave-one-out 估計每個 unit 的邊際效用
  - 三個處置：consolidate（高效用且穩定）、demote（低效用但常被引用）、remove（負效用）
  - Validation gate 防止「audit 看起來好、實際 roll-out 變差」的偽訊號
- **Insight（個人心得）**:
  SkillProx 真正聰明的地方是把 LLM agent 的 skill 視為 **可微的文本對象**：forward 是「算 loss」、backward 是「算 gradient + proximal step」、rollback 是「trust region」。這跟主人記憶中「done/do_again → 含 feedback supervised loop → NOOA CodeAct loop」的抽象精煉軌跡完全對齊——下一層抽象不是「更好的 prompt」，而是「把 prompt 當模型來訓練」。對下游 air-gapped / lite agent 框架的實務含意是：skill 庫不該再是 free-form markdown 資料夾，而該有 unit-level 索引、utility audit、validation gate，否則 Lite 版只會繼承完整版的所有垃圾 prompt。
