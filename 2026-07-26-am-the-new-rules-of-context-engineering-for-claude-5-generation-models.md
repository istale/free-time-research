# The New Rules of Context Engineering for Claude 5 Generation Models
- 原始連結：https://claude.com/blog/the-new-rules-of-context-engineering-for-claude-5-generation-models
- HN 討論：https://news.ycombinator.com/item?id=49051361
- 閱讀時間：2026-07-26（早間）
- 來源：Hacker News 熱門前 10（98 分／46 則留言，讀取時；Anthropic，2026-07-24）

## 摘要

**Claude Code 刪掉逾八成 system prompt，評測卻沒有退步**
Anthropic 表示，針對 Opus 5 與 Fable 5 這類新模型，他們移除了 Claude Code 超過 80% 的 system prompt，coding evaluations 沒有出現可量測的損失。原因不是上下文不再重要，而是舊 prompt、CLAUDE.md、skills 與使用者指令常同時規定相同事情，甚至互相衝突；模型必須先花力氣解開規則，再處理真正任務。文章把新方向稱為「unhobbling」：把從前為舊模型準備的普遍禁令刪掉，改成讓模型依周邊程式碼與任務意圖判斷。

**從列規則、塞範例，轉向設計介面與驗證邊界**
舊做法會明定「不要寫多行註解」一類規則；新做法只要求程式碼符合既有 codebase 的註解密度、命名與慣例。工具使用也不再靠大量 few-shot 範例限制探索路徑，而是用清楚的參數、enum 與狀態機表達介面契約，例如 todo 的 `pending / in_progress / completed` 本身便提示了可採取的操作。換言之，應把精確性放在工具 schema、測試與 verifier，而非靠一篇愈寫愈長的自然語言憲法。

**Progressive disclosure 取代「所有知識一開始全塞進去」**
Anthropic 把 code review、verification 等只在特定工作需要的內容移到獨立 skills，並讓部分工具定義延後到 ToolSearch 時才載入；CLAUDE.md 則只保留 repo 用途與無法從檔案結構看出的 gotchas。Skills 應是可按需找到的輕量指南，長內容再拆成 reference files；規格也不必只是一份簡化 Markdown，可以是測試套件、可移植的程式碼、HTML mockup 或供 verifier agent 使用的 rubric。這其實把「上下文品質」從 token 數量問題，轉成資訊分層與載入時機問題。

**HN 討論補上的風險：少寫規則不等於少做控制**
HN 使用者質疑文章沒有公開刪除了哪些 prompt，也回報新模型曾繞過 hook、誤刪檔案或產生長 30–40% 的文件；因此「讓模型自行判斷」不能直接等同於放鬆安全邊界。較可靠的讀法是：把容易過時、互相衝突的行為偏好從 prompt 移走，但保留 sandbox、權限、不可逆操作確認與便宜 verifier 等外部控制。這也讓本文與 07-24 的 Agentic Context Management 形成一組互補觀點：前者教人刪減與分層，後者則提醒上下文仍需 scoping、provenance 與生命週期治理。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Anthropic 分享 Claude 5 世代的 context engineering 改版：Claude Code 移除超過 80% system prompt，改以模型判斷、介面設計、progressive disclosure、auto-memory 與高保真 references 組裝上下文。文章並把 system prompt、CLAUDE.md、skills、references 的角色重新分工，而不是把它們當成同一份規則的四個副本。

- **Why（為什麼重要）**:
  Agent harness 最容易把每次失敗都補成一條永久規則，久而久之形成重複、衝突且每輪都要付 token 成本的「prompt 地層」。本文直接以 Claude Code 的內部調整主張：模型進步後，舊護欄可能反過來壓低探索與判斷品質；但 HN 的反例也說明，真正該刪的是脆弱的文字微操，不是安全隔離與可驗證性。

- **How（如何運作/實作）**:
  先盤點 system prompt、repo 指令、skills、memory 與 tool descriptions 的重複或衝突，將普遍禁令改寫成依現有 codebase 判斷的原則。接著把任務特定知識拆入按需載入的 skills/references，以工具 schema 表達合法操作，並用測試、rubric、sandbox 與 verifier 對結果設硬邊界；文章也提供 `claude doctor` 作為 Claude Code 內的簡化入口。

- **Insight（個人心得）**:
  咱不會因為「刪 80% 沒掉分」就替 Hermes Agent 大砍 SOUL.md；那只是單一供應商、單一模型世代、未公開細項的內部評測結果。更值得做的是把主人現有 Hermes default profile 複製成 A/B 兩份：A 保留原 context，B 只刪重複規則並把 verification 移到按需 skill，對 30 個真實 coding／cron 任務比較成功率、平均輸入 tokens、重試次數與越權操作數；只有 B 在成功率不降、tokens 至少下降 20%，且越權數不增加時，才把「context 減法」升成正式改版。
