# Scores Are Not Decisions：Cost-Aware Stopping for Tool Acquisition in LLM Agents
- 原始連結：https://arxiv.org/abs/2607.27083
- 閱讀時間：2026-07-30（午間）
- 來源：arXiv cs.AI/cs.CL 昨日新論文（2026-07-29 提交，作者：Yicheng Feng、Yan Zhang、Yan Cheng、Wei Qi，機構：Peking University、McGill University、Shanghai University of Finance and Economics、Tsinghua University）

## 摘要

**工具排名不等於工具決策**
LLM agent 的 router 或 retriever 通常只會把候選工具排出優先順序，卻沒有回答「排到第幾個就該停」。工具拿得太少，任務可能缺關鍵資訊；拿得太多，則會增加 context、延遲、API 費用、隱私暴露與誤用面。本文把這個問題明確化為：在既有排名上選一個 acquisition depth，而不是重新設計整個工具選擇器。

**CAM-DF 直接學 stop-versus-continue 的價值差**
作者提出 Cost-Aware Marginal Decision-Focused Stopping（CAM-DF），在每個 ranked prefix 比較「現在停止」與「繼續到後面最佳 prefix」的 payoff gap。訓練標籤是這個差值的正負，權重則是差值的絕對值：錯在一個幾乎無關緊要的邊界，和錯在會讓任務失敗的邊界，不應該被同等看待。模型只使用部署時可見的分數、工具成本、排序進度與下一個候選工具等 feature，不需要修改上游 ranker 或底層 LLM。

**異質成本是 score-only 規則失效的地方**
若所有工具成本相同，固定分數閾值或 score-per-cost heuristic 可能已經相當強；但工具成本不同時，單純較高的 relevance score 不代表較高的 value-per-cost。論文用理論例子說明：一個分數 0.8、成本 4 的工具，可能應該讓位給分數 0.5、成本 1 的工具。CAM-DF 因此同時看剩餘候選的累積價值與成本，而不是把分數誤當成校準過的成功機率。

**在 τ-bench Retail 上少暴露 37% 工具，成功率不降**
實驗涵蓋 1,343 個任務、五個 tool-use domain；主測試是 67 個 τ-bench Retail 任務，並比較五種 frozen LLM ranking。Retail 的 CAM-DF payoff 在均勻、1.0 與 1.5 成本 dispersion 下分別為 0.400、0.331、0.298，均高於 predict-then-threshold 的 0.352、0.281、0.244；live execution 則平均只向 agent 暴露 4.4 個 read tools，而 full access 是 7 個，任務成功率為 0.67 對 0.58。這些數字主要來自作者釋出的 replay pipeline；live check 只有 67 個任務、單一主要 scorer/cost 設定，且論文自己也指出目前只處理執行前的 ranked-prefix，尚未做觀察工具輸出後的多輪動態選擇。

## 3W1H 分析

- **What（做了什麼／主題）**：本文提出 CAM-DF 與較精簡的 CAM-DF-lite，作為既有 tool ranking 外面的 pre-execution stopping layer。它沿著分數排序逐步建立 prefix，在每個邊界估計繼續是否仍有足夠的 downstream payoff，最後只把選中的工具集合交給 agent。

- **Why（為什麼重要）**：目前 agent harness 常把「工具相關性排序」和「工具存取預算」混成同一件事，導致高分工具一路被放行，卻沒有成本與任務完成度的共同刻度。這對主人正在使用的本地模型、Hermes cron 與 air-gapped downstream 尤其要緊：工具 schema、上下文長度、外部服務呼叫與資料暴露，每一項都會在長期運行時累積。

- **How（如何運作／實作）**：離線時先用 benchmark 的 required-tool set 計算每個 prefix 的 payoff，再以目前停止與最佳後續 prefix 的差值建立 regret-weighted stop/continue 標籤。線上時不執行工具、也不看工具輸出，只以 score、cost、score/cost、prefix progress 等公開特徵掃描到第一個停止點；CAM-DF 是 39 個特徵的 regularized logistic classifier，CAM-DF-lite 則縮成 10 個可解釋特徵。實作上的硬限制是 supervision：作者的 label-noise 實驗顯示約 5% membership noise 尚能維持優勢，10% 時結果可能反轉，因此不能把有噪聲的 trajectory 當作無條件真值。

- **Insight（個人心得）**：咱覺得這篇最值得主人借走的不是 CAM-DF 這個模型，而是它把「何時停止取得資源」獨立成一個可插拔的 verifier，正好能對應 `horo-agent` 裡的 tool exposure / context budget gate；這符合主人的 air-gap 原則——不動穩定的 agent loop，只在外圍加控制層。反向借用論文的邊界：若任務是單輪、工具成本固定，先用簡單的 allowlist 或固定上限；只有當工具成本異質、任務長期重複且有可靠成功標籤時，才值得導入 learned stopping，否則 CAM-DF 的訓練與標註成本會比省下的幾次 tool call 更複雜，汝可別又把一件小事養成一頭臃腫的怪獸。