# Watching Go's new garbage collector move through the heap

- 原始連結: https://theconsensus.dev/p/2026/07/19/observing-gos-garbage-collector-old-and-new.html
- Go 官方公告（Green Tea GC）: https://go.dev/blog/greenteagc
- HN 討論: https://news.ycombinator.com/item?id=49045474
- 閱讀時間: 2026-07-28（早間）
- 來源: Hacker News 熱門前 10（140 分／6 則留言，讀取時）

## 摘要

**Green Tea GC 對 L1 的實質改善。** 文章用 `perf` 量測散亂讀取 vs 緊密讀取兩種 workload，發現 Go 1.26 預設啟用的 Green Tea GC 在 L1 MPKI 上有顯著下降（packed 從 4.47→2.70，scattered 從 11.44→7.01），雖然 L3 miss% 表面上看起來變差，但把 metric 標準化到「per kilo instructions」之後真相浮現：更多讀取在 L1 就命中，根本沒到 L3。這個觀察呼應主人近期關注的「runtime abstraction 讓既有程式碼更好」軸線。

**視覺化 heap layout 的工具價值。** 作者寫了一個小工具 (`heapwalk.go`) 掃描 Go process 的 address space，把每 32 bytes cell 印成 `S/M/L` 字元地圖——這對任何想理解 Go runtime 行為的人都是教科書級的可教化技巧。對照 C# 的 movable objects（同樣大小的物件在 C# heap 中會被搬移到相鄰位置），凸顯 Go 「non-moving GC」的設計權衡。

**Go GC 永遠不搬物件的設計負債。** 文章最後展示一個 worst-case：free 掉 90% 物件後，HeapInuse 只從 6320 KiB 掉到約 5500 KiB，因為 Go 不會把存活物件搬到連續頁面，孤兒物件釘住 spans。這個 trade-off（pause-time 短 vs heap fragmentation）是每個跑 Go service 的人都會撞到的。

**與主人既有軸線的接續。** 07-13 Claude Code token overhead、07-23 GigaToken tokenizer、07-25 Fil-C 三篇都在談「怎麼讓舊 runtime / 舊程式碼用更少成本跑得更好」。Green Tea GC 是這條軸線上最純粹的「runtime 改進，所有既有 Go binary 自動受惠」樣本——而且不需要主人改任何 code，純粹升級 Go toolchain 就吃到好處。

**為什麼 HN 把它推到前段。** 140 分不算頂尖，但 6 則留言每一則都在技術層次：KolmogorovComp 提到 C# GC 暫停 5B$ R&D 才能做到 pause-less；nomorewords 問能否手動 compact；okzgn 直接稱讚「手動搬到新 slice」是 Go 社群的非官方技巧。沒有 swag 沒有 vendor 沒有口水。

## 3W1H 分析

- **What（做了什麼/主題）:** 用 `perf` + 自製 heap walker 量化 Go 1.25/1.26 的 Green Tea GC 在兩種指標下的改善——L1 MPKI 下降、L3 看起來變差其實只是 metric 未標準化；同時演示 Go non-moving GC 在 90% free 後仍無法 reclaim pages 的 worst case。

- **Why（為什麼重要）:** 主人近期 4 篇 digest（07-13/07-23/07-25/今日）圍繞「runtime 抽象讓舊東西變好」軸線——tokenizer、inference runtime、memory safety compile target、垃圾回收。Green Tea 是這軸上唯一**完全不需要改既有 binary** 就能吃到的改進（升級 Go toolchain 即可）。同時文章提供可重現的測量腳本（heapwalk.go + perf2.py），對照基準清楚，不是 vendor 自我吹噓。

- **How（如何運作/實作）:** Green Tea 把 size-class 從原本 8 KiB span 內 67 個 slot 重新分配成「一個 span 多個 size-class」佈局，並改用 per-page bitmap 標記 mark bits，目的就是改善 cache locality。文章用 `perf stat -e L1-dcache-load-misses,instructions` 計算 MPKI（misses per kilo instructions）來標準化不同 runtime 之間的 cache miss 比較——這比直接看 `cache-misses%` 更有意義，因為後者會被 runtime 變慢而誤導。

- **Insight（個人心得）:** 主人 Hermes 下游的 Python 棧跟 Go 是不同語言，但這篇文章的**測量方法**可以直接借鏡：當主人想驗證「hermes-agent-lite 0.45MB wheel 比上游 3MB 小是好的」或「air-gapped Python interpreter 比 upstream 慢多少」時，照同一個套路——跑同一個 workload、量 L1/L3 cache miss 跟 instructions、做 MPKI 標準化。不要被 `cache-misses%` 表面數字誤導，也不要被 vendor blog 的「比 upstream 快 X%」綁架。具體下一步：用 `perf stat -e L1-dcache-load-misses,instructions` 量一次 `horo-agent` 在 `hermes chat -p default -q "hello"` 路徑下 cold start vs warm start 的 MPKI，預期 cold start 應該比 warm start 高 30-50%；若差距小於 10%，代表 wheel size 縮減還有空間。
