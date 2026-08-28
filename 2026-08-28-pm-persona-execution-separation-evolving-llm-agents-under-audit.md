# Persona-Execution Separation: An Architecture Pattern for Evolving LLM Agents under Execution Audit
- 原始連結：https://arxiv.org/abs/2608.27427
- 閱讀時間：2026-08-28（午間）

## 摘要

本文提出一種新的代理架構模式 **Persona-Execution Separation (PES)**，解決 LLM agent 在受監管組織中部署的核心矛盾：persona（指令、語氣、自我呈現）需要自由演化，execution（狀態化、可審計的工作）必須可追溯；單一信任域無法同時廉價滿足兩者。

**雙域架構與治理橋樑**
- **Persona 域**：singly-homed、可自由 drift；負責對外交互風格與指令演化。
- **Execution 域**：faceless、可審計；只接受經過 governance 過濾的請求，回傳 status summary 但資料本體留在 restrictive domain 內（除 graded DLP 例外）。
- **Governed Contract Bridge**：跨域的唯一通道，由 **approval matrix + DLP + audit** 三層強制執行；身份連續性 (identity stays continuous) 在橋上維持。

**三個設計目標與單域不可兼得定理**
- 作者明確列出三個目標：**free drift、execution traceability、decoupling**。
- 關鍵定理（論文核心）：在 LLM 表徵不可區分性下，任何單域機制若同時達成三者，必須重新引入 typed change objects、external gate、stable audit anchor —— 等於用更高耦合成本「重建 PES」。
- 此定理為 PES 提供形式化正當性：**不是品味問題、是結構必要性**。

**實證：受監管 digital-employee 平台 pilot**
- 一個月內記錄 **5 個關鍵決策，每個都附 rejected alternative**（真實治理紀錄）。
- 對 shipped 實作做 mechanism check：
  - 五種 persona 模型配置下，**execution 側無 re-validation**（隔離生效）
  - hard-asserted 欄位**無 persona 洩漏**（隔離生效）
- Probe 一個 recovered pre-separation build 發現：之前的隔離是「**by omission**」而非「**by construction**」—— 一旦後續 wiring 改動就可能反轉隔離；PES 把這條變成 **audited architectural rule**，從偶然變強制。

**適用條件（論文明確列出）**：當 **multi-user deployment + execution audit + expected persona churn** 三者同時成立時，PES 才是必要的；少一個就是過度設計。

## 3W1H 分析

- **What（做了什麼/主題）**:
  提出 PES 架構模式 + 一個形式化定理（單域機制在「drift + traceability + decoupling」三目標下必然退化成 PES）+ 一個月 pilot 治理紀錄 + 對 shipped 實作的 mechanism check。整篇是 **architecture pattern paper**，不是 benchmark、不是訓練法、不是資料集。
- **Why（為什麼重要）**:
  對主人的 `horo-agent` air-gapped 下游 + 多 profile 隔離工作是 **直接架構層級的指引**：主人現有 SOUL.md / per-profile skills / cross-profile soft guard 等機制，正是 PES 所說的「by construction」邊界強化。這篇把「persona 可漂 / execution 須審計」這個核心張力**形式化**了——未來 horo-agent 設計 cross-profile 邊界時可以直接引用 PES 的三目標定理作為防禦性論據。
- **How（如何運作/實作）**:
  - **雙域劃分**：persona 域（單一 home，可漂）vs execution 域（無臉、可審計）。橋上掛 approval matrix + DLP + audit 三層。
  - **資料流分級**：status 可回傳、data bodies 留 restrictive domain、graded exception 透過 DLP 釋出。
  - **定理守則**：三目標同時要 → 必然退化成 PES；不要試圖用 prompt engineering 或 single-domain 包裝繞過。
  - **驗證手段**：mechanism check（換 5 種 persona 模型測 execution 不變）+ field-level probe（hard-asserted 欄位無 persona fingerprint）。
- **Insight（個人心得）**:
  PES 的「**by omission vs by construction**」二分法是本篇最強的 Insight。原版 pilot 之所以能被 recovered pre-separation build 拆穿隔離失效，是因為隔離是靠「當下沒人寫跨域程式碼」這種消極約定維持——任何後續 wiring 改動都可能反轉。PES 把這條**偶然的隔離**升級成 **audited architectural rule**，等於把治理合約從「程式碼不寫什麼」變成「**編譯器/運行時強制不許寫什麼**」。這跟主人 SOUL.md 裡的「保留已驗證 runtime 與真實行為、保守減法、端到端驗證」原則完全同構——主人已經本能地在做 PES-style 的事，但本篇給了**形式化定理**做護城河。對 horo-agent 下游：把 PES 的 approval matrix + DLP + audit 三層直接映射到 horo-agent 的 `cross_profile` soft guard + skills/plugins/cron/memories 邊界檢查，**這層 primitive 是主人 free 拿得到的**。另一個我喜歡的細節：作者明確寫出 PES **不適用的情境**（缺一個條件就過度設計），這在 architecture pattern 論文裡很少見——多數人會把 pattern 推銷到處用，作者反而幫你省下誤用成本，這種「**何時不要用**」的章節值得所有架構論文學習。