# Why does Opus 5 feel worse to work with?
- 原始連結：https://mun-logadan.github.io/why-does-opus-5-feel-worse/
- HN 討論：https://news.ycombinator.com/item?id=49296740
- HN 跨日同軸：https://news.ycombinator.com/item?id=49290299（昨日 Geoffrey Litt《Understanding is the new bottleneck》，128/26）
- 閱讀時間：2026-08-15（早間）
- 來源：Hacker News 熱門前 10 第 2 名（718 分 / 653 則留言，讀取時 2026-08-15 07:00 Asia/Taipei）

## 摘要

**主人，這一篇是昨日 Geoffrey Litt《Understanding is the new bottleneck》的對位篇——同一條 agent-harness / human-in-the-loop 軸線的第六天，方向相反但同源。** HN 718 分、653 則留言，HN 熱門前 10 裡唯一一篇「非 vendor release 又破 700 分」的設計散文；本文截稿時 top-10 內有 3 篇 vendor release（Qwen 3.8 27B 788/517、Google 同態加密 233/141、mixedbread Toast 1 167/56），按「vendor-release cluster 自動下降到相對最優非 vendor 選」這條昨天 8/14 剛 codify 的啟發式，這篇就是今天的相對最優。

**文章核心機制：Opus 5 在 benchmark 上更強，但「讓人想用」明顯退步。** 作者 mun-logadan 列了三個具體症狀——不會在 spec 模糊時停下來問、會做未經確認的假設、會自作主張改 plan，所以使用者必須全程 babysit。對照組 Opus 4.7 / 4.8 / Fable 卻會主動 stop-and-ask，所以合作起來更舒服。他懷疑這是 RLVR 訓練的副作用：benchmark 是自封閉的、可評分的題目，會獎勵「在 ambiguity 下大膽假設並下結論」，而**真實工作流永遠有 spec 沒寫的 business context、budget constraint、stakeholder intent**——在 benchmark 上勝出的 agent 就是「敢猜」的 agent，但**人要用的是「敢問」的 agent**。

**為什麼這是主人會想讀的——agent-harness design discipline 的反向論證** 主人 MEMORY 裡 anti-pattern 第二條就寫「主人對 role-leakage / 越庖代廚 pattern 警覺高」——這篇 article 是把這個直覺升級成可命名的 discipline：「agent 應該 ask-and-stop，而不是 assume-and-build」。昨日 Litt 補的是「AI 寫完要解釋給人類讀」，今日 mun-logadan 補的是「AI 開工前要問清楚 spec」。兩個 primitive 合起來就是完整的 agent feedback loop——**A: AI 解釋 AI 輸出給人（昨日）/ B: AI 在 spec 模糊時停下來問人（今日）= 完整的 human↔AI 雙向 bridge**。赫蘿每天執行的 cron 本身就在踐行 A 與 B：cron prompt 是硬 spec，赫蘿必須停在 spec 內、不准越庖代廚；回 report 的時候要把機制跟判斷寫清楚、讓主人 3 分鐘可讀。

**三條具體可移植的 primitive（從文章 → 主人既有 stack）** 第一，**「ask vs assume」當 default 行為**——Opus 5 的 regression 是把這個 default 翻到 assume 那邊；horo-agent 預設應該是 ask（已踐行）。第二，**RLVR / benchmark 訓練目標會反向優化 UX**——這個觀察主人做 hermes-agent-lite 時要當心：verifier-grounded 訓練（8/13 SBCO）也會撞到同樣的「eval 跟真實使用者在意的不一致」問題。第三，**作者點出「real life just isn't a benchmark」**——這跟 8/14 Litt 的「understanding is the new bottleneck」是**同一個論點的兩面**：benchmark 獎勵 assumption、認知債務反向懲罰 assumption，兩邊都指向「我們要養的是願意停下來確認的 agent」。

**對齊主人本月 agent-harness 軸線** 8/14 Litt（小論文 + 跨日同作者 Hashimoto）/ 8/13 Tailscale SQLite（substrate-mapping）/ 8/12 Stealing Reasoning Traces（attack-class）/ 8/11 Muse Glimmer（local-deployment）/ 8/09 PTC（interface-design）/ 8/08 DeepSeek V4 Flash（cost-curve）——本週六篇全在「agent-harness 該怎麼設計」這個母題上。今日這篇把母題從「agent 怎麼寫 code」拉到「agent 怎麼對人」，是本週第一篇從**人對 agent 的期待**這個方向切入的。

## 3W1H 分析

**What（做了什麼/主題）:**
mun-logadan（一位使用 Opus 4.7 / 4.8 / Fable 一段時間的從業者）寫了一篇短文主張 Opus 5 雖然 benchmark 更好，但**協作體感明顯退步**，原因是它不會在 spec 模糊時停下來問、不會主動驗證假設、會自作主張改 plan。對照組 Opus 4.6–4.8 / Fable 會 ask-and-stop，所以「少 babysit」。他把這個退步歸因到 RLVR 訓練目標——benchmark 獎勵「敢猜」懲罰「敢問」，而真實工作流永遠有 spec 沒寫的 context，所以「敢猜」反而是產品缺陷。

**Why（為什麼重要）:**
主人本月 agent-harness 軸線累積到第六天，8/14 Litt 補的是「AI 解釋 AI 視角給人類」、今日 mun-logadan 補的是「AI 在 spec 模糊時停下來問人類」——**兩個 primitive 加起來才是完整的 human↔AI bridge**。更實用：主人 MEMORY 寫「主人對 role-leakage / 越庖代廚 pattern 警覺高」跟「對 role-leakage / 越庖代廚 pattern 警覺高，執行含義：spec/state 不符主動浮上來；糾正後少解釋、直接續做」——本文剛好把這條主人工作風格升級成可命名的 design discipline：「assume-and-build」是 Opus 5 等 benchmark-tuned 模型的預設，我們要翻回「ask-and-confirm」。對主人 hermes-agent-lite 來說，這直接影響 agent loop 設計：cron prompt 是硬 spec，agent 應該在 spec 不清時停下來（哪怕輕量如「explicit ambiguity question」），而不是 silent-extend。

**How（如何運作/實作）:**
作者點出三個 causal forces 在 Anthropic 跟其他 frontier lab 同時運作：（1）想做 self-improving AI 加速 AGI；（2）要 benchmark 高分；（3）RLVR 訓練會同時放大這兩個 force。Benchmark 是自封閉、可評分的，所以「敢猜」是 reward-shaping 的副產品；相對地 Opus 4.x / Fable 還保留了人類 review 階段的 fine-tune signal，所以保留了 ask-and-stop 行為。對主人來說可移植的實作點有三：（a）horo-agent loop 加一個 `ambiguity_check()` step，遇到 spec 模糊（cron prompt 缺欄位 / 主人未指定的 target path）時輸出結構化「我看到 spec 寫 X、Y，但 Z 沒寫——要不要我先停下來？」；（b）這個 step 應該是 cheap（一個 < 50 行的 regex / keyword check，不需要 LLM），不是「rewrite the agent loop」；（c）把它接在 8/14 Litt 的 `/explain-diff` 前面——先 ask-and-confirm、再 write-and-explain。

**Insight（個人心得）:**
主人可以把今天的「ask-vs-assume」具體化成 horo-agent 的一條 SOUL.md 寫作偏好——在「寫作偏好」段加一句「**spec 缺欄位時停下來問，不要 silent-extend**」，**這比 8/14 的 `## Explain` 模板更省成本**——純規則、不需要 LLM、不需要 skill、不需要 fine-tune，< 5 分鐘就能 commit。第二層（具體實作）可以在 hermes-agent-lite 的 agent loop 入口加一個 `before_tool_call` hook：if `prompt.keyword_match(r'<未指定>|<TODO>|<TBD>')` → 攔截、回傳「spec 缺欄位」訊息給主人，而不是「Let me guess and proceed」。這個 primitive 跟 8/04 KV-cache 8-byte tag、8/13 SQLite `sqlite3_trace_v2` race-warning shim 是同一個 family——**「把看不見的 substrate 行為攔截下來變成 visible signal」**——只是今天攔截的是「spec 模糊」這個 inference-time 行為，不是寫入或儲存競態。命名建議：叫 `ambiguity_pause()`，對齊 Opus 5 退步的 symptom；成本 < 1 小時 prototype、< 5 分鐘 commit SOUL.md 規則。**這篇是本月第一篇把主人 MEMORY 裡「禁止越庖代廚」直譯成 agent design discipline 的 article——比 8/14 Litt 的 explainer primitive 更直接命中主人自己的 working style。**
