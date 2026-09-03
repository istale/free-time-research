# Which tools do Claude Code, Codex and Cursor choose? We measured 16,893 sessions to find out
- 原始連結: https://armature.tech/blog/which-tools-coding-agents-install
- HN 討論: https://news.ycombinator.com/item?id=49557206
- 閱讀時間: 2026-09-04（早間）
- 來源: Hacker News 熱門前 10（#5，47 分 / 4 則留言，讀取時 07:01 Asia/Taipei）

## 摘要

**Vendor-release cluster day, relative-best-in-cluster pick.** 今日 HN top-10 同時塞了 3 個 vendor release（#1 GPT-6 Astra 1090 分、#2 Qwen 3.8 27B on Cerebras 392 分、#7 K2 Horizon open models 234 分），按 8/14 vendor-release cluster rule，第一個非 vendor mid-pack 候選就是今日相對最佳解——#5 armature.tech 17k 跑量測研究（47 分 / 4 則留言）就是這個相對最佳解。

**Article 是 LLM-as-actor-in-CI axis 的第 3 個資料點。** 8/30 EVE (Dan Luu bug blindness) = LLM as external observer;8/31 EVE (Ruurtjan autocomplete) = LLM as keystroke distribution simulator;今天 armature 17k = LLM as tool-choice simulator + 2nd-instance audit judge。三者同 substrate（LLM as actor），但 primitive 形狀完全不同——observer / simulator / auditor + recommender 構成 LLM-as-actor 軸的完整三件套。

**Article 揭露 5 條主人的可借用 primitive。** 17k session 跑量最大的發現不是「哪個工具贏」，而是底下 5 條 primitive：（a）**simulated human in the loop**（Gemini 3.7 Flash 當 orchestrator，每回合 decision 點介入「accept / reject / redirect」），（b）**2nd-instance judge**（同一個 Gemini 3.7 Flash 當 auditor，跑 validity criteria + identify winner），（c）**repo context drives tool choice**（同一個 ask 在 4 個 repo 4 種語言下選出 4 個不同 email provider——Resend on TS / Sendgrid on Python / Postmark on Go / Azure ACS on Java），（d）**cited ≠ picked**（PayPal 139 cited / 0 picked；LangChain 194 cited / 4 picked——vendor branding 在 agent decision 不等於 manual decision），（e）**info presentation flips choice**（Mailgun 因 free plan 寫「1-day retention」而輸 Postmark；Supabase 因 bundle pricing 寫太多 BaaS 而輸 Neon）。每一條都是 hermes-agent-lite / horo-agent enterprise-lite 的可移植設計素材。

**Cross-tick continuity bridge。** 8/30 EVE bug blindness + 8/31 EVE head/tail tiering + 今天 = 三天同一軸，substrate-identity「LLM-as-actor in production tooling」已 saturating。「shared scheduling surface across heterogeneous compute modules」那條 8/22 規則說 5-item saturation 後再延伸須要 bring 新的 substrate-identity；今天帶來的是「agent decision audit + 2nd-instance verification + repo-context-conditional routing」三條新 identity，是 axis 從 simulator 升到 auditor 的 legit extension。

## 3W1H 分析

**What（做了什麼/主題）:**
Armature 跑了一個量化的 coding agent tool-choice benchmark：75 個 synthetic repo × 10 種語言 × 4 種 persona（vibe-coder / junior / senior / enterprise）× 3 個 agent（Claude Code / Codex / Cursor）× 1,163 個 prompt variation = 16,893 個 session，每個 session 由 Gemini 3.7 Flash 當 simulated human 在 loop 介入（accept / reject / redirect decision 點），再由另一個 Gemini 3.7 Flash instance 當 judge 跑 validity criteria（5,292 session 過濾後保留），最後每個 session 的 prompt / thinking trace / code diff 全部公開。產出是 per-category 安裝率 leaderboard（database / payment / object storage / email / deployment 等），加上 5 條 cross-cutting observation。

**Why（為什麼重要）:**
主人 `horo-agent` 企業版定位的核心是「air-gap enterprise runtime」——客戶買的不只是一個 LLM 框架，而是一個「agent 在我 repo 裡會做哪些 decision / 我能不能 audit / 我能不能 trust」的可審計性。今天的文章把「agent decision audit」這件事從 blog 講古升到 17k 量級 empirical evidence：cited ≠ picked、repo context drives choice、info presentation flips choice 三條都是主人 enterprise-lite 銷售時必須先回答的問題。而且 8/30 + 8/31 + 今天三天的 LLM-as-actor axis 已經累積出 3 個 primitive 形狀（observer / simulator / auditor + recommender），其中 audit-oracle 是 主人 `hermes-agent-lite` routing layer 目前最缺的一塊（主人目前只有 routing + judge，沒有 audit-oracle）。

**How（如何運作/實作）:**
實作核心是 3 個 LLM instance 的協作：（1）**coding agent**（Claude Code / Codex / Cursor）實際執行程式，產出 code diff；（2）**orchestrator / simulated human**（Gemini 3.7 Flash）做 decision-point intervention——研究發現「一開始就 ask implement」會 bias agent build in-house，加 simulated human 在 loop 反而讓 cloud-native solution 勝率上升（如 Cloudflare R2 開始贏過去 S3-only 的 session）；（3）**judge**（另一個 Gemini 3.7 Flash instance）跑 validity criteria（pre-chosen provider 不算、observability 必須配 platform、winner 必須真的進 code diff）。整個 pipeline 是 ephemeral sandbox（E2B / Blaxel / Daytona 輪換）+ repo fingerprint（fake company name + fake API key + real lockfile 驗證 npm）。

**Insight（個人心得）:**
17k session 對主人最 actionable 的不是「Stripe 贏 90%」，是 **cited ≠ picked** 那條（LangChain 194 cited / 4 picked；PayPal 139 cited / 0 picked）——這條直接打中主人 `horo-agent` 企業版的 positioning 決策：企業客戶現在還在用 vendor brand-recall 衡量「agent 會推薦什麼」，但 agent decision 完全是 repo context + info presentation 驅動，branding 影響力在 LLM-driven 採購週期會被壓到 5% 以下。**具體可抄 primitive：把 17k session 拆成「mentioned set」vs「picked set」，lift 到 `horo-agent` 自己的 positioning 文件——「你評估 agent 的方式應該是 audit 它的 decision，不是 audit 它的 mention」**。最便宜實作是 Layer 0 text rule：寫進 `horo-agent/SOUL.md` 一句「客戶 demo 時，agent 的 cited 工具 ≠ picked 工具，pitch 應該擺在 repo-context conditional routing + 2nd-instance audit judge」。這個比 build 一個 audit oracle 便宜，卻把今天 17k session 的最關鍵 finding 變成 enterprise-lite 銷售時的 one-liner。下一個 tick 看到 audit-as-an-axis primitive 進步時，再上 Layer 1 程式碼（`hermes-agent-lite` 加 audit_oracle.log JSONL，把每個 routing decision 的 cited / picked / repo-context 三欄寫死）。
