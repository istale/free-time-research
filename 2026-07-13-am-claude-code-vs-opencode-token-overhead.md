# Claude Code Sends 33k Tokens Before Reading Your Prompt (Systima 比較 OpenCode)
- 原始連結：https://systima.ai/blog/claude-code-vs-opencode-token-overhead
- 閱讀時間：2026-07-13
- HN 來源：https://news.ycombinator.com/item?id=48883275（376 分，2026-07-12 發燒榜第一）

## 摘要

Systima 把 Claude Code 與 OpenCode 接到同樣的 Sonnet 4.5 / Fable 5 模型與同一台機器，中間插一個 logging proxy 抓 raw payload + usage block，量化「agent harness 真正送出去的 token 量」。結論：**Claude Code 在使用者送出 prompt 之前，就已經塞了約 33,000 token 的 system prompt + tool schemas + 注入式 scaffolding；OpenCode 只有約 7,000。** 同樣任務、同一模型 — 差距高達 4.7×，切到 Fable 5 縮小到 3.3×，但仍遠高。

**Floor 構成拆解**
Claude Code 的 33k token baseline 中，約 24k 是 tool 定義本身（共 27 個 tool，從 CronCreate、Monitor、Task family、worktree、通知一路包到 coding 核心）；3 個 system-reminder 區塊（agent delegate catalog、skill catalog、user context）佔了約 8k；system prompt 本體 6.5k。OpenCode 1 個 system block + 10 個 tool，tool schema 只佔 4.8k。**Zero-tools 隔離實驗證明，光是「行為教條」（tone、safety、task management、env）就佔了 Claude Code 剩餘 6.5k token 的一半以上。**

**真實工作流的 Multiplier**
1. **72KB AGENTS.md 指令檔**：兩邊都吃，**額外加 20k token/request**。Claude Code 2.1.207 只認 CLAUDE.md，AGENTS.md 會被默默忽略 —「silent ignored」是最貴的 bug。
2. **MCP servers**：每個小型 server +1,000–1,400 token/request；五個 server 把 tool 數從 27 推到 69，schema 直接膨脹。
3. **Framework templates**（BMAD 類）：8,400 char template ≈ 2,100 token，且每個 request re-carry；9 輪 session re-pay 9 次。
4. **Subagents**：直接做 T3 是 121k token，**fan-out 兩個 subagent 直接變 513k token（4.2× 乘數）**。每個 subagent 自己付 bootstrap，parent 再吞它的 transcript。
5. **Extended thinking**：thinking block 走 output rate（5× input），且永久進 history 一起 re-send。

**Cache economics — 最關鍵的發現**
兩邊的 cache breakpoint 都設得正確，但 **prefix 穩定性天差地遠**：
- OpenCode：每個 request 的 tools bytes + system bytes + message bytes **byte-identical**，三次 run 寫 0 cache token，全部 read。
- Claude Code：每個 session 有 3 個 distinct request class（warmup probe / main / subagent），每個 class 自己一份 cache entry；system bytes 跨 session 不一致；first-message scaffolding 跨 run 不一致。
- 結果：同樣的 file-summarise 任務，Claude Code 寫 53,839 cache token（含一次 43k prefix 完整 re-write），**OpenCode 只寫 1,003** — **54× 差距**。
- Cache write 走 1.25× premium（5 分鐘 TTL）或 2×（1 小時 TTL）；**cache re-write 是純粹的浪費**，跟品質無關。

**完整工作流**
OpenCode 配 11 個 MCP server + 72KB 指令檔，第一次 request cold cache 寫 **90,817 token**，帶 179 個 tool、277KB schema。Claude Code 4 個 MCP + 套件 + 同指令檔 = **~75k token / 311KB payload**。減掉 gateway envelope 後這是 floor 的 **~12× 乘數**。

## 3W1H 分析

- **What（做了什麼/主題）**：
  Systima 在 2026-07-12 發布的 harness 比較研究，以 logging proxy 直接觀察 Claude Code 與 OpenCode 對 Anthropic API 發出的 raw JSON payload 與 usage 區塊。涵蓋 floor overhead、cache 穩定性、5 個現實 multiplier（指令檔、MCP、framework、subagent、extended thinking）、以及 EU AI Act Article 12 角度的 audit trail。同時附 @systima/aiact-audit-log 把 185 筆 request/response 寫成 SHA-256 hash-chained log，驗證後 hash chain 完整無 break。

- **Why（為什麼重要）**：
  Owner 正在跑的 Hermes / Agent Share / LMStudio Council 多 agent 設定，subagent fan-out + MCP server + workspace instruction 全部齊備 — **這篇文章每一個數字都直接打在我們的 token 帳單上**。更關鍵的是 cache re-write：如果 prefix 不穩定，1.25× / 2× 的 write premium 會吃掉所有 cache discount，dashboard 看似神奇爆漲其實是 harness 自己的 prefix 在跨 round 變動。對 agent 經濟學（token-economics-of-enterprise-agentic-ai / harness-effect 那條線）來說，這是第一次有人把「harness 不是 free」的帳目攤開。

- **How（如何運作/實作）**：
  - 方法論：在 harness 與 model endpoint 之間插 logging proxy，capture request payloads + usage block；payload 是 ground truth，metered usage 是 ground bill。
  - 隔離：fresh config 目錄、無 MCP、無 user setting、無 memory、空 workspace 跑 floor，multiplier lane 一次一個變數加入。
  - 三個任務：T1（說 OK / 22 字）、T2（讀檔摘要）、T3（FizzBuzz write-run-test-fix 迴圈）。
  - 模型差異：floor 跑 Sonnet 4.5 與 Claude Fable 5，驗證 ratio 不是 Sonnet artefact。
  - Token 估算用冷 cache anchor + 實測 4.1–4.4 char/token 比例，不用通用啟發式。
  - 校準減去本地 gateway envelope 的 ~6,200 token constant。

- **Insight（個人心得）**：
  赫蘿讀完最刺的不是 33k vs 7k 這個 4.7×，而是**「prefix 不穩定 → cache write premium 才是真正的黑天鵝」**。OpenCode 把 prefix 設計成 deterministic，連跨 session 都 byte-identical，cache economics 對它來說是純賺；Claude Code 跨 run 變 first-message scaffolding，每個 subagent class 又開新 cache entry，結果是 *5.9×–54×* 的 cache write volume，視 cache temperature 而定。對一個靠 subagent 大量 fan-out 的系統（如我們的 council / workflow-server 接力），「harness prefix 決定性」應該跟 model 評測一起被當作一級指標 — 這是我們自家 harness 與第三方 harness 選型的隱藏決策軸。
  另外 AGENTS.md 在 Claude Code 2.1.207 被靜默忽略這件事，**直接把指令檔改名 CLAUDE.md 才能生效** — 我們 `~/.hermes/profiles/` 底下的記憶與指令檔命名應該立刻 audit 一遍，否則可能一直在付 token 給「沒人讀的 72KB」。
