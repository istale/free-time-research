# Change2Task: From Repository Changes to Executable Coding Agent Tasks and Environments
- 原始連結：https://arxiv.org/abs/2607.28591
- 閱讀時間：2026-08-01（午間）
- 來源：arXiv cs.SE / cs.CL（2026-07-30 提交；Haomin Qi、Xingliang Wang、Xuanqi Gao 等 12 位作者）

## 摘要

**把 repository history 變成可執行任務，而非只當成檔案庫。** Change2Task 從已合併的 pull request 取得開發者原始意圖、實作 patch 與測試證據，再把同一項維護工作重建到該 repository 較新的健康版本。每個產物不只是一段題目文字，而是一組可重現的環境狀態、任務規格、修復 oracle、允許修改範圍與來源證據，能用於 coding agent 的訓練、benchmark 與持續評估。

**三層重建路線，先保守、失敗才升級。** Level 1 直接反轉歷史 patch；若新舊程式碼已有局部漂移，Level 2 以唯一 source-block 對應做 Code Mapping；只有結構對不上但行為仍存在時，才進入 Level 3 Agent Reconstruction。第三層最多嘗試四次，候選 patch 須依序通過套用、語法、修改範圍、複雜度與來源變更 fidelity 檢查，避免讓生成代理把任務改成另一件事。

**真正的核心是三態生命週期驗證。** 健康基底必須讓 target 與 regression checks 都通過；套用 task patch 後，target checks 必須失敗而 regression checks 繼續通過；最後套用 restoration patch，兩者都要恢復通過。研究由 1,130 個已符合前置條件的歷史變更建成 900 個任務，涵蓋 Bug Fix、Feature Addition、Test Generation、API Migration 與 Security Repair，整體 verified construction 成功率為 79.6%。

**成果顯示「共用健康基底」比每題一個 snapshot 更划算。** 在 621 個相同 Bug Fix 候選上，Change2Task 建成 500 題，SWE-smith PR Mirror 為 387 題，相對多回收 29.2%；3,600 組配對 agent 評估的整體結果一致率為 89.7%，最高子組達 98.0%。900 題共用 388 個現代基底後，環境準備時間下降 58.4%、保留儲存量下降 71.2%，但納入額外自動化與 Agent Reconstruction 後，端到端支出只下降 10.8%——表示主要收益是環境與資料供應效率，不該把它誇成大幅降低所有成本。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Change2Task 將真實 merged PR 轉成落在現代 repository revision 上的可執行 coding-agent 任務。它同時保留歷史維護意圖、可驗證行為、修改範圍與來源 provenance，並以配對實驗確認重建題仍大致維持原題的 agent 排名與難度訊號。

- **Why（為什麼重要）**:
  Coding agent 的瓶頸不只在模型，而在「有多少健康、可跑、可判分的任務環境」。單題單 snapshot 會重複付出依賴安裝、映像儲存與驗證成本；若能在同一個維護中的基底重放多個真實變更，repository 歷史就會成為可持續補充的評測資料源，而不是一次性 benchmark 素材。

- **How（如何運作/實作）**:
  系統先凍結可執行的健康 descendant commit，對齊歷史 PR 的 target checks、regression checks 與現代行為位置，再依序嘗試 Patch Reversal、Code Mapping、Agent Reconstruction。每個候選都必須完成「健康 → 故障/缺功能 → 還原」三態驗證，並通過 scope 與 fidelity gates；但適用前提也很硬：需有可追溯 PR、穩定 executable oracle，以及仍保留相關行為的現代基底，目前實驗主要來自公開 Python／Java corpus。

- **Insight（赫蘿觀察）**:
  咱認為這篇最值得主人借的是「一個健康基底，承載多個可逆任務」這個最小單位，而不是照搬整套三層建構器。對目前的下游精簡與 code-agent 工作流，可先把每階段 commit 視為歷史證據，為同一份已驗證基底保存 task patch、restoration patch 與真實 lint/build/test gate；只有當這三態都能被機器明確判分時才轉成回歸題，若涉及 UI 可理解性、主觀設計或缺乏穩定 oracle，就不該硬塞進自動生成流程。這恰好守住主人的保守減法原則：重用已證明健康的 runtime，不為了造資料而重寫核心。
