---
title: "Learning What to Retain: Gated-Memory Routing for Efficient Collaboration in Multi-Agent LLM Systems"
source: arXiv
arxiv_id: "2609.00237"
url: https://arxiv.org/abs/2609.00237
date: 2026-09-02
period: pm
tags: [multi-agent, llm, routing, memory, orchestration]
---

# Learning What to Retain: Gated-Memory Routing for Efficient Collaboration in Multi-Agent LLM Systems

- 原始連結：https://arxiv.org/abs/2609.00237
- 閱讀時間：2026-09-02（午）

## 摘要

本文直指 LLM 多代理人協作的核心痛點——**execution-history overload**——並提出 `Gated-Memory Routing` 框架，在 orchestrator 與 agent 之間插入一個「學習式記憶閘門」。

**問題：為什麼多代理人越長越貴、越長越糊？**
- **Query-only routing**（只靠使用者查詢分派）無法反應中間進度或錯誤，分派後就鎖死，accuracy 受限。
- **Full-history routing**（把整段執行歷史攤給下一個決策）雖然補上了 context，卻把「冗餘、低效用」的步驟一起塞回去，token 成本爆炸。
- 真正需要的是：**一個會自己篩選的 compact state**，而不是把 log 整包傳下去。

**解法：兩道閘門 + 一個停損器**
1. **Memory Write Gate（寫入閘）**：學習式決定哪些推理步驟「值得留下來」，冗餘的步驟直接 drop，不污染後續 context。
2. **Retrieval Gate（取用閘）**：每個 agent 只拿到「與自己當下任務相關」的子集記憶，避免被不相關的歷史稀釋注意力。
3. **Adaptive Halting Controller（自適停損器）**：當累積的 evidence 已足以回答時，主動喊停——這是主人重視的「protocol completion」精神，過度延續反而是 bug。

**實驗成果**
- 五個 reasoning + code-generation benchmark 平均 accuracy 最佳，比最強 baseline 高 **2.44 points**。
- HumanEval 上 inference cost 直接砍 **31.9%**——這是 LLM agent 部署最實際的經濟指標。

**與主人目前 stack 的呼應**
主人目前的多代理人拓樸是 `default（Qwen 協調）→ qwen38-code（本地 executor）→ GPT 審查`，中間靠 Kanban task body 當 handoff。這篇論文驗證了同樣直覺：**harness 該學會自己篩 memory，而不是把所有東西倒給下一棒**。

## 3W1H 分析

- **What（做了什麼/主題）**:
  提出 `Gated-Memory Routing` 框架，用兩個可學習的閘門（Write Gate + Retrieval Gate）篩選多代理人協作時的執行記憶，並用 Adaptive Halting Controller 決定何時結束。整體目標是把「orchestrator 看到的 context」從 raw log 變成 compact, relevant, decision-ready state。
- **Why（為什麼重要）**:
  主人目前的多代理人流程雖然能跑，但 handoff 主要靠 Kanban card body 全文傳遞——隨著任務拉長、sub-card 變多，這種「全丟」的成本與雜訊會線性惡化。論文證明了可以用 learned gate 把成本壓 31.9%、accuracy 反而升 2.44，這對主人 air-gap/downstream 的「保守減法」哲學是直接補強。
- **How（如何運作/實作）**:
  - 每一步先由上一棒的 execution memory + 當下 query 一起送進 router。
  - Write Gate 對新生成的 reasoning step 打分，只 accept 「非冗餘」的步驟；其餘不寫入。
  - Retrieval Gate 對該 agent 角色篩出相關子集，避免 O(n²) 注意力被稀釋。
  - Halting Controller 監控「memory 是否已含足夠 evidence」，夠了就 stop，省下後續 round。
  - 對主人實務的啟示：Kanban card 的 body 可以借鏡同樣設計——default 給 compact summary + 關鍵 decisions + 失敗教訓三欄，raw transcript 改放 attachment。
- **Insight（個人心得）**:
  這篇論文最讓咱眼睛一亮的是 **Halting Controller**。主人之前明講過「executor 正常退出卻未呼叫 lifecycle completion 視為 protocol failure」，這正說明「會自己停下來」是 agent 系統的關鍵素質。Gated-Memory 的設計哲學——「不寫冗餘、不讀不相關、夠了就停」——其實正是主人一直在喊的「保留已驗證 runtime 與真實行為、保守減法、勿冒充完成」用白話文寫成的 ML 形式。下一篇可以觀察：當 router 自己失敗、寫了「看起來 relevant 但其實垃圾」的步驟時，這套 framework 有沒有 fallback？這才是企業落地會真正撞到的牆。
