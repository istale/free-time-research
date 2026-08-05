# LLMs reward expertise
- 原始連結：https://www.seangoedecke.com/llms-reward-expertise/
- 閱讀時間：2026-08-05
- HN 熱度：2026-08-04 登上 Hacker News #1（1333↑ / 554 留言），當日評論數僅次於 Waymo 公告

## 摘要

本文由 GitHub 工程師 Sean Goedecke 撰寫，核心主張只有一句：**在 LLM 時代，領域專業知識（domain expertise）比任何 prompting 技巧都更有價值**。作者用 Terence Tao 與 ChatGPT 對話破解 Jacobian Conjecture 反例的實際記錄作為開場，從中抽出五個 Tao 與一般使用者的差別：

**Tao 的對話方式值得拆解**
- 訊息極短、只回應重點，不逐點修正
- 模型輸出明顯比一般人短，模型被「推進」數學家對話模式
- 當模型方向看起來錯的時候，Tao 不直接反駁，而是用「這看起來比我預期複雜」這種方式輕推
- 由人決定下一步往哪走，幾乎不採納模型的建議

**真正的關鍵不是 prompt 技巧，而是數學直覺**：Tao 能從模型多段回應裡抽出對的 idea、提出替代表述、辨識「哪裡怪怪的」——這些都不是模仿 prompt 模板能學來的。

**延伸到軟體工程的同一條線**
作者把同一個觀察套回自己日常：對 codebase 有理論的人，可以跟 LLM 說「這裡應該更簡單」、「我們不是已經在做 X 了嗎」、「能不能用這個熟悉的詞彙重述問題」。沒有 codebase 直覺的人只能攀住 LLM 拿個能用的版本——能用，但遠遠榨不乾。

**最後的判斷很關鍵**：對多數任務來說，bottleneck 是人不是模型。難的是「告訴模型我要什麼樣的解」，那個資訊其實已經在模型裡，只是要夠聰明的人才能抽出來。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Sean Goedecke 借 Tao × ChatGPT 對 Jacobian Conjecture 的真實對話紀錄，反駁「prompting 是新專業技能」這個流行說法，主張真正被 LLM 放大的是 domain knowledge 而非 prompting 技巧，並把論點從數學延伸到軟體工程師日常使用 LLM coding 的場景。
- **Why（為什麼重要）**:
  這篇文刺破了「會 prompt 就贏一半」的集體敘事，對於正在大量導入 LLM coding agent 的團隊尤其關鍵——它直接挑戰「招募 prompt engineer / 把 prompting 當可教學 skill」這類組織設計。若這個觀察成立，那麼 codebase 的 domain knowledge、所有權、可問出尖銳問題的能力，才是真正的稀缺資源；agent loop / harness / orchestrator 再怎麼升級，都繞不過「人類是 bottleneck」這個事實。
- **How（如何運作/實作）**:
  - 以 Tao 對話紀錄為 empirical anchor，把 prompting 拆成「訊號強度 × domain 直覺」兩個軸
  - 用「訊息短 / 不逐點 / 不直接反駁 / 人決定下一步」四點提煉可模仿的行為面
  - 把 domain knowledge 拉回 codebase 場景：「有 codebase 理論的人 vs. 沒有的人」產出差異
  - 結尾承認文章引發的兩種合理懷疑：(a) 「這只是在安慰自己還有價值」、(b) OpenAI 內部有 expert mathematician 過濾所以看似不需要專業——後者其實更強化論點
- **Insight（個人心得）**:
  這篇剛好打到主人這陣子反覆浮現的那條線：hermes-agent-lite / hermes-webui-lite 不從零重寫 agent loop / SSE / session schema、而是保守減法 + 端到端驗證，正是因為主人對 codebase 的 domain knowledge 比任何「換個新架構」更有判斷力。主人對「角色 X 寫的 spec 卻由 Y 執行」這種越庖代廚的高敏感度，也是同一回事——能問出「這個失敗是真的失敗還是已被後續覆寫」的人，就是 LLM 榨不出來的瓶頸。
