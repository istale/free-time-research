# GPT-5.6 Sol Ultra 證明 Cycle Double Cover 猜想
- 原始連結：https://cdn.openai.com/pdf/04d1d1e4-bc75-476a-97cf-49055cd98d31/cdc_proof.pdf
- 完整 prompt：https://cdn.openai.com/pdf/04d1d1e4-bc75-476a-97cf-49055cd98d31/cdc_prompt.pdf
- HN 討論：https://news.ycombinator.com/item?id=48863490
- 閱讀時間：2026-07-11

## 摘要

OpenAI 發布了一篇歷史性論文：GPT-5.6 Sol Ultra 用現成的推理模型，在約一小時內解開了由 Tutte、Itai–Rodeh、Szekeres、Seymour 等人提出的 **Cycle Double Cover 猜想**——一個懸而未決超過四十年的圖論經典難題。這篇文章同時把**完整的 prompt 公開**（連 prompt 的 scaffolding 都攤開），這在頂級實驗室裡幾乎前所未見。

**Cycle Double Cover 猜想是什麼**
對任何有限、無橋（bridgeless）、無自環（loopless）的多重圖，都存在一組 cycle 的 multiset，使得圖中**每條邊恰好被覆蓋兩次**。這個猜想被譽為圖論中與四色定理、Hadwiger 猜想並列的關鍵未解問題，自 1970s 起一直困擾數學界。Seymour 1981 年的 6-flow theorem 是最接近的結果。

**GPT-5.6 Sol Ultra 的證明骨架**
1. **Reduction to cubic graphs**：先利用 Jaeger 的觀察——最小反例必須是 snark（不可 3-edge-colorable 的 cubic bridgeless 圖），因此只需證明對 cubic 圖成立。
2. **Nowhere-zero F_2^k flow**：對 cubic 圖用 Seymour 的 6-flow theorem 取得一個 nowhere-zero 2^k-flow。
3. **Edge-to-pair-of-elements labeling**：把 flow 重新編碼為每條邊 e 配一個雙元素集 P_e ⊂ F，使得每個頂點 v 處的局部集合 {s ∈ P_e | e ∋ v} 構成剛好兩個元素的集合。
4. **Local-to-global lifting**：這一步是核心——用一個精巧的線性代數論證（dual vector space）證明局部的雙元素集配置可以**在邊上對齊**成全域一致的 cycle cover。
5. **Linear algebra closeout**：證明對偶空間裡的線性方程組有解，從而構造出完整的 cycle double cover。

**Prompt 工程本身就是寶藏**
公布的 prompt 揭示了 OpenAI 內部「multi-agent v2」的設計哲學：
- 強制多樣化 portfolio（不同 invariants、reductions、algebraic viewpoints、flow formulations）
- 對抗式審計 agent（檢查 exactly-two multiplicity、平行邊 2-cycle、cut vertex、bridges 是否被偷偷引入）
- 防止 agent 過早收斂到同一條路線（用「approach family registry」分群）
- 禁止公開搜尋這個猜想本身，只能查一般數學背景
- 「Spend at least 4 hours on this before even thinking of returning or giving up」

**為什麼這不只是又一個 AI 解題**
- 是**真開放猜想**，不是經典訓練資料裡反覆出現的問題
- 證明**非常簡潔**（整篇 PDF 主體不到三頁），暗示模型抓到了一個人類專家「漏掉」的 clever trick
- 公開 prompt 讓外界可以**重現整個研究過程**，這對 AI 安全與科學透明度意義重大

## 3W1H 分析
- **What（做了什麼/主題）**:
  OpenAI 用 GPT-5.6 Sol Ultra 解開 Cycle Double Cover 猜想——一個圖論裡與四色定理並列的長期未解問題。同時公開 prompt 工程細節（multi-agent orchestration + 對抗式審計），把整個研究流程攤在陽光下。證明主體極短，用 nowhere-zero flow reduction + 局部到全域的線性代數論證，把 40 年未解問題切成可被現成模型消化的子問題。
- **Why（為什麼重要）**:
  對主人這條技術軸線來說意義雙重：(1) **AI agentic reasoning 的真實 milestone**——不是「解了一題 IMO」這種封閉競賽題，而是「解了一個數學社群知道存在但沒人寫出證明的命題」；(2) **prompt 工程本身是可重現的研究 artifact**，主人常駐 LMStudio 與 coding-agent workflow，這份 prompt 是 multi-agent orchestration 在純研究任務上的最高品質公開樣本。對「enterprise agent token economics」（主人前幾天讀的 Harness Effect）也是直接佐證。
- **How（如何運作/實作）**:
  - **算法層**：cubic graph reduction → Seymour 6-flow → edge labeling → dual space 線性代數，三步內把猜想切成線代問題
  - **Prompt orchestration 層**：「multi-agent v2」管理 N 個並發 agent，每個跑不同 approach family；root agent 反覆 synthesize、redirect；對抗 agent 全程驗證 exactly-two multiplicity 與無 bridge 退化
  - **審計機制**：禁止公開搜尋 CDC 本身、禁止 return reduction-only / partial result、要求每個 candidate proof 都被對抗 agent 走過 exact-two、parallel-edge 2-cycle、cut vertex、bridge introduction 等失敗模式
  - **時間預算**：明確「spend at least 4 hours before returning」，迫使 agent 不在第一波失敗就繳械
- **Insight（個人心得）**:
  主人前幾天讀的「Harness Effect」講的是 enterprise agent 的 token economics；今天的 CDC 證明讓咱看到同一個原理的**研究任務版**——multi-agent orchestration 不是把一個聰明的模型放大，而是把「研究流程本身的紀律」自動化：對抗式審計、多樣化 portfolio、防止過早收斂、強制時間預算。HN 留言裡 sim04ful 提到「only 1/5 of the prompt is about the problem, rest is cajoling the harness into shape」——**這句話才是今天真正的新聞**：AI 突破純數學難題的故事，主角不是模型智商，而是 orchestration design 的成熟度。對主人的 LMStudio / coding-agent 工作流，這意味著瓶頸不再是「挑哪個模型」，而是「怎麼寫一份對抗式、多樣化、有審計的 prompt」。咱建議主人把這份 prompt 收進 `research/self-host-rp-platforms` 或新建一個 `agentic-math-research` skill——這是 2026 年公開能拿到最高品質的 agent 設計教材。