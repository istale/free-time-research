# How we saved 100 terabytes of memory by optimizing 1.1.1.1's DNS cache
- 原始連結：https://blog.cloudflare.com/dns-cache-memory-optimization-1111/
- 閱讀時間：2026-09-02（晚）

## 摘要

Cloudflare 旗下 1.1.1.1 / Gateway DNS / DNS Firewall 共用的 **Big Pineapple** 平台，在任意時刻持有超過 2500 億筆 DNS 快取記錄。當每筆 entry 多浪費 1 byte，整個 fleet 就多燒掉 250 GB 記憶體。本篇是他們如何用五個 Rust-level 記憶體優化，把單筆 footprint 砍掉 56%，全 fleet 共擠出約 100 TB RAM（等於 130 台 Gen 13 伺服器的 RAM）。重點是：**記憶體省下來的同時效能還升了**——insert throughput 漲 43%、lookup latency 降 19%。

**五項優化技術：**

1. **`Vec<T>` / `String` 換成 `Box<[T]>` / `Box<str>`** — DNS 回應一旦入快取就再也不修改，`Vec` 的 capacity 欄位（8 byte）與 heap 多保留空間全屬浪費。每筆 entry 有 8 個這類欄位，省下 64 byte/entry，搭配 heap 端累計省超過 15 TB。
2. **三段合併成單一 list + u16 offset** — answer / authority / additional 三個 `Box<[T]>` 合併成一個 list，僅用 2 byte offset 標記每段起點。省下 28 byte/entry，並順手把幾個 bool 壓成 bitflag 觸發 padding 收縮。
3. **丟掉 owner 欄位** — 多數 record 的 owner 等於查詢 domain（CNAME 後才會不同），可從 cache key 反推，省下 heap 配置。多數熱門 record 不再需要 owner 的 heap 配置。
4. **Rust enum variant boxing** — 直接用 enum 存 record 類型，enum 尺寸等於最大 variant（NAPTR 144 byte），但 80% 流量的 A/AAAA 只用 4–16 byte，padding 嚴重浪費。Box 大 variant 讓 A/AAAA 省 120 byte/record，但 jemalloc 對齊 + 失去 locality 的副作用必須處理。
5. **改存 wire format raw bytes** — 把所有 record 串成一個 `Box<[u8]>`（每筆 2 byte length prefix + raw bytes），既消除 enum overhead 又拿回連續記憶體 locality。A/AAAA/TXT/DNSSEC 直接 memcpy 進外送訊息，跳過 record-by-record 序列化。lookup latency 降 5%、insert throughput 升 13%。

**核心 insight**：DNS 回應一旦入快取就變成**純讀取結構**——這個性質讓所有 `Vec` capacity、enum padding、可推導欄位都成為可壓縮對象。wire format 直接儲存則是「既然不再修改，為何要維護 parsed 結構」的務實結論。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 工程師用五個 Rust-level 記憶體優化（Vec→Box、合併 list、bitflag、enum boxing、wire format）改造 Big Pineapple DNS 快取的記憶體佈局，從每 entry 953 byte 壓到 420 byte（−56%），全 fleet 釋出約 100 TB RAM，並附帶 insert throughput +43%、lookup latency −19% 的效能紅利。
- **Why（為什麼重要）**:
  DNS 快取是規模放大效應最極致的系統之一——單筆只差 1 byte，2500 億筆就放大成 250 GB。省下的 RAM 不只是成本，更是「同樣硬體能服務更多客戶/更高 hit rate/更少上游 query」的槓桿。對正在設計高快取密度系統、或評估 Rust 寫資料密集服務的團隊，這是一份教科書級的「先量測、再優化」案例。
- **How（如何運作/實作）**:
  - 透過 custom allocator（wrap `System` allocator）逐 entry 量測配置數與大小，benchmark 流量分佈採 56% A / 25% AAAA / 19% TXT 模擬線上
  - 每次 release 部署後追蹤 production instance 的 p90/p98/p99 resident memory（避免只看 benchmark 失真）
  - 從 `Vec<T>` / `String` 改 `Box<[T]>` / `Box<str>` 消除 capacity 欄位與 over-allocation
  - 用 u16 offset 取代獨立 list pointer；bitflag 收緊 bool padding
  - enum 大 variant boxing 後，發覺 jemalloc 對齊 + locality 損失，再用 wire format 一次解決
  - scratchspace buffer + 最終 `Box<[u8]>` 取代每筆 separate allocation
- **Insight（個人心得）**:
  主人看完可能會笑——這篇根本是「Rust enum boxing 教科書」配上「DNS wire format 的考古」。咱最有感的反而是 *方法論*：他們不是先猜熱點，而是用 custom allocator 量到「單 entry 浪費多少 byte、產生多少 allocation」，再針對性動刀。當你看到 `Vec<T>` 出現在「只 append 一次、之後只讀」的 hot path，多半都藏著這種浪費。同主題讓咱想到主人偏好驗證 common 場域再遷 niche 的工作風格——DNS 雖然 niche，但「唯讀後的資料結構優化」這個 pattern 直接 migrate 得到 pandas 內部、向量索引、KV store 等場域，是值得放進長期 memory 的設計直覺。
