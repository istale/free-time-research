# SkillEffect: Checked Lowering for Memory-Bounded Agent Tools
- 原始連結：https://arxiv.org/abs/2608.17007
- 來源：arXiv cs.AI 昨日新論文（2026-08-20 發佈，2026-08-20 11:30 Asia/Taipei 抓取）
- 閱讀時間：2026-08-20（午間）
- 作者：Yinuo Wang, Yiyu Shi（CUHK 合作）
- 與主人既有 axis 的對位：hermes-agent-lite / horo-agent tool dispatch + memory ceiling + 工具呼叫介面

## 摘要

**Agent Skills 通常以「自然語言＋少許約束」描述工具該怎麼用，但 LLM 把 skill 落到真實 tool interface 時，往往一個對的程式也會「整份輸入讀進來」瞬間爆掉單次呼叫的記憶體上限。** SkillEffect 把這個失誤型態形式化成「checked lowering」：先把 skill 編譯成一段 IR（Intermediate Representation）、再由獨立 checker 把 IR 從原程式重 build 一次，比對 immutable input，最後才放行到 bounded VM 執行。整個模型是「common runtime + relation plugin」：runtime 負責 selection、bounded execution、atomic capacity leasing、staged publication；每個 plugin 給出 source recognizer、input-fact extractor、bounded-IR constructor、arena-bound function、postcondition 五個零件。

**六個 operator family、同一份 contract、跨五種 execution pattern 跑通：** 從 streaming reduction 到 bounded-heap Top-k，新關係（XLSX onboarding）和新保留狀態模式（Top-k extension）都共用同一個 trust boundary。論文 audit 顯示 legal configuration 100% 接受、adversarial proposal 100% 拒絕，並且 peak memory 在 external cap 下「substantially reduces + improves completion」。這條 plugin boundary 是 architecturally general（每種關係要 audit）但**不是** automatic——這一點是 SkillEffect 對下游的真正訊號：trust boundary 是工程活，不是 LLM 給。

**為什麼值得主人看：** SkillEffect 描述的失敗模式——「語意對、記憶體爆」——正是 hermes-agent-lite 把 skill 文件餵給 16GB Mac 上的 Qwen3.8-27B 時最常見的 silent-failure 型態。今天早上 DFlash 2 解決的是 inference throughput（一次多吐幾個 token）；SkillEffect 解決的是 **agent-tool dispatch substrate**（一次工具呼叫別撐爆 RAM）。兩者互補：DFlash 2 把「每 token 成本」壓低，SkillEffect 把「每 tool call 容量」框住——主人 horo-agent lite 同時需要這兩層 guarantee。

**6 plugins × 5 patterns × 6 operator families × checker-accepted / adversarial-rejected** 是 SkillEffect 給的具體 anchor 數字：這個 audit envelope 對應主人目前 hermes-agent-lite tool dispatch 還沒有 formal checker 的現況，是少數能直接抄進 `horo-agent` tool-dispatch primitive 的 paper。

## 3W1H 分析
- **What（做了什麼/主題）**:
  SkillEffect 是一個 checked-lowering runtime：把 Agent Skill 文件先 lower 成 IR，由獨立 checker 從原程式 rebuild 一次比對，最後在 arena-bound VM 內執行；每種 bounded relation 是一個 plugin（5 個零件），runtime 共享 selection / bounded execution / atomic capacity leasing / staged publication。跨 6 個 operator family、6 plugins、5 種 execution pattern（包含 streaming reduction + bounded-heap Top-k）驗證同一 contract。
- **Why（為什麼重要）**:
  現代 agent runtime 一天消耗的 token 數遠超過 chat 時代，但**單次 tool call 的記憶體上限**才是更難察覺的隱形瓶頸——一個語意正確但 naive 寫的 tool implementation（例如把整份 200MB Parquet 讀進 pandas）會把整個 agent loop 撐爆，且 error 訊息不直觀。SkillEffect 給的不只是「bounded execution」這種大詞，而是 formal 的 recoverable source relation + audited bounded implementation + registered postcondition 三段式合約，能讓 hermes-agent-lite 把 tool dispatch 變成可被 lint / 可被拒絕的形式化介面。
- **How（如何運作/實作）**:
  - 每個 plugin 提供 5 件套：source recognizer、input-fact extractor、bounded-IR constructor、arena-bound function、postcondition
  - Common runtime 提供 4 件事：checked selection（IR 與原程式重建比對）、bounded-VM execution（arena 內跑）、atomic capacity leasing（capacity 原子租賃）、staged publication（執行結果分階段對外）
  - Adversarial test：所有合法 configuration 被接受、所有對抗性 proposal 被拒絕；XLSX onboarding study + Top-k extension 顯示新關係／新保留狀態模式都重用同一 trust boundary
  - 一般性是**架構的**而非**自動的**——每加一個新 relation 都需要手工 audit plugin，這是 SkillEffect 對下游的真實成本訊號
- **Insight（赫蘿心得）**:
  主人目前在 hermes-agent-lite 對 tool dispatch 的處理還是「寫 Python、走 subprocess、try/except 接 OSError」，沒有 formal memory bound。SkillEffect 給了一個**對手機的下游可用的** plugin 契約：horo-agent lite 完全可以定義一個 minimal 「3-piece plugin」（source recognizer + bounded-IR constructor + arena-bound function），先在 pandas + SQLite 兩條路上各做一個 plugin，就能在不重寫 hermes core runtime 的前提下把 tool call 變成 checked-lowering。這跟主人「保守減法、保留 runtime 介面」硬規則完全對齊——SkillEffect 的 contribution 是 plugin 契約、不是新 runtime，重點是 5 件套 contract 可以直接搬到 `horo-agent` 的 tool dispatcher 上，跟今天早上 DFlash 2 的 inference 加速疊起來正好形成「tool-call memory cap」＋「per-token throughput」雙層 substrate。