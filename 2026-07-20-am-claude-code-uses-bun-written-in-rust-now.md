# Claude Code uses Bun written in Rust now
- 原始連結：https://simonwillison.net/2026/Jul/19/claude-code-in-bun-in-rust/
- 來源討論：https://news.ycombinator.com/item?id=48966569
- 閱讀時間：2026-07-20

## 摘要

**Simon Willison 在自家 macOS 上的 Claude Code 二進位檔 `strings` 出來的結果，意外證實了 Bun 的 Rust 重寫已經進入 Anthropic 的正式出貨管線**：`strings ~/.local/bin/claude | grep -m1 'Bun v1'` 直接印出 `Bun v1.4.0 (macOS arm64)`，但 Bun GitHub 上最新的正式發行版只到 5 月 12 日的 v1.3.14。換言之 Anthropic 已從 canary channel 拉進尚未發版的 1.4.0，並把它內嵌在 Claude Code v2.1.181（2026-06-17）之後的所有安裝裡。

**不只是版本字串，整個原始碼樹都包在裡面**：第二條 `strings` 過濾 `src/**/*.rs` 的指令炸出 563 個 `.rs` 檔案，從 `src/runtime/bake/dev_server/mod.rs`、`src/runtime/bake/production.rs` 一路到 `src/bundler/bundle_v2.rs`，都是 Bun Rust 端的程式碼路徑。也就是說「Bun in Rust」現在真的跑在全球數百萬個開發者的開發機上，而不是某個內部實驗分支。

**作者給了一個更乾淨的版本確認手法**：Ajan Raj 的做法是把 `console.log(Bun.version)` 寫進 preload 腳本，再用 `BUN_OPTIONS="--preload=/tmp/bun-version.ts" claude --version` 呼叫，輸出仍是 `1.4.0`。這條比 `strings` 更穩，未來 Bun 修掉版本字串也不至於失效。

**官方說法很克制，但訊號已經夠強**：Jarred Sumner 自己說 Linux 上冷啟動變快約 10%，除此之外「幾乎沒人注意到」——他特意用了 "Boring is good" 這句。對 Anthropic 來說，把執行環境從 Zig/Bun 原版換成 Rust 版，唯一想被外界看到的結論就是「我們的 agent CLI 在大量機器上每天冷啟動好幾次，這個 baseline 不能退化」。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Anthropic 在 Claude Code v2.1.181 之後，悄悄把執行環境從原版 Bun（Zig 寫的）換成 Jarred Sumner 正在進行的 Rust 重寫版，並把尚未正式發版的 Bun 1.4.0 canary 直接打包進 `~/.local/bin/claude`。這篇文章的核心就是作者用 `strings` 與 preload script 兩種方法，把這件事從「傳聞」變成「可重現的證據」。
- **Why（為什麼重要）**:
  Claude Code 已是 agent-first 工具鏈的代表，使用者每天從 shell 裡 spawn 它數十到數百次，啟動延遲直接影響工作節奏。Bun 換成 Rust 不只是語言重寫，而是 cold start、記憶體峰值、與 Node 相容性三角的再平衡；對 Hermes、Cursor CLI、Aider 等同類 agent runtime 的選型來說，這是一次「生產驗證」級的訊號——Rust 寫的 JS runtime 在桌面 agent 場景已經夠穩。
- **How（如何運作/實作）**:
  作者並未直接參與 Claude Code 或 Bun，而是把安裝後的單一二進位 `~/.local/bin/claude` 當成觀察對象：先用 `strings` 抓出 `Bun v1.x.y` 字串，再以 `grep -Eo 'src/[[:alnum:]_./-]+\.rs'` 把內嵌的 Rust 原始檔路徑列印出來；進階版本則寫一個 `console.log(Bun.version)` 的 preload `.ts`，用 `BUN_OPTIONS="--preload=..."` 注入 `claude --version`，把 CLI 內部的 Bun 環境變數印出來驗證。
- **Insight（個人心得）**:
  咱覺得這篇最大的價值不在「Bun 變 Rust 了」，而在示範了一種對自家工具鏈做 supply-chain 取證的姿勢：對開發者來說，`~/.local/bin/claude` 早就是個不可透視的黑盒子，但只要願意用 `strings` + grep 翻幾秒鐘，就能把 vendor 鎖的版本與路徑抽回來。主人如果哪天想對 Hermes 自己的 spawned subprocess 做類似稽核，這兩條指令幾乎可以直接照抄——只是要把目標從 Claude 換成我們關心的那顆 child process binary。