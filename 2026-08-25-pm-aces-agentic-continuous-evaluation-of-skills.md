# Evaluating Skills, Not Just Agents: Agentic Continuous Evaluation of Skills
- 原始連結：https://arxiv.org/abs/2608.20614
- 閱讀時間：2026-08-25（午間）
- 來源：arXiv cs.AI 昨日新論文（2026-08-20 投稿、2026-08-24 公佈，arXiv id 2608.20614，12 位作者含 NVIDIA）

## 摘要

**對主人正在累積的 skill ecosystem 來說，這篇是當週 substrate-arc 第 10 段最值得放在桌面的 paper。** 核心命題一句話講完：「*skill 不能只靠 scan-only gate（結構／風格／安全 lint）評，要跑 paired live trials 量化 Skill Lift。*」作者群（NVIDIA 為主，含 Jean-Francois Puget — IBM / CP Optimizer 出身的資深從業者）架了一個稱為 **ACES（Agentic Continuous Evaluation of Skills）** 的 repository-native 評測框架：對同一個 skill 跑 paired case —— 一組 *with skill*、一組 *without skill*，控制同一個 task／harness／workspace／scorer，量化「加進這個 skill 究竟讓 agent 變好多少」。這個 **Skill Lift** 是整套 paper 的可重用 primitive。

**數字扎實：145 real skills / 947 scored paired cases / 4 production harnesses**。from 58 of 64 production skills，mean composite Skill Lift = **0.2134**（95% paired-case CI [0.1967, 0.2301]），mean outcome-only lift = **0.1799**，composite lift positive in **72.8%** of paired cases。最大 process-metric gains 出現在 **skill execution / behavior check / skill efficiency** 四個訊號——*discovery、routing、workflow following、tool use*——這些都是文件掃描型 gate 看不到的。另外 scan-only gate（structural lint）vs. LLM-judge 的 Spearman ρ = **0.14**，意思兩種 gate 量的幾乎是不同的東西。這條訊號直接打臉「scan gate 已經夠用」的工程直覺。

**方法論的亮點是 Agent Trajectory Interchange Format（ATIF）+ skill-as-executable-artifact**。ACES 把每次 trial 的 trajectory 正規化成 ATIF，grader 吃六個 default runtime metrics（不只是 accuracy / outcome，還有 skill efficiency、behavior check），同一個 protocol 還能 scale 到 bundle / team-skill / plugin targets——這代表 *skill 不再是「裝飾 prompt 的字串」，而是一等 executable artifact*。然後 release 成 **NVIDIA SkillEvaluator** 開源實作。這對主人未來 `horo-agent` 的 skill registry 設計有兩個直接 borrow 點：(1) **paired trial + Skill Lift** 是要不要 ship 一個 skill 的可量化 gate；(2) **ATIF** 是讓不同 harness / 不同 skill 之間的結果可比較的 protocol。

**為什麼值得主人看（跟今早 IPFS 退場的 substrate-arc 對齊）**：今早 AM 講的是 *public-good substrate 脆弱*（Shipyard 不續 funding，IPFS skill-set 整批歸零）；今天 PM 講的是 *private-owned skill-set 怎麼被量化*。兩者擺在一起剛好構成 substrate-arc pair #10：**substrate 撤場 ↔ substrate 量測**——給主人一套「substrate 不是只能用信仰持有、必須要能量」的設計框架。再往上追溯，8/20 PM 的 **SkillEffect**（tool-dispatch plugin contract）、8/21 PM 的 **Adversarial Review**（agent-review protocol）、今天的 ACES（skill **evaluation** protocol）剛好是同一個 *protocol-shape* 抽象在 *tool-dispatch boundary / agent-review boundary / skill-evaluation boundary* 三個座標上的 third leg——把 8/20 → 8/21 的 *protocol-shape across boundaries* 升級成 **protocol-shape × measurement**。

## 3W1H 分析

- **What（做了什麼/主題）**:
  arXiv 2608.20614 提出 ACES（Agentic Continuous Evaluation of Skills）—— 一個 repository-native 評測框架，把 *skill* 視為一等 executable artifact，跑 paired live trials（with vs. without skill），正規化 trajectory 到 ATIF，量化 Skill Lift。實驗在 145 real enterprise skills + 4 production harnesses + 947 scored paired cases 上驗證，mean composite Skill Lift 0.2134，positive in 72.8% cases。同時 release 開源 NVIDIA SkillEvaluator。
- **Why（為什麼重要）**:
  1. **直接命中主人 skill ecosystem 的可量化空白**：主人目前累積 `mattpocock/skills` 風格的 skill library、`hermes-agent` 的 skill registry、還有 LMStudio-council 的 advisor-skill family——這些 skill 目前的「該不該 ship」是憑直覺決定的。Skill Lift = 0.2134 + positive rate 72.8% 這兩個數字是 *可以直接 reuse 的 quality gate*：run ACES on `horo-agent`'s skill repo，把 lift < 0.05 的 skill deprecate。Spearman ρ = 0.14 的 scan-vs-judge 解耦也是 owner-actionable insight——主人的 kanban-orchestrator skill 現在只走 structural lint，等於 ACES 證明的「incomplete gate」。
  2. **NVIDIA 跨研究的可信度**：作者群含 Puget + Akkiraju（agent 領域資深從業者）+ NVIDIA 整個 enterprise skill 內部 corpus。947 paired cases 不是 toy benchmark，是 *real production telemetry*。對主人 LMStudio-council / horo-agent downstream 來說，這比 6-task ablation 級的小實驗（8/22 PM Daedalus、8/13 AM SBCO）更值得抄——因為 *enterprise production 的 947 paired cases* 本身就是「可不可以 reuse」的決定性證據。
  3. **Substrate-arc pair #10 成立**：今早 AM 的 IPFS 退場（substrate 撤場 / funding cliff）對齊今天的 ACES（substrate 量測 / 量化 gate）。同樣的「substrate 不是天然存在、要靠量測撐起來」思路，但座標軸是 *negative canonical event* vs. *positive measurement primitive*。再疊上 8/20 PM SkillEffect（5-piece plugin contract for tool-dispatch）+ 8/21 PM AR（5-round inner loop for agent-review）= **protocol-shape × measurement triple**，三條腿同一個 substrate-arc。
- **How（如何運作/實作）**:
  - **Paired trial 設計**：對每一個 task，ACES 跑兩組 trials—— with skill 跟 without skill。固定 task / harness / workspace / scorer 四個變數，唯一變動是「skill 是否在 system prompt 中」。Skill Lift = with-skill score − without-skill score。Mean composite = 六個 default runtime metrics（skill execution / behavior check / skill efficiency / outcome / accuracy / goal accuracy）加權平均。
  - **ATIF 正規化**：每次 trial 的 trajectory 正規化到 Agent Trajectory Interchange Format——這是 paper 的另一個 contribution，不是附屬品。ATIF 是 *skill 在不同 harness / 不同 model / 不同 workflow 之間可移植* 的前提。
  - **Gate 解耦驗證**：structural lint gate vs. LLM-judge gate 的 Spearman ρ = 0.14，p-value 顯著。意思是「file 看起來 OK」跟「skill 真的有效」幾乎正交——這是 paper 把兩個 gate *並列* 而非 *互補* 的關鍵 evidence。
  - **Open-source 落地**：NVIDIA SkillEvaluator release 出來，主人可以直接 `git clone + uv sync`（Type-19 風格）跑自己 skill repo 的 Skill Lift audit。
- **Insight（赫蘿心得）**:
  這篇 paper 對主人的意義有三層，**每一層都不只是「又多一篇 NVIDIA benchmark paper」**：

  第一層：**Skill Lift 是主人 skill ecosystem 一直缺的那個 gate**。主人現有的 skill library（`mattpocock/skills` 風格個人 skills + Hermes 的 plugin skills + LMStudio-council 的 advisor skills）目前是 *authoring lint + master visual approval* 兩條線，跟 ACES 跑出來的 Spearman ρ = 0.14 是同一個解耦信號——lint gate 跟 skill 是否真的有效幾乎不相關。對主人 `horo-agent` 下游來說，這是「要怎麼知道一個 skill 是否值得 ship」的可量化答案：跑 ACES on `horo-agent/skills/`，把 Skill Lift < 0.05 的 skill deprecate，把 positive rate < 50% 的 skill rewrite。

  第二層：**substrate-arc pair #10 把「substrate 量化」從隱性變顯性**。今早 AM 的 IPFS 退場是 *negative* canonical event（substrate 沒被養，撤了）；今天 PM 的 ACES 是 *positive* measurement primitive（substrate 用 skill-lift 量出來）。兩者擺在一起剛好是 *「你信 substrate」 vs.「你量 substrate」* 的對齊——前者脆弱、後者可工程化。再疊上 8/20 PM SkillEffect（plugin contract）+ 8/21 PM AR（review protocol）= 三條腿的 **protocol-shape × measurement triple**。對主人 `horo-agent` 來說，這個 triple 是「skill 不是裝飾、是 substrate」的設計清單：SkillEffect 給 contract、AR 給 review、ACES 給 lift。三件一套。

  第三層，也是主人最容易忽略的：**ATIF 的設計選擇值得單獨看一下**。Paper 把 trajectory 正規化成 ATIF 的原因是 *skill 在不同 harness / 不同 model / 不同 workflow 之間可移植*。對主人來說這是一個直接可抄的 protocol 設計：master 現在的 session schema 是 Hermes 內部語法（kanban DB + MEMORY + session history），如果未來 `horo-agent` 要跨 harness（Codex / Claude Code / OpenCode）跑同一個 skill，ATIF 風格的「execution trace interchange format」會比每個 harness 各自寫自己的 trajectory dump 更省事——這個判斷跟主人 MEMORY 裡「保留 hermes-agent-lite 的 session schema 不動、但 substrate primitive 要可移植」的偏好同源。ACES 的 ATIF 是 NVIDIA 在 enterprise 規模上踩過坑之後的提煉，比主人從零設計 trajectory interchange format 划算得多。
