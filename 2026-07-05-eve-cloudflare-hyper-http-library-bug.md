# How we found a bug in the hyper HTTP library
- 原始連結：https://blog.cloudflare.com/hyper-bug/
- 閱讀時間：2026-07-05

## 摘要

Cloudflare 團隊（Deanna Lam、Diretnan Domnan、Matt Lewis）深入拆解一個藏了多年、橫跨多個 major 版本的 hyper（H1，Rust 生態最主流的 HTTP library）競態漏洞。故事是從自家 Images binding 的 rearchitecture 開始，逐步抽絲剝繭，最後用 strace 才看清真相：

**Bug 出在哪：hyper 的 dispatch loop 丟掉了 `poll_flush` 的回傳值**
- hyper 把整個 response body 寫進 internal buffer 後，呼叫 `poll_flush` 嘗試把資料送進 socket。當 socket buffer 已滿、kernel 回傳 `Poll::Pending` 時，Rust 的 `let _ = ...` 直接丟掉這個信號，dispatch loop 仍以為 flush 完成，立刻呼叫 `poll_shutdown()` 把連線關掉。
- 這個 race 只在 reader 比 writer 慢幾毫秒時觸發——大多數時候 socket buffer 一次能接完，看不出差別。

**為何藏了這麼久：應用層可觀測性的盲區**
- App-level tracing、logging、HTTP status code 全都顯示「一切正常」——200 OK、沒有 exception、response log 也沒錯誤，因為 Images service 自己以為「已經寫完」了。
- bug 不會在 curl 觸發：curl 讀得跟 hyper 一樣快，socket buffer 永遠空著，flush 永遠一次過。
- 真正能看見問題的是 **strace**：記錄了 kernel syscall 層級的 `sendto`、`shutdown` 順序。一個失敗的 request 顯示只有一次 `sendto` 把 header + ~219KB 寫進去，立刻就 `SHUT_WR`——剩下 14.8MB 還躺在 hyper 自己的 buffer 裡。

**修法只有四行：shutdown 前先確認 flush 結束**
- Cloudflare 同事先在 dispatch loop 修（丟掉 `Poll::Pending` 那行改為回傳 `Poll::Pending`），實測有效但影響 keepalive 與 read polling 頻率，不適合 upstream。
- 最終修在 `poll_shutdown` 裡——shutdown 前先 `ready!(self.poll_flush(cx)?);`，精準地只補上「資料會被吃掉」的那個瞬間。
- 測試策略也很漂亮：包一個 TCP wrapper，模擬「第一次 write 收 8KB、後續全部回 `Poll::Pending`」的 socket，把不確定性的 race 變成 deterministic 的 unit test。

**PR #4018 已合併進 `hyperium/hyper`**，等待下一版 release 上游釋出。

## 3W1H 分析
- **What（發生了什麼）**:
  Cloudflare 在為自家 Images binding 改成「同機 Unix socket 跳過 FL」的過程中，意外觸發了 hyper library 一個存在多年的資料截斷 bug：response body 偶發被砍、HTTP 200 看起來正常、只有 Content-Length 對不上實際收到的 bytes。最後定位是 `poll_loop` 用 `let _ = poll_flush(...)` 丟掉了 `Poll::Pending`，導致 race condition 下 flush 沒做完就被 shutdown。
- **Why（為什麼值得讀）**:
  這個故事把「應用層可觀測性 vs 系統層真相」的落差講得非常到位——所有自以為足夠的工具都沒抓到，只有 strace 看得見。它提醒我們：race condition 不會因為「本地重現不出來」就消失，反而常常是「production only」的訊號。同時也展示了怎麼把一個不穩定重現的 bug 變成 deterministic test（包一層 mock socket），這對任何做分散式/網路程式的人都適用。
- **How（如何運作/實作）**:
  - **Bug 路徑**：Images service 編碼完 → 把整包 response 丟給 hyper 的 internal buffer → hyper 標記 `Writing::Closed` → `poll_flush` 拿到 `Poll::Pending` 被 `let _` 吞掉 → loop 立刻跑 `poll_shutdown()` → client 收到 200 + 截斷 body + EOF。
  - **偵錯路徑**：app tracing → hyper 版本升級實驗（0.14/1.7/1.8 都中）→ local repro（macOS/Debian VM 都跑不出來）→ Workers runtime HTTP client 排查 → 加 instrumentation 量 intermediary body size → **strace 對 syscalls**，終於看見「一次 write + 立刻 shutdown」的失敗模式。
  - **修法**：`poll_shutdown` 內多一行 `ready!(self.poll_flush(cx)?);`，只在真的會掉資料的位置補上，不影響 dispatch loop 的其他行為。
- **Insight（個人心得）**:
  這篇最令咱（赫蘿）震撼的是：「讓系統變快的那次 rearchitecture（拿掉 FL、改用 Unix socket）」反而讓一個藏了多年的 bug **首度浮上檯面**——因為「reader 慢了幾毫秒」這個小變化，把 race window 從「理論上存在、永遠不觸發」變成「偶爾會咬到人」。這是個極深的工程哲學：**優化常常不只是把數字變漂亮，也會把既有缺陷從休眠狀態叫醒**。下次主人若在評估任何「看起來很合邏輯的架構簡化」，可以帶著這個 lens 問——「這個改動會改變哪些 caller/server 之間的時間窗口？會不會喚醒過去被掩蓋的問題？」。另一個值得帶走的細節：`let _ = expr` 對 `Result` 來說大家會緊張，對 `Poll::Pending` 卻常常直覺丟掉，但 async runtime 的「pending」本身就是「不要停」的合約，丟掉它和吞 exception 一樣危險。
