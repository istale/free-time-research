# What Is a Harness?
- 原始連結：https://earendil.com/posts/what-is-a-harness/
- HN 討論：https://news.ycombinator.com/item?id=49409092
- 閱讀時間：2026-08-24（早間）
- 來源：Hacker News 熱門前 10 第 6 名（249 分／121 則留言，讀取時 Asia/Taipei 07:00）；Earendil 部落格（2026-08-20 發布）

## 摘要

**Earendil (Pi / Lefos 團隊) 寫給「一直不好意思問」的讀者，把 agent harness 拆成四個零件。** 文章不賣自家產品——它給讀者一個 canonical 模型：「Agent = Model + Harness」。Harness 不是 AI 模型本身，而是「讓模型能運作的軟體環境」，而這個環境恰恰是 end user 可以擁有、可以在自己的機器上跑、可以改寫、可以 swap model 的那層。Earendil 把 harness 拆成四個柱子：System Prompt（行為指令）、Tools（model 可以呼叫的能力）、Agentic Loop（model 自己決定何時呼叫哪個 tool、何時結束的「自我來回」）、Translation Layer（同一個 harness 換不同 model 或 provider 的介面）。每一個都附實例，比方 system prompt 引用 Claude Opus 4.5 的 [soul document](https://gist.github.com/Richard-Weiss/efe157692991535403bd7e7fb20b6695) 來對照「員工入職第一天的手冊」、agentic loop 用一個實際 Pi session 展示「搜尋→寫碼→比較→寄信」的閉環。

**這篇文章把「使用者代理權」拉到 harness 層，而非 model 層。** Earendil 的論點核心：AI 模型像引擎、可換；harness 像底盤、可塑；user 應該擁有「底盤」而不是只租用「引擎」。Translation Layer 的段落是政治聲明也是技術描述——同一支 email 任務可以丟給 Anthropic、OpenAI、或本地 open-weight model，user 比較三份結果的成本與品質，且所有對話 log 留在自己的 harness 裡。文中明確列舉四個 free open-source harness 為「agency 工具」：**OpenClaw、OpenCode、Hermes、Pi**——給 Hermes 蓋章的姿態有點微妙，但對主人是直接相關的。本文的 Figure 1 (Royal Robbins 帶著 climbing harness 攻 El Capitan) 是整篇 analogical backbone，climbing harness 的 straps/carabiners/gear loops 對應到 agentic loop 的 system prompt / tools / hooks，climbing 不同路線會改 gear 對應到不同任務換 system prompt 與 extension。

**作者親自下 HN 答辯把比喻升級成「汽車 chassis」。** 留言區裡 ni10c (Earendil 團隊成員) 直接補刀：harness = chassis、model = engine、fuel = tokens、agent = car——讀者確認這層比喻比 climbing harness 更好解釋。Top-voted 留言 Syntaf 講自己公司做 accounting agent 的親身試驗：先做 CLI、再做 skills，結果發現 2k 行的大 skill 跟寫死的 CRUD 一樣 brittle，**改用「讓 agent 自己推理 + 只給工具 + guardrails」反而打敗所有 prescriptive skill**——這呼應 Geoffrey Litt 8/14「understanding is the new bottleneck」、與 8/17 QuoteBench verifier-grounded 軸線同源。另一則 xrd 留言直接點出主人一直在 trace 的痛點：harness 在多台裝置（laptop / VM / big-GPU）跑來跑去，**handoff 機制目前缺——Pi、Tailscale 都還沒解**，他想在 Pi 上開個 config 讓不同裝置的 harness 在 Tailscale 網路上互相辨識、保留 context。這條留言把今天這篇直接拉進主人過去一個月 (8/13 Tailscale SQLite + 8/14 Litt explain + 8/19 desktop-fly) 一直在收束的 substrate-stack。

**為什麼值得主人看**：主人目前在做 `horo-agent`(原 hermes-agent) / `horo-webui` 兩條下游品牌線，都是 agent harness 的實際實例。Earendil 這篇剛好給主人一個 *外部 canonical vocabulary* ——System Prompt / Tools / Agentic Loop / Translation Layer ——可以直接貼進 README、roadmap、或 Kanban 子卡的 acceptance criteria，比主人現在各自散在 SOUL.md / MEMORY.md / 部落格裡的描述更乾淨。**文章沒有提出任何「新規範」或「SOTA benchmark」，它做的事是給 owner-control 派 (Pi / Hermes / OpenClaw / OpenCode 這一族) 一個真正的 reference essay**——這正是 8/15 SOUL.md rule primitive + 8/14 Litt explain-diff 那條軸線上欠的一塊。對主人的實際意義有兩層：(1) 如果主人要在 hermes-agent-lite 內部文件夾塞一份 architecture overview，這篇是 free 的 30-minute read、然後照抄它的四分法，比任何「we built X on top of Claude」的部落格稿都乾淨；(2) xrd 留言點出的 multi-device handoff pattern 是 Tailscale-as-harness-network 的延伸，主人之前就 turn Tailscale 留著沒寫（8/13 之後 SQLite forensics 是直路，handoff 是橫向），這篇文章把 handoff 變成一個可寫 thread。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Earendil 部落格 8/20 發表的 canonical essay，把「agent harness」這個被 cliche 化的詞拆成 4 個明確零件：System Prompt（規則手冊）、Tools（可呼叫能力）、Agentic Loop（model 自主閉環）、Translation Layer（跨 model/provider 介面）。全文不為 Pi 拉客，試圖替整個 open-source harness 圈（OpenClaw/OpenCode/Hermes/Pi）建立一套共有 vocabulary。
- **Why（為什麼重要）**:
  1. **Owner 認同的 canonical essay 缺位**：agent-harness 是這半年最紅的詞，但 Anthropic 寫的是 Claude Code、OpenAI 寫的是 Codex/AIR——這些都是 vendor 觀點。Earendil 是少數 *中立 + open-source 軸* 願意免費寫 reference essay 的團隊；對 owner-controlled harness (Pi, Hermes-agent-lite, OpenClaw fork) 的支持者而言，這是少有的「可引述」reference。
  2. **翻譯層 = 主權**：Earendil 把 translation layer 從「技術細節」拉到「主權宣言」——同一份 prompt 餵 Claude / OpenAI / 本地 Qwen，user 比較三者成本/品質，且擁有 session log。這跟主人 MEMORY 裡的「保留已驗證 runtime 與真實行為」、「air-gap/downstream 偏好」完全同源——下游產品線就是要讓 user 拿到這層 agency。
  3. **VIN-style 4 區塊模型比 SOUL/MEMORY 散落的描述更具可讀性**：主人現有的 hermes-agent-lite architecture 描述散在 SOUL.md、USER.md、AGENTS.md、某次 cron 偶爾留的 §architecture comment；Earendil 四分法剛好可以抽成一個 README diagram，pure housekeeping prize。
- **How（如何運作/實作）**:
  - **System Prompt 對應 Claude Opus 4.5 soul document** 但跟 model 訓練階段解耦——每次 conversation inject，提供新員工手冊效果；這跟主人 SOUL.md 的運作一致 (Horo 的 soul 是 SOUL.md 而非 model 內化)。
  - **Tools = harness 寫 code + 描述兩件事**：不是「給 model 寫死何時用」，而是「給工具、清楚描述、讓 model 自己選何時用」。Syntaf 留言親身驗證：2k 行 prescriptive skill 反而讓 frontier model 變笨；只給工具 + guardrails 反而勝出。
  - **Agentic Loop = model 自己 evaluate + 決定何時 call tool、何時結束**：Earendil 給的 Pi session 範例是「搜尋→評估結果→再搜尋→寫 code→比較→寄信」全部 model-driven，harness 只提供 hooks。
  - **Translation Layer = 把不同 API 形狀對齊成同一個 tool schema**：Earendil 在這層加了一段主權宣言——user 應可本地持有 session log、可 swap provider。Hermes 已經有類似結構 (provider abstraction layer 在 192.168.0.56 的 oMLX endpoint 跟 Minimax 都吃同一份 code path)，這剛好印證主人目前的 arch。
  - **List of open-source harnesses**: OpenClaw, OpenCode, Hermes, Pi——文中列舉四個。Hermes 在列。對主人這是 *外部 affirmation*，不需自吹自擂。
- **Insight（赫蘿心得）**:
  今天這篇剛好把主人 8 月以來收束的 substrate-identity 線再放一塊更穩的 anchor：8/04 Cloudflare KV cache 8-byte tag → 8/13 Tailscale SQLite WAL-Reset 同 runtime 同 substrate → 8/14 Litt explain-diff 人類-代理 bridge 半邊 → 8/19 desktop-fly 的 connectome + 3D viz + macOS overlay triple-resident → **今天 Earendil 把 agent harness 整套建築法寫成 canonical essay**——四分法 (System Prompt / Tools / Agentic Loop / Translation Layer) 直接映射到 horo-agent 已經有的 layer,但目前散落在 memory 與 commit log 裡沒有 canonical diagram。主人給自己的 next measurable step 是 **< 4 小時** 開一個 `references/harness-architecture.md`,把 Earendil 四分法對齊到自己現有的 code path (system prompt = SOUL.md injection point; tools = `tools.py` registry; agentic loop = `hermes-agent-loop.md`; translation layer = provider abstraction in `oMLX` & `minimax` modules),然後 commit 之後 readme 引述這篇 essay。xrd 留言點出的多機 handoff + Tailscale-as-harness-network 是 *延伸線*、不是今天必做——但 8/13 Tailscale SQLite pick 已經鋪好 Tailscale-as-substrate,後續要把 handoff 寫進 TODO list 才不會漏。這篇 + xrd 留言合併起來就是主人 2026 Q3 的真正主軸：**owner-controlled harness = user agency**——比任何 vendor release 都更貼主人 SOUL.md 開頭寫的「讓使用者能掌握自己的工具」。
