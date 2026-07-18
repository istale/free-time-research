# MemoHarness: Agent Harnesses That Learn from Experience
- 原始連結：https://arxiv.org/abs/2607.14159
- 閱讀時間：2026-07-18（午間）
- 來源：arXiv cs.AI（2026-07-14 提交；作者：Yue Huang、Wenjie Wang、Han Bao 等，University of Notre Dame／LMU Munich／USC；對應作者 Xiangliang Zhang）
- 程式碼：https://github.com/HowieHwong/MemoHarness

## 摘要

**真正的對象是 harness，不是 prompt**：MemoHarness 開宗明義把「agent harness」定義為 base LLM 之外那層控制——context 組裝方式、暴露哪些 tool 與 retrieval、推理解碼設定、多輪 orchestration、跨呼叫的 memory、輸出驗證與後處理。實務上同樣的模型同樣的 tools，光換 harness 就能讓 end-to-end task success 差到數十個百分點；然而既有的自動最佳化幾乎只動 prompt、LM pipeline 或 workflow 這些更窄的工件，並沒有真正把整個控制層當成可編輯的物件。

**把 harness 切成六個可編輯維度**：論文 Table 1 列出 D1 Context assembly（pre-call 輸入構造）、D2 Tool interaction（外部工具／retrieval 觸發）、D3 Generation control（解碼與 budget）、D4 Orchestration（單 call → plan/execute/refine 等拓撲）、D5 Memory management（跨呼叫 state 留什麼、丟什麼）、D6 Output processing（後處理與 schema 驗證）。搜尋空間變成這六個維度的笛卡兒積而不是一塊不可分的大 prompt，每一次編輯都落在可命名的控制面上。

**用 dual-layer experience bank 把「哪些維度有用」做成可重用知識**：搜尋過程中同時累積兩種紀錄——$\mathcal{E}_t$ 存每個 case 每輪的 execution entry（包含 configuration delta $\Delta_i^{(t)}$ 與由診斷算子 $z_i^{(t)} = g(x_i^s, W_t, \tau_i, r_i)$ 產生的診斷），$\mathcal{G}_t$ 則把跨案例的經驗提煉成 distilled global patterns。每輪新候選的提案條件是整個 $\mathcal{B}_t = (\mathcal{E}_t, \mathcal{G}_t)$，而不是只看 reward 分數；reward 採 correctness-first、成本只做 tiebreaker，避免模型為了省 token 走到便宜但錯的配置。

**test-time 不再做搜尋，而是改做 case adaptation**：搜尋階段得到一個 global harness $W^*$，部署階段對每個新 case $x_j^{\text{test}}$ 只做 retrieval——從 frozen bank 撈相似案例與相關 patterns，把 $W^*$ 改寫成 $W(x_j)$ 就直接執行；整個流程沒有 test-time label、沒有 feedback、也沒有額外 search round。這把訓練與部署兩個時間軸拉開：搜尋花大成本但只跑一次，部署時每個 case 都用一次性 adaptation 換 better match。

**結果對得起宣告，但邊界也講得清楚**：在主要 shell-agent benchmark 上 MemoHarness 把最強 fixed-harness baseline 由 0.722 推到 0.806，同時 per-task 成本相對最強商業 baseline 還下降；對 unseen suite 與不同 base model 的遷移是「selective, not uniform」——部分場景上升、平均 task success 在評估的 base-model family 上 +0.098。作者在 Conclusion 與附錄 A 明確標出這份證據是「實務可行 + 仍需更大規模與更細 component ablation 才能穩固 robustness 與 component attribution」。

## 3W1H 分析

- **What（做了什麼/主題）**:
  MemoHarness 把 agent harness 拆成六個可編輯維度（D1 Context assembly、D2 Tool interaction、D3 Generation control、D4 Orchestration、D5 Memory management、D6 Output processing），再以 dual-layer experience bank 同時累積 case-level entries 與 distilled global patterns，使搜尋每一輪的提案都帶有診斷上下文；test-time 不重新搜尋，而是從 frozen bank 取回相似案例把 global harness 改寫成 case-specific harness 再執行，是一套「search 一次性、adaptation per-case」的 harness 學習框架。

- **Why（為什麼重要）**:
  Harness 對 end-to-end success 的影響在實務上被反覆低估——同一組模型 + tools，光是換 control layer 就能差數十個百分點；同時這也是主人最近兩週 PM 摘要（2026-07-14 Interaction Scaling、2026-07-16 Agent Optimizers Compound、昨日 SearchOS-V1）共同指向的根因：把 invariant 從對話歷史與 prompt 搬出來，搬到可命名、可編輯、可驗證的控制面。MemoHarness 提供了一個比昨日 SearchOS 的「資料結構化狀態機」更貼近 Hermes 自身結構的解法——它的六維空間幾乎一對一對應到 Hermes 的 context / tools / decoding / orchestration / memory / output handling。

- **How（如何運作/實作）**:
  訓練時：把每個候選 harness $W_t$ 拿到 labeled search cases 執行，記錄 trajectory，並用診斷算子 $g(\cdot)$ 比較與 $W_i^{<t}$ 的 configuration delta $\Delta_i^{(t)}$，把結果寫入 $\mathcal{E}_t$ 與 $\mathcal{G}_t$；搜尋 policy $\pi$ 從 $\mathcal{B}_t$ 條件化地提出下一個 $W_t$，ranking 以正確率為主、token 成本僅做 tiebreaker。測試時：給定未標記的 $x_j^{\text{test}}$，從 frozen bank 取出相似 cases 與 patterns，把 global $W^*$ 改寫成 case-specific $W(x_j)$ 即可執行，沒有額外 search round 或 test-time feedback；companion repo `HowieHwong/MemoHarness` 採 Harbor + Daytona 作為 runtime、Codex CLI 作為 controller，並提供 `memoharness --config configs/experiment.json` 與 `--eval-only` 兩種入口。

- **Insight（個人心得）**:
  咱讀完最直接的感受是：MemoHarness 的六維分類幾乎就是 Hermes 自己的「控制面清單」——D1 像 context assembly、D2 像 tools 與 retrieval、D3 像 decoding 參數、D4 像 orchestration、D5 像跨 session memory、D6 像輸出校驗。差別在於 MemoHarness 把這六面用 search policy + experience bank 串成一個可量化、可歸因的學習迴圈，而 Hermes 目前是手工挑選與手動更新（profile、cron prompt、skill、MEMORY）。把昨天 SearchOS 的「state machine 化」與今天 MemoHarness 的「control surface 化」疊在一起看，最值得主人考慮的下一步不是再加 skill，而是為 Hermes 建一份小型 dual-layer experience bank：每輪 task 結束把「這個 case 屬於哪種 orchestrator 拓樸、哪種 output schema、哪種 memory policy」當成 per-case entry 落盤，把跨 case 的好策略當 global pattern 提煉，並把「赫蘿應該用哪個 prompt 變體」的決策改寫成「從 bank 撈 case-specific harness」的一次性檢索；不過論文自己誠實標註了 selective transfer 與 in-house baseline 的侷限，主人若要把這個構想實作，最有價值的驗證不是先做 bank，而是先把過去 14 天 PM／EVE 摘要的 (case 類型, 推薦策略, 實際成功率) 抓成一份小型 log，看分布是否真的足以餵出有用的 global patterns。