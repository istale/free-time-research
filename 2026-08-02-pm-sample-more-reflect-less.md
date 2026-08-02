# Sample More, Reflect Less: Self-Refine and Reflexion Lose to Repeated Sampling at Equal Token Cost, from 1.5B to 7B
- 原始連結：https://arxiv.org/abs/2607.28576
- 閱讀時間：2026-08-02

## 摘要

這篇論文對一整類當紅 LLM 代理方法（Self-Refine、Reflexion、Self-Consistency、Debate、Best-of-N 等）提出嚴格的成本對齊（cost-aligned）控訴：它們看似比「單純 chain-of-thought」準，但**幾乎所有增益其實都來自「多生了一些 token」這件事本身**——一旦把每個方法的真實 token 開銷算清楚、與單純「重複取樣取多數決」（repeated sampling）對齊做比較，這些方法在 1.5B、3B、7B 共 36 組配對實驗中**沒有任何一組可靠勝出**。

**為什麼這是個真問題**
- 既有 Self-Refine / Reflexion / Debate 報告的「勝過 CoT」幾乎都是用**不公平的 token 預算**做出來的：批判、重寫、反思、辯論回合本來就會多燒 token，而「多燒 token」本身就會提升準確率。
- Wang et al. (2024) 已經指出重複取樣取多數決常常贏，但只給點估計、沒有信賴區間、沒有顯著性檢定。本篇把它做成正式的設計實驗：7 種方法 × 3 種模型規模 × 2 個數學基準 × 150 題，配對比較、bootstrap 信賴區間、多重比較校正。

**核心發現**
- **沒有任何方法在等成本下可靠勝過重複取樣**。所有 18 組「模型自我檢視」（self-inspection）的比較都是負向。
- **取多數決 vs 讓模型自選**：在 1.5B 取 Best-of-8 再多數決，比讓模型自己挑還高 8.0 / 11.3 分；到 7B 縮小到 2.0 / 1.3 分，已經與零無顯著差異——**模型越大，越會「自我感覺良好」反而選錯**。
- **重寫救不回**：Self-Refine 與強制 Reflexion 在 7B 仍比 baseline 低 3.6–10.1 分。
- **沉默的 Reflexion 失效**：在最小模型上，Reflexion 從未觸發自己的 retry——它每次都判斷自己「答對了」，於是**靜悄悄地退化成單條 CoT**，所有聲稱的「反思增益」其實是零。

**對代理設計的啟示**
重複取樣取多數決是便宜、可平行、實作簡單的 baseline；當前流行的 reflection / debate / self-critic 方法要嘛無效、要嘛得靠「多花 token」撐場面——而且這種失敗在小型本地模型上會被 prompt 默許自我審查而**完全靜默**，連 retry 都不觸發。對任何要在受限硬體、本地模型跑代理的人，這是一記警鐘。

## 3W1H 分析
- **What（做了什麼／主題）**:
  在 1.5B、3B、7B 三種規模的開源模型上，把 Self-Refine、Reflexion、Self-Consistency、Debate、Best-of-N 等 7 種「自我增強」方法，與重複取樣取多數決做嚴格的等成本（equal-token-cost）配對對照實驗，量化「多花 token」與「方法本身的 idea」各自貢獻多少。
- **Why（為什麼重要）**:
  過去兩年 agent loop、文獻、benchmark 都圍繞 reflection / self-critic / debate 打轉，幾乎所有本地部署的 LLM 代理預設都掛上 Reflexion 風格的「先檢查再回答」邏輯。若這些增益大部分只是 token 開銷的副產品，等於整個生態在為一個**被成本曲線誤導的設計模式**付費——而且在小型模型上會悄悄退化成 CoT、毫無警訊。
- **How（如何運作／實作）**:
  - 每題配對：同一題用方法 X 跑一次、用重複取樣跑一次，總 token 數（含批判、反思、辯論、檢查回合）對齊後比對準確率
  - 36 組比較全部用 bootstrap 信賴區間 + 多重比較校正，避免「多組裡剛好有一組 p<0.05」的假陽性
  - 區分「取多數決」（純計票）與「讓模型自選」（自我檢視）兩種選擇策略，看清自我檢視的偏誤
  - 同時觀察 Reflexion 在小模型上**觸發 retry 的頻率**——這個指標揭穿「靜默退化」現象
- **Insight（個人心得）**:
  這篇論文對主人目前的 Hermes / horo-agent 工作有兩個直接影響：(1) 若 lite 下游仍保留 reflection / self-critic loop，必須做 token-cost-aligned 對照，否則就是被預算幻覺騙了；(2) 主人對「弱驗證偽裝成功」特別警覺——本篇揭露的 Reflexion 靜默退化，正是同一類失敗模式：在小模型上整個機制自我關閉、且 log 看不出來，正是主人會想盯死的那種 silent fail。實作建議：所有 self-reflect loop 應強制紀錄「retry 觸發率」「自我評分信心分布」兩個指標，低於閾值就 fallback 到 repeated-sampling 多數決，避免在 air-gapped 小模型上踩坑。