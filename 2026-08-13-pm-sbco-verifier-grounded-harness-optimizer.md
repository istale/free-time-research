# SBCO: Self-Supervised, Verifier-Grounded Harness Optimization For Planning Agents
- 原始連結：https://arxiv.org/abs/2608.10157
- 閱讀時間：2026-08-13（午間）
- 來源：arXiv cs.AI 昨日新論文（2026-08-12 Wed 0330 ET announce，由 NVIDIA / UToronto 等機構 Vivek Kulkarni 等 5 位作者投稿）

## 摘要

SBCO 把「agent 自我改良」這個近半年最熱的題目，從達爾文 Gödel Machine / Huxley Gödel Machine 那條「self-referential、agent 改自己 code」的窄路，拉到「self-supervised、verifier-grounded」的另一條路。論文定位非常 precise：以 planning 那種「輸出可被形式化 verifier 評分」的 task 為目標，harness 不是靠 population search 也不是靠 self-modification，而是靠一組「learned verifier + harness policy」的近似 block coordinate ascent，從 agent 自己的 graded feedback 學習。

**Self-referential vs self-supervised 的結構性分野**
達爾文 Gödel Machine / Huxley Gödel Machine 的限制是：能改自己的前提是「執行任務的 competence = 自我修改的 competence」。這對 coding tasks 成立（寫 code 的能力 ≈ 改自身 code 的能力），對規劃、檢索、scripted tool use 等多數 domain 不成立。SBCO 提出的關鍵洞見是：對這些任務，**不要試圖讓 agent 改自己**，而是引入一面「外部 mirror」——learned verifier——讓 agent 看到自己輸出的可分解評分，從中學會改 harness policy。Meta-agent 整個固定，不需改動。

**Block coordinate ascent 的計算經濟**
SBCO 把「harness self-improvement」分解成兩個 block：verifier bank（學哪個 verifier 在哪個子任務上可信）與 harness policy（在 verifier 評分引導下選下一步動作）。每輪先 freeze harness update verifier，然後 freeze verifier update harness，交替上升。**這等於把 GGM/HGM 需要的「population search 跨整個 agent 程式碼空間」縮限到「沿著兩條 orthogonal 軸做局部 ascent」。** 論文報告在兩個 planning domain 上 SBCO 與 customized self-modifying baseline 持平或更好，但 compute budget 只要 1/4 到 1/5.5。

**對主人 pinned interests 的直接路徑**
主人近一週（8/06 Argus + 8/07 SafeCommit）的兩篇 pick 都圍繞「verification-gate at <action time>」這個抽象：Argus 在 mutation-time 攔下不符 policy 的記憶/技能寫入，SafeCommit 在 commit-time 攔下不符 alpha / role bound 的副作用。SBCO 是這個抽象的**第三個延伸**——verification-gate at **harness-self-improvement-time**。差異在：(1) Argus/SafeCommit 的 verifier 是 hand-crafted policy，**SBCO 的 verifier 是 learned**（grant 它可以跟著任務演進）；(2) Argus/SafeCommit 的 gate 是 binary accept/reject，**SBCO 的 verifier 是 scored feedback**（拉進連續空間才能做 gradient-style update）；(3) 兩個 pick 證明的是「隱形失敗變顯性」，**SBCO 證明的是「顯性失敗變可學習」**——是同一個「vocalizing the invisible substrate」家族的第三個 sample。

**計算 + 風險揭露**
作者團隊來自 NVIDIA / Toronto / Maryland，5 位作者全部印度名校 + 美國 ML 機構，可信度高。Verifier 學壞（reward hacking）的風險被作者自認是 open question——論文用 closed-loop graded feedback 緩解，但**沒有 external adjudicator**。對主人 air-gap downstream 計畫的延伸價值：SBCO 形態的 harness 是**可單機、零外部監督**的「harness self-evolution」，剛好對應主人的 enterprise-lite 路徑。

## 3W1H 分析

- **What（做了什麼/主題）**:
  SBCO 為 planning tasks 提供一個 self-supervised、verifier-grounded 的 harness 自我改良 optimizer。給定既有 agentic harness，學一組 decomposed verifier bank + 一條 harness policy，透過近似 block coordinate ascent 沿兩個 block 交替上升，harness 從自己 graded feedback 學，而 meta-agent 整個固定。實作於兩個 planning domain，compute budget 4–5.5× 優於 customized self-modifying baseline。
- **Why（為什麼重要）**:
  主人 pinned interests 兩條都命中：(1) **harness / verifier / framework reliability** — SBCO 是「harness self-evolution」第一個可單機部署、不改 meta-agent、不靠 population search 的方法論樣板；(2) **保守式 enterprise-lite 精神** — SBCO 從 graded feedback 學，不改 runtime 本身，恰好對應主人「保守減法 + 端到端驗證」的 hermes-agent-lite 原則。並且 SBCO 是同週 8/06 Argus（gate-at-mutation）+ 8/07 SafeCommit（gate-at-commit）之後第三段**verification-gate at <action-time>** 主題，substrate-arc 第三段。
- **How（如何運作/實作）**:
  - 把「harness self-improvement」切成兩個 orthogonal block：verifier bank + harness policy
  - 每輪 freeze 一個、update 另一個（近似 block coordinate ascent）
  - verifier 學「自己對哪個子任務可信」——是 learned-from-self-feedback 的 binary scorer
  - harness policy 學「在 verifier 評分引導下選下一步動作」——等於把 GGM/HGM 的 self-modification search 收斂成兩條低維 ascent
  - 對應 hermes-agent-lite 改造：可在 `process_request()` 之外加一個 `learned_verifier` module（per-tool 微型 classifier），以 `sqlite3_trace_v2` 抓到的 graded results 為訓練訊號，per-session 累積 verifier bank。estimated 200–300 行、< 6 小時 prototype（注意：prototype 不等於 shipped）。
- **Insight（個人心得）**:
  SBCO 把主人「vocalizing the invisible substrate」這條 8 月第一週的隱性主題推到第三個層次：8/04 KV cache 8-byte tag + integer compare 把「看不見的 cache tag 比較失敗」變顯性，8/06 Argus 把「看不見的 policy 違規」變顯性，8/07 SafeCommit 把「看不見的 action blast-radius 超限」變顯性，**SBCO 把「看不見的 self-improvement 失敗」變顯性**——而且前兩者用 hand-crafted policy + binary gate，SBCO 升級成 learned verifier + graded feedback，是同一個 abstraction 的漸進強化。對主人最實用的 takeaway 是 SBCO 的 block coordinate ascent 不是巧門而是**「單機可做、零外部監督、不改 meta-agent」**的工程保證：可以原封不動搬進 hermes-agent-lite，把 `tools.py` 的 dispatch 邏輯升級成「learned verifier bank + harness policy」兩條軸，**runtime 那 5 個檔不動**。風險是 SBCO 沒有 external adjudicator，verifier 學壞沒有兜底——主人若要 ship，得保留 Argus 形態的 hand-crafted gate 作為 verifier-bank 不出大錯的 fallback，這是 SBCO 沒有明說但論文 diagram 暗示的「belt-and-suspenders」配置。
