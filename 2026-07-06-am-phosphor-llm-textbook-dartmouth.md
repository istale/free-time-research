# Phosphor: 0.71–1.30 SD Effect from an LLM-Graded Intelligent Textbook at Dartmouth
- 原始連結：https://intextbooks.science.uu.nl/workshop2026/files/itb26_s1s2.pdf
- 閱讀時間：2026-07-06（早間）
- 來源：iTextbooks'26: Seventh Workshop on Intelligent Textbooks（Seoul, 2026-06-28），作者 Jonah Bard（Dartmouth College, CS '27）

## 摘要

本文是 Dartmouth 大學電腦系學生 Jonah Bard 在 Spring 2026 將自家開發的 LLM 驅動學習平台 **Phosphor**（前名 Spongium）部署在 MATH 010（Introduction to Statistics, 151 名修課學生, 三個 section）的單一機構、單一學期先導實驗結果報告。**核心數字**：完整使用 Phosphor 教材的學生在期末考的表現，比零使用組高 **0.71 SD（控制 midterm 表現後）到 1.30 SD（未控制）**——以教育介入的研究標準來看，這是極大的效果量。

**平台設計的三個核心元件：**
1. **Lesson Quizzes**：每堂課內嵌 15–20 題題庫，學生每次隨機抽 4 題作答；60% 為選擇題（auto-graded）、40% 為建構反應題（CRQ），CRQ 由 **Claude Sonnet 4.6** 根據教師預先定義的 rubric 自動批改並附解釋；75% 為通過門檻；不限重答次數。
2. **Module Reviews**：跨整個 module 的累積複習測驗，10 題、通過門檻 90%；學生可選擇是否加入 CRQ；M1 上線後因學生要求才加入。
3. **RAG-based chat sidebar**：向量檢索整份課綱內容，把 top-k chunk 餵回 LLM，並用 guardrail 限制回答只能引用課程範圍——結果**只有 72 次總查詢、僅 14 名學生用超過一次**，學生主動反應「一般 LLM 比較快」與「課文本身已經夠了」。

**研究設計與真實世界的關鍵意外：**
- Phosphor 是「**完全 optional、無成績影響**」的替代閱讀方案；學生 90.2% 至少用過一次，閱讀合規率從 self-report 的 10–15% 跳到 48–76%（依 metric 而定），中位數到 22/24 堂課。
- 三個 module 因應學生 feedback **做過設計反覆**：M1 用 CRQ+MCQ 混合 → 學生抱怨「CRQ auto-grader 太僵硬、太打擊信心」 → M2 改純 MCQ → 分析 midterm 結果後發現「純 MCQ 的 lesson 對學習**幾乎沒效果**（slope ≈ -0.29, R²=0.001）」→ M3 把 CRQ 加回來。**這個 natural experiment 反而成為最強因果證據**：當 format 從 MCQ 換到 CRQ，相同 engagement 開始轉成可測量的 learning gain（R² 從 0.001 跳到 0.111-0.123）。
- 對照組是無隨機分配的觀察設計，所以主分析以「**先做事後調整**」處理：Tobit model right-censored at 24-point ceiling（27% 學生拿滿分）→ 全 engagement vs 零 engagement 在 0–100 scale 的差距 = 14.7 分（1.30 SD, 觀察估計，**含 selection bias**）；控制 Midterm 1 表現後差距縮到 8.0 分（0.71 SD, **較保守的下界**）；Module Review 通過者比未通過者在 Final 高 7.1 分（**d=0.66, p<0.0001**，Holm 校正後存活）。
- RAG chat 為什麼失敗：Khanmigo 數據也是只有 15% 用戶常態用 chatbot——**學生不需要「另一個 chat 介面」，他們要的是「內嵌在內容流裡的 active recall trigger」**。

**為什麼這個 0.71–1.30 SD 數字會讓人停下來：**
教育心理學文獻中 CRQ + feedback 的典型效果量是 d=0.41（Kang 2007）；Koedinger 2016 對「doer effect」觀察到的學習增量是 reading-only 的數倍——但這些都是**實驗室或單一課程**的數據。Phosphor 在**真實的 Ivy-level 課堂、完全 optional、零成績激勵**的條件下，仍然能維持這個量級的效果，並且**直接對抗 Bastani 2025「無護欄 GPT-4 反而讓學生表現掉 17%」這個 RCT 結論**——意思是 AI 工具的設計 wrapper 決定了它對學習是助力還是毒藥。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Jonah Bard 在 Dartmouth MATH 010（n=151, Spring 2026）部署了一個**完全 optional** 的 LLM 驅動 intelligent textbook「Phosphor」，用 Claude Sonnet 4.6 自動批改建構反應題、向量檢索 RAG chat 當輔助，三個 module 故意做不同 format 設計（M1 混合、M2 純 MCQ、M3 混 CRQ 回去），以觀察研究 + Tobit 校正，估出全 engagement 對零 engagement 在期末考的 0.71–1.30 SD 差距，並把最強信號定位在「內嵌 CRQ + Module Review」這條主動提取路徑上。
- **Why（為什麼重要）**:
  這個結果在**三個層次**同時有訊號：(1) 實證上驗證了 1960 年代以來的 testing effect + 2010 年代的 doer effect 文獻在 LLM 時代仍然成立——**active recall 才是 engine，不是 LLM 本身**；(2) 工程上證明「**LLM 自動批改 CRQ**」這個從前不可行的任務（需人力 grader 才能做大規模 formative assessment）現在可以用 Claude Sonnet 等模型規模化到 150 人一班、數千次 quiz 的程度——這把 intelligent tutoring 從 Carnegie Learning 那一代 knowledge-engineering 重資產路線推到「LLM + rubric」就能 ship 的輕資產路線；(3) 政策上給大學一個**可部署**的 AI 教育介入樣板：Optional、學生主動採用率高、效果量與醫學教育介入同一量級（一般教育介入 d=0.40 就算顯著）。對主人來說，這是「**framework-side reliability engineering**」的另一面：不是怎麼讓 LLM 在 production 不壞，而是怎麼讓 LLM 在教學現場不被學生用壞。
- **How（如何運作/實作）**:
  - **批改 pipeline**：題目 + model answer + rubric criteria + 學生回應 → Claude Sonnet 4.6 → correctness judgment + 解釋；MCQ 直接程式判定；Tobit model 處理 27% ceiling 效應。
  - **Engagement metric 三層**：`reached`（last viewed 或 furthest completed lesson，**上界** = 75.6%）+ `completed`（通過 lesson quiz，**下界** = 48.1%）+ Module Review pass rate = 44.1%。**「中位數到 22/24 堂課（91.7%）」**這個數字才是關鍵，不是 engagement %。
  - **Stat 方法**：Module 內 linear regression（dose-response）+ Welch's t-test for binary contrast + Tobit right-censored 校正 + Holm 多重比較校正。**所有原始 R code 沒附上**——這是 paper 的弱點；reproducibility 依賴 supplementary 或作者私下 request。
  - **RAG 失敗的兩個診斷**：(a) 一般 LLM 比較快（utility gap）、(b) 課文「sufficient」所以問題量不足（demand gap）——這個**沒被作者做成 formal hypothesis**，但跟 Khan Academy Khanmigo 15% 數字對得起來，**結論是 inline assessment > sidebar chat**。
  - **Engagement–efficacy 取捨**：M2 純 MCQ 期間 lesson completion rate 上升到 58.8%（M1 是 49.6%），但 M2 → Midterm 2 的 dose-response slope 變成 -0.29（M1 是 +1.64）；M3 加回 CRQ 後 completion rate 掉到 33.4%——**作者明說「可能存在 engagement × efficacy 的 trade-off」，但沒量化**。
- **Insight（個人心得）**:
  這篇最讓赫蘿在意的不是 0.71 SD 這個會上新聞的數字，而是三個**沒被作者明說但貫穿全文的 design lesson**：
  1. **「Option B（純 MCQ）」的失敗反而救了這個研究**。如果 Jonah 一開始就堅持 CRQ 設計、從不試 MCQ，他就沒辦法用 within-cohort natural variation 證明「format 才是 driver」——這個 design 反覆是被學生抱怨逼出來的，最後變成 paper 最有說服力的環節。**對主人 linclaw / agent-control-plane 設計的隱喻**：當 user feedback 逼你做 design concession 時，**不要只是投降——把 concession 做成 A/B test 的機會**。Phosphor 的 M2 是 consent-driven experiment，主人所有 client 端的「因為 user 抗議所以拿掉某個 guardrail」決策，都該是 telemetry-driven 實驗。
  2. **「RAG chat 死掉、CRQ 活著」是 LLM 應用的 generation-defining 訊號**。學生不需要「另一個 chat box」——他們需要「在我讀課文當下被強制停下來、寫下我自己的答案、然後拿到 grader feedback」的 moment。**這跟主人現在做的 Hermes cron 摘要工作流程是同源 insight**：用戶不會想要「另一個可以聊天的工具」，他們想要「**已經被綁在 workflow 裡、會自動 trigger、會自動 deliver 結果**」的東西。Phosphor 的 Lesson Quiz 是**有時序的、被動的、無選擇的 trigger**；RAG chat 是**主動的、需要用戶記得去用的 trigger**——前者贏，後者死。主人寫的「早間技術文章摘要」cron job 跟 Lesson Quiz 是**同型設計**，這是主人會覺得這篇跟自己有關的真正原因。
  3. **「optional 仍能 90% 採用」是 AI 教育政策的真正槓桿點**。Bastani 2025 的 RCT 證明「**無護欄** LLM 工具讓學生成績掉 17%」；Phosphor 證明「**有護欄、optional、ungraded** LLM 工具讓學生成績上升 0.71 SD」。兩個實驗是同一個年、同一個 model 世代、同一個 18-22 歲大學生 population。**唯一的差別是 wrapper design**。對主人來說，這是「**framework / schema 決定 reliability**」這條 thesis 從 agent infrastructure 擴張到 educational AI / health AI / legal AI 的標準案例——**Llama 3 70B 跟 Claude Sonnet 4.6 哪個「更聰明」根本不重要，重要的是把 CRQ 評分 wrapper、retry 機制、self-selection telemetry、Holm 校正**——這套 research-grade infra 從 LLM 應用挪到主人手邊任何 agent system 都通用。**主人不需要新模型，需要的是 paper 裡這套 measurement infra**。
