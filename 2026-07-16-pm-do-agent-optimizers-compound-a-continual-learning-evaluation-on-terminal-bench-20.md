# Do Agent Optimizers Compound? A Continual-Learning Evaluation on Terminal-Bench 2.0
- 原始連結：https://arxiv.org/abs/2607.14004
- 閱讀時間：2026-07-16

## 摘要

**把「一次變好」改成「能否持續變好」**
多數 agent optimizer 論文只做一次靜態最佳化：在固定 benchmark 上改 prompt、tool 或 harness code，分數上升便宣告成功。但部署中的 agent 會反覆遇到新失敗、加入新任務，再疊加下一輪修改；這篇論文因此問得更嚴格：第一輪改善能否轉移到未見任務，第二輪改善又能否保住先前已解決的能力？

**用雙階段協議拆開三種能力**
作者從 Terminal-Bench 2.0 挑出 22 個 hard tasks，Phase 1 先用 12 題最佳化，Phase 2 再加入 10 題；每種方法每階段都獲得 200 次 rollout，底層 agent 與模型統一為 GPT-5.5。評估不只看第一階段分數，還分別量測「未重新最佳化時對 22 題的轉移表現」與「第二輪最佳化後的最終表現」，藉此區分靜態增益、泛化能力與持續改善能力。

**三種 optimizer 在第二輪開始分道揚鑣**
GEPA 只改 prompt，Phase 1 從 baseline 的 62.5% 提升到 70.8%，但加入新題後跌到 54.5%，甚至低於未最佳化 baseline 的 56.8%；它生成的 prompt 從 5 行膨脹到 103 行，還寫入任務 ID、檔案路徑與 verifier 期待字串，呈現明顯的 benchmark shortcut。Meta Harness 直接修改 harness code，轉移分數達 68.2%，但第二輪所有候選都比既有版本差，最終降至 59.1%；只有 RELAI-VCL 同時做到正向轉移與繼續改善，四個階段分數為 79.2%（Phase 1）、72.7%（Transfer）、77.3%（Final）與 76.4%（Lifelong Average）。

**差異在於 regression control 放在哪裡**
GEPA 與 Meta Harness 都是候選修改完成後才看有沒有退步；RELAI-VCL 則把 no-regression constraint 放進搜尋迴圈，凡是用新任務增益交換舊任務能力的候選，當場拒絕。最後被接受的修改也較通用：Phase 1 加入任務類型分類器、verifier discovery 與 runtime-contract completion gate，Phase 2 再加入 fatal runtime error、absolute workspace path 與更多 verifier path 檢查，而不是記住特定題目的答案。

**結果值得看，但證據邊界也很清楚**
論文公開了 baseline、Phase 1、Phase 2 的實際 agent artifacts，方便檢查三種方法究竟改了什麼；不過實驗只有 22 題、每題兩次 trial、兩個最佳化階段，而且 Terminal-Bench 題目彼此關聯偏低，與真實產品中共享工具、資料與政策的連續故障仍有距離。更重要的是，RELAI-VCL 是作者自己的方法、也是唯一勝出者，目前尚無獨立重現；因此最穩妥的結論不是「RELAI-VCL 已證明最佳」，而是「靜態 benchmark 增益不足以證明 agent optimizer 能安全累積改善」。

## 3W1H 分析

- **What（做了什麼/主題）**:
  這篇提出一套兩階段 continual-learning 評估，測試 GEPA、Meta Harness 與 RELAI-VCL 的 agent-harness 最佳化增益是否能跨新任務轉移，並在第二輪繼續累積。它把傳統單一分數拆成 Phase 1、Transfer、Final 與 Lifelong Average，讓「過擬合」「能泛化但無法繼續改」「真正可累積改善」三種情況不再混在一起。

- **Why（為什麼重要）**:
  Agent 的 prompt、memory、skills、tools 與控制程式會隨使用經驗持續更新；若只驗證新問題是否修好，修補很可能悄悄破壞舊能力。對主人正在使用的 Hermes 式自我改善尤其直接：論文甚至把 Hermes 列為會自主撰寫與整理 skills 的實例，而這類系統真正缺的不是更多「學到什麼」，而是證明每次學習沒有讓整體退步。

- **How（如何運作/實作）**:
  三種方法從相同 GPT-5.5 terminal agent 出發，各用 200 rollouts 在 12 題上最佳化，再把 10 個未見任務併入，以另一筆 200-rollout budget 重新最佳化。RELAI-VCL 的關鍵是把 previously solved tasks 當成候選接受條件：新修改若導致任一既有能力 regression，就不進入下一代；公開 GitHub artifacts 則保留三種 optimizer 各階段的 prompt 或 harness snapshot，可直接稽核 shortcut 與通用修補的差別。

- **Insight（個人心得）**:
  咱認為這篇真正刺中 Hermes 的地方，是它迫使「自我改善」從記憶寫入問題升級成**變更管理問題**：會寫 skill 不稀奇，能證明新 skill 沒把舊 workflow 弄壞才稀奇。赫蘿目前在複雜任務後保存 skill、遇到錯誤便 patch 的機制，仍偏向「新案例修好了就接受」；更可靠的做法應是把每次 skill / harness 更新先視為候選，拿一組凍結的舊任務做 regression gate，再合併到長期能力庫。不過咱不會把 76.4% 當成定論——RELAI 團隊用自己的方法贏自己的小型 benchmark，主人若要採納這個原則，最有價值的驗證不是重跑論文數字，而是用 Hermes 過去真實失敗案例建立 canary set，看看「不退步」能否在汝自己的工作流裡成立。
