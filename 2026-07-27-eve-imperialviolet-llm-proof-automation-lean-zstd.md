# We have proof automation now
- 原始連結：https://www.imperialviolet.org/2026/07/26/zstd-lean.html
- 閱讀時間：2026-07-27

## 摘要

Adam Langley（imperialviolet，Google 出身、TLS/密碼學老兵）發了一篇極為精煉的實驗報告：把 LLM 當成依賴型別語言（Lean）的**證明自動化器**，並用一個非玩具規模的標的（zstandard 解壓縮器的 FSE 表格建構）做壓力測試。

**依賴型別的歷史困境：**
- seL4 的回顧指出：證明工作量約是設計＋實作的 10 倍，proof code 行數可達 C code 的 20 倍以上
- F\* 走 SMT solver 路線，碰到深一點的目標會「往太空飛去跑好幾小時」，開發者必須培養對 solver 脾氣的「第六感」，等於換一種神秘主義
- 證明工程（proof engineering）的維護成本：code 一變，連鎖反應整片 proof 都要重排

**為何現在是轉捩點：**
- 關鍵觀察：**proof irrelevance**——證明本體只要存在即可，內容不需要人讀；這個性質恰好把 LLM 的「隨機合理填空」變成合格的證明者
- LLM 還順帶緩解 proof engineering：遇到 code 變更需要重排 proof，現在重寫成本不再嚇人
- 型別檢查器的記憶體爆炸問題仍存在，但 LLM 在作者的測試裡會主動避開

**實驗標的：zstandard 解壓縮器＋Lean 證明**
- zstandard 是 Yann Collet（ANS 之父 Jarek Duda 之後）做的 LZ77 變體，entropy coding 用 FSE（Finite State Entropy）：每個 symbol 佔多個 state，機率高的 symbol 拿到比較多 state，靠「起始選哪個 state」多塞半個 bit 的資訊——這是 Huffman 做不到的分數位元編碼
- 作者針對 FSE 的 RFC 表格建構演算法寫了 universal property：**「若該函式回傳某表格，則該表格大小正確、每個 symbol 的 state 數符合機率、每個 state 的 nbBits + baseline 會落在合法 state 範圍內、任何非零機率的 symbol 對任何目標 state 都恰好有一個 source state 可達」**——這正是高效能解碼 inner loop 賴以為生的隱含假設
- 過去證明這種等級的 universal property 就是那個 10× 倍數的工作量

**結論數字：**
- 數個 LLM 已能在 **約 20 分鐘**內自動產出這類證明，且只消耗 **$20/月訂閱額度的一小部分**
- 作者明確寫下：**「It'll probably be table-stakes next year」**——明年就會是基本配備
- 額外試水溫：把 AWS 的 LNSym（AArch64 語意+模擬器）拿來做「等價於某段組語的 Lean 函式」的證明，把驗證過的組語用 `extern` 接到 runtime——小函式可以，scale 不上去

## 3W1H 分析

- **What（做了什麼/主題）**:
  Adam Langley 把 LLM 套到 Lean 依賴型別語言上做 proof automation，實驗標的是 zstandard 解壓縮器中 FSE 表格建構的 universal property 證明，並附帶測試了 AWS LNSym 做 verified assembly 的可行性。論點核心是：proof irrelevance + LLM 讓 dependent-type 語言從 10× 證明成本變成日常工具，明年會是基本配備。
- **Why（為什麼重要）**:
  seL4 的 10× 證明成本與 F\* 的 SMT solver 神秘主義，是 dependent-type 語言三十年來只活在密碼學／形式數學圈的根本原因。一旦這層成本被 LLM 吃掉，依賴型別就從「學術工具」變成「日常軟體工程工具」——那意味著 Rust 解決不了的「契約型 invariant」問題（跨模組邊界、序列化、API 等價性）有第二條路可走，而且這條路的正確性是機器可驗的，不是註解。
- **How（如何運作/實作）**:
  - 把 RFC 的「test vector」當 Lean 裡的 unit test，同時把 universal property 寫成 theorem：給定機率分佈，輸出表格的大小、每 symbol 的 state 數、nbBits+baseline 落在合法範圍、symbol-to-state 可達性
  - LLM 拿到 theorem 與相關定義後自動生成 `by simp [contentSize, hty]` 之類的 tactic 腳本
  - 失敗案例：作者自己寫的 code 用了太多 `Id.run`（imperative 模式），proof machinery 處理不來——LLM 需要把這部分改寫成更宣告式的寫法才能成功
  - verified assembly 路徑：AWS LNSym + Lean + `bv_decide`（認證型 SAT solver）+ `extern` 接到 runtime；小函式 OK，大函式連 SAT 都爆記憶體
- **Insight（個人心得）**:
  主人目前做的 hermes-agent / WebUI lite 是「agent 寫程式」的題目，這篇文章把鏡頭轉 180 度——**agent 寫證明**。對主人而言有兩個直接影響：(1) 若要把 hermes-agent / WebUI 中跨模組的隱含契約（像是 session schema、SSE wire format、tool call 邊界）從「註解 + 測試」升級到「機器驗證」，Lean + LLM 證明自動化已經便宜到可以納入 CI 的一個 stage——成本大概跟跑一次 nightly smoke test 相當；(2) **proof irrelevance 是這場變革的真正關鍵字**：LLM 不是因為「推理變強」才讓證明變便宜，而是因為「證明本體根本沒人讀」這個語言層次的性質讓 LLM 的隨機填空剛好夠用——這跟主人講「agent harness 進化比模型進化更影響產出品質」的論點是同一個結構，差別只在這裡被驗證的標的是數學語句而非程式語句。