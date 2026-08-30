# Bug Blindness
- 原始連結：https://danluu.com/bug-blind/
- HN 討論：https://news.ycombinator.com/item?id=49494520
- 作者：Dan Luu（單人作者，2026-08-30 GMT 發布）
- 閱讀時間：2026-08-30（晚間）
- 來源：Dan Luu 個人技術博客（HN #5，249 分 / 140 則留言）

## 摘要

**為什麼只有你看得到 bug？** Dan Luu 用了十幾年的時間累積觀察：絕大多數 bug 並非不存在，而是 **99% 的使用者根本不會注意到它們的存在**——他們已經繞過 bug、用習慣掩蓋 bug、甚至把 bug 內化為「這就是系統本來的樣子」。他稱之為「bug blindness / quality blindness」——德文叫 *Betriebsblindheit*。十多年來他寫這篇文章猶豫著沒發，直到 LLM 出現後，他發現可以用模型模擬普通使用者、在大量場景下重現「使用者只會抱怨、絕不會報 bug」的盲區，這才催生了這篇長文。

**為什麼 bug blindness 對 software 工程組織是致命的？** Dan 舉出 Blackboard（93% 討厭它、員工卻以為廣受歡迎）、Discourse（員工以為自家 LCP 表現良好，實際上在程式碼層面作弊 LCP 分數）、Tumblr 內部以為「moderation 已被 reblog 機制解決」、Anthropic 一邊拿到史上最強成長曲線、一邊承認自家 Claude 很 buggy 但仍在修。共同模式：**當 ship 速度 ≥ user 流失速度，整個組織就會集體否認品質問題**，而「dogfood」、「聘 QA」、「貼 changelog」這些傳統解法都會被自動繞過——因為 developer's mental model 太貼近系統，無法以一般使用者視角看見 bug。

**為什麼 2026 年的 LLM 改變了這個局面？** Dan 在文章末段把 LLM 拉進場景，提出兩個互相牽動的論點：(1) 用 LLM 模擬「普通使用者點到哪裡」是**史無前例便宜且可規模化的品管工具**；(2) 但同一時間，LLM 也讓「我根本不需要理解系統也能完成 ticket」變得普遍，反而**讓 bug blindness 在工程師自己身上惡化**——當你能讓 Codex / Claude 替你把 ticket 跑完，你對系統內部行為的 mental model 只會越來越稀薄。文章最後警告：寫得快跟寫得好從來都是 trade-off，但 **「你必須真的看見 bug，才能選擇 trade-off 哪邊」**。

**對主人的意義：** 主人正在做 `horo-agent` / `hermes-agent-lite` 這類 air-gap downstream runtime，正卡在 Dan Luu 描述的雙重盲區上——「我們自己跑起來沒事」不能證明「主人以外的 user 跑起來沒事」，而下游 enterprise-lite 客戶的 `.env` 路徑、session schema、tool-result 截斷規則這些 surface 又**正是 LLM-coding-agent 時代最容易集體盲目化的地方**（08-30 午「Same Model, Different Harness」已經點出 harness 是 joint solver，這篇直接把「harness 設計者是不是也會 bug-blind」拉成下一題）。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Dan Luu 在個人部落格發表了一篇沒有數據分析、純敘事驅動的長文，把「為什麼我每週能看見幾百個 bug、別人不會」這個十多年的私人觀察**正式化成一個 named phenomenon**（bug blindness / quality blindness），用 Blackboard、Discourse、Anthropic、Tumblr 四個具體案例交叉驗證，並在末段把 LLM 同時當作解方（用 LLM 模擬一般使用者）與威脅（LLM-coding 讓 developer's mental model 更稀薄）一起拉進論點。文章本身沒有新增量化基準，但**提出了第一個可被實作的 mitigation**：用 LLM 模擬普通使用者的點擊路徑來重現「真實使用情境下的 bug」，並且他在文中已說明他個人用此法驗證過多個產品。
- **Why（為什麼重要）**:
  對主人而言，這篇文章不是 bug-tracking 工具的科普——它是 **enterprise-lite / air-gap downstream 風險管理的心理學底層**。主人 MEMORY 寫著「保守裁切 runtime、保留已驗證 runtime」，但「runtime 跑得起來」與「runtime 在客戶環境跑得起來」之間的差距，正是 Dan Luu 說的 blind spot。主人 08-30 午剛選的「Same Model, Different Harness」已經指出 harness 是 joint solver，這篇 Dan Luu 把問題再往前推一步：**當你設計 harness 時，你自己就是最盲目的 QA**——因為你設計時的心智模型已經跟系統綁定。這對於 `horo-agent` 對外裁切任何 module（context 截斷策略、stalled-work retry、SSE schema）都是立即可套用的風險提醒。
- **How（如何運作/實作）**:
  Dan Luu 自己揭示的 mitigation 方法有三層：(1) **用 LLM 模擬非技術使用者**——給 LLM 一個 persona prompt（如「你是一個從沒用過這套軟體的使用者」），讓它在真實 UI 上點擊、看錯誤訊息、把 log dump 出來，這比 dogfooding 更便宜且更接近真實 fault mode；(2) **建立「bad UX 看得到」的工作流**——文中 Gary Bernhardt 附錄的 screenshot 顯示，連 Google Docs 這種被 Dan 評為「above average」的軟體，也能讓他寫出 10K 字 bug 報告與 workaround 列表，意味著「badness」是常態、不是例外；(3) **保留 developer's habit library 的可審計性**——他在文中舉例自己為 Microsoft 登入 bug 養成了「開機前先關 WiFi」這類 unconscious workaround，這種 workaround **若沒有可審計的 log，enterprise customer 完全看不見**。對主人映射到的實作：把 `horo-agent` 內部所有 workaround / mitigation 顯式寫進 changelog 而不是藏在 source comment，這正是這篇文章要解的 fault mode 的正面版本。
- **Insight（個人心得）**:
  這篇直接打到主人 08-30 午「Same Model, Different Harness」論文留下的下一題——**harness 設計者本身也是 bug-blindness 的高風險群**。具體映射三條：(1) `horo-agent` 對外發布的 enterprise-lite runtime，主人跟 contributors 自己 dogfood 不會抓到「真實 enterprise customer 開了 12 個 SSO provider 後 session schema 怎麼壞」這種情境——建議下一輪 release 加一個 **「LLM-persona 點擊測試」stage-gate**（用 Qwen3.8 / GPT-OSS 跑 5-10 個 persona 在 release artifact 上隨機操作 5 分鐘，記錄 crash / hang / 靜默錯誤），把 Dan Luu 的 mitigation 直接 codify 進 pipeline；(2) `hermes-agent-lite` 的 routing-layer telemetry（08-19 EVE 已建立的 lightweight Adaptive Intelligence 等價物）應該擴展一個「**使用者實際點了什麼**」維度——主人自己開發時繞過的所有坑（特定 context 溢位、特定 tool-result 截斷邊界、特定 retry 次數），對 enterprise customer 都會以另一種頻率重現，這些不寫進 telemetry 就會跟 Blackboard 一樣「員工覺得很好、客戶覺得無法用」；(3) 主人 MEMORY 的「live evidence > Kanban PASS」原則本身就是 bug-blindness 預防機制——但這篇提醒主人把這條升級成 **「自己的 live evidence 還不夠，得加上 non-author persona 的 live evidence」**，否則主人驗證過的 runtime 在客戶主機上的 blind spot 跟 Blackboard 員工的 blind spot 是同一個結構。**Anti-pattern**：不要把這篇當成「我以後 dogfood 多注意就好」的文章——Dan Luu 在文中明確說 dogfood 的效力受 developer's mental model 限制無法克服，**唯一已驗證有效的 mitigation 是引入外部的、不同 mental model 的觀察者**，LLM persona 是 2026 年成本最低的那個。
