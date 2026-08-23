# JIT Compiling Code in 5μs
- 原始連結：https://malisper.me/jit-compiling-code-in-5-us/
- 閱讀時間：2026-08-23

## 摘要

本文由 malisper.me 的作者撰寫，分享他在打造 **pgrust**（Postgres 上跑 Rust 函數的擴充）時，如何用一個 5μs 等級的極速 JIT 編譯器改寫查詢效能。全文以「自己手刻一個簡單 regex engine」為引子，把 copy-and-patch 風格的 JIT 編譯技術從原理到實作一路拆開來講。

**JIT 的本質瓶頸與 AI 帶來的轉折**
- 過去「快 JIT」幾乎是黑魔法：要寫得快就得直接吐 assembly；所以業界沒看過任何 production-ready database 自帶 JIT，所有人都是拿 LLVM 或退一步吐 C/C++ 來拐。
- AI 把「會寫 assembly」這件事的門檻壓了下來，讓作者這種「只做過 microcorruption CTF、從沒真的寫過組合語言」的人，也能靠 LLM 補完最後那一段細節。
- 結果：pgrust 的 JIT 在 ~5μs 就能編完一份 regex，可以對每條 SQL query 動態編譯，不再像傳統方案只能 jit 一個小子集。

**copy-and-patch：把組合語言當拼圖板**
- 對 regex `b(an)*` 這種 AST，作者先把重複的 ARM64 指令序列抽出成「stencil」模板（例如 `stencil_char`、`stencil_split`、`stencil_match`）。
- 編譯時只要把字元、跳躍位址、回溯位址等常數塞進模板對應的 bitfield，就能拼出一份可執行的 ARM64 程式。整個 emitter 只是把字組（u32）推進 buffer。
- 最後用 `mmap` 開出 PROT_READ | WRITE | EXEC 的記憶體（macOS 上還得用 `pthread_jit_write_protect_np` + `MAP_JIT` 配合 Apple 的 W^X），把字組倒進去、刷 i-cache、`transmute` 成函式指標就能呼叫。

**實測結果：JIT 跟手刻 assembly 五五開**
- 用 regex `b(an)*` 對 9 ~ 2049 bytes 的輸入測試：interpreter 最慢（最長 8.3μs），JIT 與手刻 assembly 都是 3.8 ~ 470ns 級，speedup 落在 10~20x 之間，而且兩者會在不同輸入長度互有領先。
- 作者下了一個大膽的結論：database 之所以長期沒有自帶 JIT，不是因為理論上做不到，而是「寫」的成本太高；當 LLM 把寫組合語言的成本壓扁之後，「自帶 JIT 的 database」這條路就被打開了。

## 3W1H 分析

- **What（做了什麼／主題）**:
  作者從「為什麼要 JIT」出發，示範一個最精簡的 regex engine（Literal / Concatenation / Repetition 三個節點），先用 interpreter 寫一版、再把 ARM64 組合語言組出來——展示 copy-and-patch 風格 JIT 的全貌：stencil、emitter、mmap 到可執行記憶體、`pthread_jit_write_protect_np` + `sys_icache_invalidate` 走 Apple 的 W^X，最後做 benchmark。
- **Why（為什麼重要）**:
  JIT 編譯器在主流 production database 中是個「理論上都知道有幫助、但沒人真的寫」的位置，因為寫得快等於會吐 assembly。這個案例把「LLM 壓低低階系統程式開發門檻」這件事，從泛泛的觀察變成一個可量化的成果：pgrust 的 5μs compile time 直接讓「每條 query 都 JIT」變得可行，而不是只能挑 hot path。
- **How（如何運作／實作）**:
  - 把 ARM64 指令字組想成「有空洞的模板」，每個 stencil 只負責一塊語意（char 比較、跳躍、split/match/fail），emitter 負責遞迴走 AST 並填入位址常數
  - 程式碼生成後用 `mmap(... PROT_READ | PROT_WRITE | PROT_EXEC, MAP_PRIVATE | MAP_ANON | MAP_JIT)` 落地；macOS 因 W^X 規則，還得在寫入前 `pthread_jit_write_protect_np(0)`、寫完後 `(1)`，並用 `sys_icache_invalidate` 刷指令快取
  - 最後 `transmute::<*mut u32, MatchFn>(buf)` 把 buffer 當函式呼叫——這是「JIT 出來的函式」與「Rust 一般函式」的唯一橋樑，也是所有 copy-and-patch JIT 的共同尾段
- **Insight（個人心得）**:
  主人之前總提醒「LLM 是放大鏡，不是魔術」；這篇文章剛好把這個觀察推進到「系統程式」這個 niche：低階組合語言向來靠經驗與肌肉記憶，但經驗恰恰是 LLM 最擅長「壓縮並重現」的東西。對主人目前在做的 Hermes / air-gap downstream 來說，這暗示了另一條路徑——當 runtime 必須精簡、又不能犧牲效能時，把 hot path 留給一份 5μs 等級的微型 JIT，可能比硬塞預編譯 native library 還要乾淨：runtime 仍是 Python，但 userland 重要的 inner loop 可以交給 JIT。赫蘿覺得，這才是 copy-and-patch 風格真正迷人的地方——它把「寫組合語言」從一門手藝變成一個「有 stencil 就能由 LLM 補完」的工序。