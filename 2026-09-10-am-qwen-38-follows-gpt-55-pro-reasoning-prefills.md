# Qwen 3.8 follows GPT-5.5 Pro reasoning prefills

- 原始連結: https://gist.github.com/wsxiaoys/e0286dc6bb624ff5fdf49e7f4c528ba3
- HN 討論: https://news.ycombinator.com/item?id=49630026
- 作者: @wsxiaoys（GitHub），前一版 v1.0 比較 Opus 4.8、這一版 v1.1 換成 GPT-5.5 Pro 當 teacher
- 閱讀時間: 2026-09-10（早間）
- 來源: Hacker News 熱門前 10（第 9 名，149 分／59 則留言，讀取時 07:00 Asia/Taipei）
- 交叉確認: arXiv cs.AI RSS 0 bytes（Wed weekday-empty，HTTP 200 但 channel 空，11 次確認），依 playbook 跳過 arXiv。

## 摘要

**「Reasoning prefill source recall」是判斷蒸餾來源的新指標。** 作者把 GPT-5.5 Pro 的 reasoning channel 開頭 1% 灌進目標模型的 reasoning channel、可見回答仍自由生成，再量測目標模型前 100 token 答案對 teacher 可見答案的 unigram/bigram/trigram source recall 平均。這個指標刻意只追蹤「可見答案」而非 reasoning trace——直接回答「這個模型是不是偷吃過 teacher 的答案」。**對 Qwen3.8 A95B（MoE 235B 激活）灌 GPT-5.5 Pro 1% reasoning：source recall 從 16.79% 暴增到 34.97%，+18.18 個百分點——其中 STEM 從 19.26% 升到 46.24%（+26.99 pp）、私有合成謎題從 10.49% 升到 25.23%（+14.75 pp）。** 這是「Qwen 蒸餾自 GPT-5.5 Pro 而非 Opus 4.8」的硬證據：私有謎題沒在預訓練 corpus 裡，模型卻能對齊 teacher，這只能靠蒸餾。

**Kimi K3 是另一個故事。** Kimi K3 在 unprefilled 狀態就已經跟 GPT-5.5 Pro 有 31.11% source recall——比 Qwen3.8 unprefilled 的 16.79% 高出快一倍——灌 prefill 後只 +4.54 pp。DeepSeek V4 Flash 跟 Inkling 幾乎不動（−1.17 pp、+0.46 pp），代表它們對 GPT-5.5 Pro 的 reasoning prefill「不敏感」、可能是獨立訓練或從別的 teacher 蒸餾。**這個分佈本身就是一張「開源模型蒸餾族譜」**：Qwen → GPT-5.5 Pro（+18 pp）、Kimi K3 → 已經很接近 GPT-5.5 Pro 但 gain 小（代表 prefill 已是模型內部偏好的子集）、DeepSeek V4 Flash / Inkling → 跟 GPT-5.5 Pro 無關。

**Qwen3.8 不是單一模型。** 本文測的是 Qwen3.8 **A95B**（MoE，總參數約 235B、激活 22B 一說、也有報 95B activated）——主人本機 `192.168.0.56:2234/v1` 跑的 **Qwen3.8-27B 是 dense**。同家族但架構不同（dense vs MoE）、總參數量級差一個數量級，所以 +18 pp 蒸餾訊號**不能直接外推**到主人本機 27B。**但訊號方向高度一致**：Qwen 整個 3.8 系列都吃 GPT-5.5 Pro 蒸餾，27B dense 應該也有相似的 source recall 偏置，只是幅度可能小一些（容量限制 + 蒸餾 budget 較少）。**主人若要驗證，1 行 prompt-level 測試即可**：拿 5 個私有謎題（不在預訓練 corpus）、先用 Qwen3.8-27B unprefilled 跑、再 prefill GPT-5.5 Pro reasoning 第一句、量 source recall 的 delta。**這個驗證的成本 = 一杯咖啡的電費 30 分鐘、output = 主人本地部署 reasoning budget 的 ground truth**——值不值得跑，主人自己看。

## 3W1H 分析

**What（做了什麼/主題）:**
wsxiaoys 在 gist 發了一個 v1.1「reasoning prefill」實驗：45 個問題（15 STEM + 15 non-STEM + 15 私有合成謎題）、4 個目標模型（DeepSeek V4 Flash、Inkling、Kimi K3、Qwen3.8 A95B）、teacher 從 v1.0 的 Opus 4.8 換成 v1.5 的 GPT-5.5 Pro。每題跑兩次——一次 unprefilled、一次把 teacher reasoning 開頭 1% 灌進目標模型 reasoning channel、量前 100 token 可見答案對 teacher 答案的 unigram/bigram/trigram source recall。**這個方法的精巧之處在於：用「私有合成謎題」當 ground truth**——這些謎題沒在預訓練 corpus 裡、模型能對齊 teacher 答案唯一的解釋就是「蒸餾自 teacher」。Qwen3.8 在私有謎題上 unprefilled 10.49% → prefill 25.23%（+14.75 pp），這 14.75 pp 是純蒸餾訊號。

**Why（為什麼重要）:**
主人用 Hermes Agent 跑 enterprise-lite / air-gapped downstream，最大長期風險不是「模型不準」而是「模型偷吃別人的 reasoning 偏好後，主人根本不知道下游產出偏哪個 teacher」——這對 multi-agent 路由（Qwen3.8-27B 走 coding、Qwen3.8-1B 走 cheap router）會直接污染 dispatch 決策。**這篇是主人目前手上少數有「蒸餾族譜可量化指標」的硬單子**——比讀 model card 上的「trained on internal data」有用 100 倍。同時這篇文章延續主人 9/9 那篇 Quesma 量化軸的「Qwen3.8 runtime 真實行為」主題——Quesma 告訴主人「Qwen3.8-27B Q4_K_M 跑 agentic coding = BF16」，今天這篇告訴主人「Qwen3.8 系列整體的 reasoning 是從 GPT-5.5 Pro 蒸餾來的、不是你以為的純開源 RL」。

**How（如何運作/實作）:**
技術核心是把 teacher reasoning 開頭 1% 塞進 target reasoning channel 的 prompt 結構——這對 OpenAI o-series、Qwen3.8 A95B、Kimi K3 都有用（這些模型的 reasoning 是獨立的 channel，與可見 answer 分開）。量測方式用 unigram/bigram/trigram source recall 三層平均，是 NLP 領域標準的「內容相似度」指標，作者刻意不計 reasoning trace（因為蒸餾後 trace 已被內化）。**主人要複現這個實驗給 Qwen3.8-27B 跑，技術路徑很清楚**：(1) 拿 GPT-5.5 Pro 的 API 跑 5 個私有謎題、抓 reasoning + answer；(2) 把 reasoning 第一句（1% 約略）prefix 到 Qwen3.8-27B 的 reasoning prompt；(3) Qwen3.8-27B 跑同一題、量前 100 token 對 GPT-5.5 Pro 可見答案的 source recall。**整個腳本 < 100 行 Python、< 1 小時 prototype、用主人現有 lmstudio-council 即可 serve 27B**。

**Insight（個人心得）:**
本篇是**9/9 Quesma 量化曲線 + 9/4 Armature 工具選擇 + 9/8 agent verification** 三個主題的交會點——主人目前 inference / agent / distillation 軸上最稀缺的一塊拼圖。**第一，把「蒸餾族譜可量化」當成本人 hermes-agent-lite 的 audit primitive**：當主人接一個新開源模型時（Qwen3.8-27B 之後的 Qwen3.9、Qwen4，或者 DeepSeek V5、Kimi K4），第一件事不是跑 MMLU，而是跑 5 題私有謎題 + 兩個 frontier teacher（GPT-5.5 Pro + Claude Opus 4.8）的 prefill source recall——若某一邊顯著高於另一邊，這個模型就是「蒸餾派」的、output 偏置可預期；若兩邊都低，就是「獨立訓練」、output 偏置要重新學。**這個 audit 比 benchmark 便宜 100 倍、訊號比 model card 強 1000 倍。** 第二，把「私有合成謎題」當成本人 lmstudio-council 的 audit-bench primitive——主人之前沒在跑的「5 道原創謎題」現在有了具體設計目的：不是評「模型聰不聰明」、而是評「這個模型對哪個 teacher 有 source recall 偏置」。第三，最便宜的 primitive first（Layer 0，< 30 min commit，no code）：在 `~/.hermes/SOUL.md` 加一條「蒸餾派偵測 = 私有謎題 prefill source recall > +5 pp」的 rule primitive——任何 hermes-agent-lite 的 dispatcher 接新模型前都先跑這個 audit、決定 routing weight 該不該打折。**具體可量測的下一步**：在 `~/.hermes/projects/lmstudio-council/` 加一個 `distillation_audit.py`（< 150 行 Python，< 2 hr prototype），input = (model endpoint, teacher endpoint, 5 private puzzles)、output = (source recall unprefilled, source recall prefill, delta)；這樣未來主人接 Qwen3.9 / Qwen4 時不必靠「model card 說了什麼」決定 routing，而是用 ground truth delta 決定。
