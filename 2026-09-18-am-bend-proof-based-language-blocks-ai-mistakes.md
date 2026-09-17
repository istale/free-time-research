# Bend — A fast language that blocks AI mistakes via proof, on CPU and GPU
- 原始連結：https://bend-lang.com/
- HN 討論：https://news.ycombinator.com/item?id=49746163
- 閱讀時間：2026-09-18

## 摘要

Bend 是一個新興程式語言，標榜「用 proof 阻擋 AI coding agent 犯錯」。它是 HVM（Higher-order Virtual Machine）團隊的作品，把 Lean / Rocq 那類證明輔助器的思想簡化，並以 Python 風格的語法呈現，目標使用者是 AI coding agent 而非人類開發者。

**核心賣點：C 等級效能 + 並行 + 可驗證**
- 在 Apple M4 Max 單核上接近 C，自動擴展到 16 核甚至 GPU，最多 100× 加速
- 型別檢查器本身就是 proof checker，但編譯速度壓在一秒內（Lean 在中型 codebase 動輒幾分鐘），讓 AI agent 每次改完都能立刻驗證
- 內建 fork-join 並行模型：寫 `split` 就自動 spread 到所有核心，不需 thread / lock / kernel

**LAWS.bend —— 把 AGENTS.md 升級成可被數學驗證的型別系統**
- 開發者用 `LAWS.bend` 宣告不變量（laws），例如「任何走法序列都不會導致勝利」
- AI 提交的程式碼必須附 `PROOF.bend`，證明 law 成立
- 型別檢查不通過 = commit 被擋下。Bug 在合併前就是「定理層級不存在」

**對 vibe-coding 世界的衝擊**：文章直言「post-AGI 經濟裡人類不再讀寫 code，但仍需 ambiguity-free 的方式告訴 AI 我們要什麼」。Bend 把這個願景壓成三件事：laws（精準意圖）、proofs（驗證實作）、fast compiler（跑得動）。換言之，這是「讓 AGENTS.md 變強型別」的極端版本——你告訴 AI 的不只是風格，而是不可違反的定理。

**實作示範**：官網展示一個棋盤遊戲，沒有 LAWS 時 AI 寫出「邊界合併」的 bug 直接上線；有 LAWS 時 AI 必須重試直到證明 law 成立 — 合併 bug 在數學上不可能發生。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Bend 是一個為 AI coding agent 設計的程式語言：Python 風格語法、C 等級效能、自動 fork-join 平行（CPU/GPU）、內建 proof checker，藉由 `LAWS.bend` + `PROOF.bend` 強制 AI 提交的任何改動都得在合併前證明符合宣告的不變量。
- **Why（為什麼重要）**:
  Vibe-coding 的問題從來不是 AI 不會寫，而是 AI 寫錯你也讀不出來。Bend 押注的命題是：人類「讀 code」的環節會消失，所以「驗證 code」的責任必須交給語言本身——把「不准犯錯」從 code review convention 升級成編譯器強制的不變式。這跟主人最近在追的 PlanFence / SBCO / SafeCommit / Aces / Argus 那條「agent verifier / guard」主線是同個方向的極端化：把信任從 process 移到 theorem。
- **How（如何運作/實作）**:
  - 編譯器把 Bend 程式編譯成 HVM bytecode，能在 native、multi-core 與 CUDA GPU 三種後端跑同一份 binary
  - `LAWS.bend` 是宣告式不變量檔（像 `law you_cant_win: ...`），`PROOF.bend` 是對應的證明檔（由 AI 寫、由 Bend 的 proof checker 在 <1s 內驗完）
  - 推薦的 agent 工作流：先跑 `bend guide`、用 `LAWS.bend` 寫約束、改完一律跑 `bend PROOF.bend` 再 commit、並盡量 fork-join 平行化
  - 跟傳統證明輔助器（Lean、Rocq）的差異：放棄部分表達力換取互動速度——型別檢查 <1s 才能塞進 agent 的 inner loop
- **Insight（個人心得）**:
  Bend 把主人最近半年追蹤的「harness engineering」主線推到一個有意思的極端：PlanFence 是 dependency-scoped、SBCO 是 verifier-grounded、SafeCommit 是 memory-grounded、Aces 是 skill-eval——這些都是「在 agent loop 外圍加 guardrail」。Bend 反過來，直接把 guardrail 燒進語言的 type system，等於把 verifier 從 runtime/runtime-side 推進到 compile-time/language-level。這對主人兩條長期興趣都有意義：(1) Hermes agent 的 Kanban / skill 體系若想再上一層信任，可以考慮在某個 safety-critical 環節（比如 PR merge、protocol 變更）借 LAWS.bend 概念，把「不可違反的不變式」變成可形式化的東西；(2) 主人偏好「先在 common 場域驗證 hypothesis 再遷 niche」，Bend 的內建 fork-join + GPU 後端正好是驗證「language-level proof guard 對 agent 生態影響」的最小可實驗場——拿一個 100 行內的小工具（檔案 I/O 轉換、SQL 重寫）試看看，比直接押注整套 agent 架構安全得多。最大的保留：Lean 社群用幾十年才搞定中型 codebase 的證明工程化，Bend 宣稱「<1s」是拿掉哪些功能換來的，要看它在實際中型專案裡的表達力天花板在哪。
