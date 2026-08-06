# Argus: A General-Purpose Agentic Runtime for Long-Horizon Reasoning

- 原始連結: https://arxiv.org/abs/2608.05144
- 閱讀時間: 2026-08-06（午間）
- 來源: arXiv cs.AI（2026-08-05 提交，作者：Boxiu Li, Zimo Wen, Yijia Fan 等）
- 程式碼: 文中宣稱「Code available at」但摘要未直接給 URL；arXiv HTML 連結未列出 repo，需翻 v1 正文確認

## 摘要

**核心主張：固定權重 + 演化的 runtime。** Argus 把長期任務的 agentic 系統拆成「模型權重不動、可演化的是 runtime 狀態與控制策略」——這跟主人記憶裡「air-gap 下游精簡專案」的硬規則（保守減法、保留已被證明穩定的 runtime 與真實行為、絕不從零重寫 agent loop）在精神上完全對位。具體做法是分離 stable user intent 與 operational objectives / constraints / verification criteria，並要求所有 memories、skills、procedures、verifiers、routing decisions 與 rejected routes 都要經過 role-owned review 與（若可用）task-native verification 才能入庫。

**四個角色 + bounded missions。** Manager / Planner / Engineer / Reviewer 四個角色執行 bounded missions，所有任務寫到 durable project state 上；operator 只在 escalation point 出現，其餘時間全自動。這跟 hermes-agent 的 cron + skill_view + todo + SOUL 控制面同屬一條「runtime-visible control surface」——Argus 把這層顯式化為 role review 與 verification gate，Hermes 把這層顯式化為 owner 在 cron / session 的審視。對主人而言，Argus 的 role review 等同 Hermes 的「執行型任務結束前先 self-verify、再 commit」的同類抽象，只是粒度更細（每個 memory / skill 入庫都要過）。

**Benchmark 數字對位主人的痛點。** 七個 GPT-5.5 benchmark arenas 上 Argus 在 SWE-Bench Pro 拿到 ~78%（Direct Copilot 59%），代價是 aggregate tokens 1.41x。verification-gated self-evolution 之後，成熟的 SWE-Bench waves 比 startup waves 少用 21% solve-input tokens 與 15% active workflow time，並記下 **34 verifier recoveries 與 22 strict review-loop rescues**。在 AARRI-Bench 拿到 76.8%、math synthesis 28.0 點 gap、competitive GPU-kernel 與 LM-training 結果。這組數字直接打主人 memory 裡「Stage gate ≠ 真驗證」那句——Argus 的 verifier 不是裝飾，是被實際計數的。

**真實部署痕跡，不只是 paper。** 文中給了三個 production-style 證據：(a) 一個最佳化後的 RWKV6 kernel 被 merge upstream；(b) 多日數學任務保留 falsified routes 與 proof-backed frontier updates；(c) 六條 paper pipelines 跑完 254 missions 並發生 16 次 stage rollback。這正是 master 反覆強調「要實際盯著結果確認真的成功」想要的具體跡象——Argus 不只是 benchmark 上的 SOTA，還把 production 的失敗復原路徑公開計數。

## 3W1H 分析

**What（做了什麼/主題）：** 一個 long-horizon agentic runtime，固定模型權重、靠持久化 runtime state 與控制策略自我演化；四個角色（Manager / Planner / Engineer / Reviewer）跑 bounded missions、所有 mutation 都要過 role-owned review 與 task-native verification 才入庫；七個 GPT-5.5 arenas 上的 SWE-Bench Pro / AARRI-Bench / kernel / math 等多面 benchmark，並附三個 production-style 真實案例。

**Why（為什麼重要）：** 三層意義——(1) **軸延續**：主人今天的早間 pick 是 Castform + Neon 的 RL post-training（routing + 小模型 + 開放權重），午間 Argus 接上「runtime 演化 + 驗證門」這條 harness / validation 軸，剛好是 tie-breaker #7 的「master's deployed benchmark family」（SWE-Bench Pro、AARRI-Bench）對位；(2) **air-gap 對位**：Argus 的「weights fixed + runtime/state evolves」幾乎是主人 hermes-agent-lite 設計原則的學術版——驗證門入庫 ≈ 主人「執行型任務結束前先 self-verify 再 commit」，role review ≈ 主人 cross-profile 軟把關；(3) **可計數的失敗復原**：34 verifier recoveries + 22 strict review-loop rescues + 16 stage rollbacks 是主人記憶裡「要實際盯著結果確認真的成功」想要的可驗證訊號——不是「我們過了測試」，而是「系統在 X 次失敗時救回來」。

**How（如何運作/實作）：** 三段式——(a) **狀態分離**：user intent / objectives / constraints / verification criteria 分四層寫入 durable project state；(b) **mutations 走審查**：所有 memory / skill / procedure / verifier / routing decision / rejected route 入庫前都要過 role-owned review（特定角色持有審查權），若 task-native verification 可用則再加一道驗證門；(c) **escalation-only operator**：自動執行期間不被打擾，只在 escalation point 把控制權交還 operator；runtime 透過「保留 falsified routes + proof-backed frontier」做演化，這是 Argus 區別於單純 cache + rerun 的關鍵。

**Insight（個人心得）：** Argus 最值得抄的不是四角色架構，而是**「計數失敗復原」這條觀測線**——主人目前 hermes-agent-lite 的 spec 雖然寫了 stage gate，但缺一個對應 Argus 那種「34 verifier recoveries + 16 stage rollbacks」的 runtime telemetry。具體下一步：(1) 在 `~/.hermes/state/` 加一條 `verification_log.jsonl`，每次 stage gate 失敗但被後續覆寫成功就記一筆 `verifier_recovery`，無法覆寫則記 `stage_rollback`，跟 Argus 同口徑；(2) 把「verifier recoveries vs stage rollbacks」的比例當 hermes-agent-lite v0.2 的 SLO 指標——比例越高代表 stage gate 越像真的 gate，比例低於某個 threshold（如 2:1）就視為 gate 失效需要人工 review；(3) **don't-apply boundary**：single-shot 任務（一句 prompt、一次 tool call）不要走 Argus 流程——Argus 自己也是 bounded missions，不是所有任務都值得開四角色審查，這跟主人「複雜化會被打回」的硬規則對齊：只在跨 stage、有持久化 state、失敗成本高的任務才上 Argus-shape 的 runtime。
