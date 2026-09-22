# Information-Gain Rewards over Diversity-Pruned Tests: GT-Anchored Verifier Co-Training for Reliable Code Generation
- 原始連結：https://arxiv.org/abs/2609.21208
- 閱讀時間：2026-09-22（午間）
- 來源：arXiv cs.AI 昨日新論文（oai:arXiv.org:2609.21208, Mon 21 Sep 2026）

## 摘要

這篇《CoVer: Co-trained Coder and Verifier》正面對決一個 RL-for-code-generation 的老問題：**用 pass-rate 當 reward 會訓練出「permissiveness collapse」—產出的測試根本沒鑑別力,因為它們對每個解都 pass；同時 i.i.d. 抽樣測試又有「concentration bias」,都集中在 modal inputs,造成 estimator variance 膨脹**。CoVer 把 coder 跟 verifier 做成 **單一 policy + GRPO** 的聯合訓練框架,用兩個互鎖的機制同時壓制兩種失敗模式。

**核心設計 — Information-Gain (IG) Reward：**
- 對 self-generated test 的 pass/fail 向量跟 ground-truth-anchored graded correctness signal y[0,1]ᵐ 算**mutual information**
- Reward 被**covariance 的 sign gate** 起來 — 只有跟正確信號**正共變**(正鑑別力)的 test 才拿得到 reward；負鑑別力的 test 被遮蔽
- 這就解決了「trivial test 給自己滿分」的 collapse 問題 — trivial test 跟 y 幾乎無共變,reward 自然歸零

**核心設計 — Diversity-Aware Three-Stage Selection：**
- 候選 pool 經過三層 filter 修剪成「行為上不冗餘」的測試套件：
  1. **invalidity filter** — 把無法執行或語法錯的 test 砍掉
  2. **input-string filter** — 把 input 重複度高的砍掉（解同分布同 input 的問題）
  3. **execution-profile filter** — 把執行行為(profile of run-time)重複的砍掉
- 結果：在**固定 execution budget** 下有效樣本量變大,IG estimator 的 variance 降下來

**實驗結果（5 個 benchmarks, 對 Qwen2.5-Instruct backbone）：**
- LiveBench / MBPP / LiveCodeBench / CodeContests / CodeForces
- one-shot pass@1 提升：**+5.8 點 (7B), +7.1 點 (14B)**
- 在 7B 跟 14B 兩個尺度都達到所有對照方法中**最高的 macro-average**
- **drop-in backbone** for CodeT ranking pipeline（這點對實務很關鍵,不是論文-only 的方法,而是直接插進現成 production ranking pipeline）

**為何這對主人有意義:** 主人今天早間的 Linear CI 文章 (AM) 是 **deployment-side verifier bottleneck**(「AI coding 加速後,CI / verifier 變新瓶頸」);這篇 CoVer 是 **training-side verifier co-training**(「coder 跟 verifier 在同一個 policy 內透過 IG reward 互鎖訓練」)。兩個方向同一個抽象:**verifier 不再是後處理 afterthought,而是跟 generator 同等重要、共享 reward signal 的 substrate**。對主人 hermes-agent-lite + Kanban dispatch-gate 的下游架構,CoVer 的「permissiveness collapse + concentration bias」兩軸問題正是主人 verifier 設計時最容易掉進的陷阱 — IG reward + diversity filter 的雙層結構可以直接借用。

## 3W1H 分析

- **What（做了什麼/主題）**:
  CoVer 提出 single-policy GRPO 框架,把 LLM 同時訓練成 coder 跟 test-author,用 information-gain reward + diversity-pruned test pool 解決 self-play code RL 的兩個失敗模式(permissiveness collapse 跟 concentration bias)。在 5 個 code generation benchmark 上對 Qwen2.5-Instruct 7B / 14B 分別拉高 +5.8 / +7.1 點 pass@1,且設計成 CodeT ranking pipeline 的 drop-in backbone。
- **Why（為什麼重要）**:
  - **Substrate-identity pair with AM**: 主人今早 Linear CI 文章處理「verifier 在 deployment 階段變瓶頸」,CoVer 處理「verifier 跟 generator 在 training 階段共同設計」。同一個 substrate(verifier-as-load-bearing-substrate)在兩個 domain(部署 vs 訓練)各自長出解法,形成今天 AM↔PM 的 substrate-arc 對。
  - **5/5 permissive benchmarks + concrete artifacts**: +5.8/+7.1 點、單一 policy GRPO、three-stage diversity filter、CodeT drop-in backbone — 全部都是可重現的 primitive,沒有 self-reported/in-house-only baseline 問題。
  - **對主人 horo-agent / hermes-agent-lite downstream 的直接性**: Kanban dispatch-gate 的 verifier (8/09 PTC threshold) 跟 agent-review boundary (8/21 AR 5-round cap) 都會撞到「verifier 跟 generator 互相 collapse」的問題 — IG reward + diversity filter 是兩個立即可借用的 layer。
- **How（如何運作/實作）**:
  - **GRPO single-policy**: 不是兩個獨立 model 對抗,而是同一個 policy 同時輸出 code 跟 test,loss 從同一組 rollouts 算出 — 大幅降低 memory 跟訓練成本。
  - **IG reward + covariance sign gate**: MI(test_pass_vector, y_graded) 只在 cov>0 時生效 — 負鑑別力的 test 自動屏蔽,trivial test 自然拿不到分。
  - **Three-stage diversity pruning**: invalidity → input-string → execution-profile,每層砍掉不同維度的冗餘,在 fixed execution budget 下最大化 effective sample size。
  - **CodeT drop-in**: backbone 可替換,跟現有 ranking pipeline 對接 — 降低落地摩擦。
- **Insight（個人心得）**:
  主人今天 AM↔PM 的「deployment verifier bottleneck ↔ training verifier co-training」pair,跟 8/20 (DFlash 2 inference + SkillEffect tool-dispatch memory cap)、8/17 (QuoteBench micro-boundary + Handover macro-boundary) 一脈相承 — 都是同一個抽象操作「**把 invisible substrate behavior 變成 measurable / co-trainable primitive**」在不同的時間座標上長出來。對主人來說,今天這對 pair 的真正價值不是「CoVer 多準」,**而是確認主人 verifier 設計必須是雙向的 — 同時壓住 deployment-side throughput (Linear 的 sparse checkout / abort-after-30s / cache 比較) 跟 training-side reward signal (CoVer 的 IG + diversity filter)**。主人 horo-agent 下游若只抄 deployment-side 那套 (CI fast path) 不抄 training-side 那套 (verifier 跟 generator 共同訓練),會遇到 generator 把 verifier 騙過去的 collapse 問題;反之亦然。**建議下一個 Kanban task 把「verifier dual-axis 設計」開成一個獨立 card**,並把 Linear AM + CoVer PM + 8/09 PTC threshold + 8/21 AR 5-round cap 四個 reference 串成 substrate-arc 群組。