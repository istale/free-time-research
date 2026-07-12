# Why we cannot wait for better post-quantum signature algorithms
- 原始連結：https://blog.cloudflare.com/ml-dsa-will-have-to-do/
- 閱讀時間：2026-07-12（晚）
- 作者：Bas Westerbaan（Cloudflare, Post-Quantum Security Research）

## 摘要

Cloudflare 資深密碼學家 Bas Westerbaan 給出一份誠實到殘忍的 post-quantum signatures 現狀盤點：NIST 第三輪的九個候選（加上即將定稿的 FN-DSA）沒有一個能取代 Ed25519 當「all-star」；ML-DSA 雖平庸、笨重，卻是唯一已經能用且經過時間驗證的選項，必須在 2029 全面 post-quantum 化前先部署下去。

**沒有 post-quantum 的 Ed25519**：作者用一張表把所有候選攤開，Ed25519 仍以 32-byte 公鑰、64-byte 簽章、0.15 ms 簽名碾壓群雄。所有 PQ 簽名方案要嘛簽章大、要嘛公鑰大、要嘛慢、要嘛側通道難做、要嘛有狀態。沒有任何一項是全面性的贏家。

**「Specialist vs Generalist」分類**：作者把候選分兩類。「專家型」如 SQIsign（簽章 148 bytes，幾乎完美，但簽名慢 300×、最難寫對）、UOV（簽章 96 bytes 但公鑰 66 KB）、SLH-DSA（安全最保守但 8–17 KB 簽章）。「通才型」如 ML-DSA——沒一項特別強，但各方面都還堪用，唯一的選擇。

**FN-DSA 真正的「sharp edges」**：Falcon 改成 FN-DSA 即將定稿（FIPS 206），理論數字漂亮——簽章 666 bytes、驗證 0.7× ML-DSA——但需要**硬體加速浮點運算**才能達到。這在密碼學標準史上頭一遭，副作用可怕：浮點加法結合律不成立，導致連測試向量都難寫死；編譯器是否插入 FMA、Parseval 定理的捷徑實作，都會讓同一把私鑰的兩次「deterministic 簽章」洩漏私鑰部分位元。

**真實時程，不是寫在論文上的「ready」**：以 ML-DSA 為基準——從 NIST 選中到 OpenSSL 支援、IANA codepoint、CMVP 憑證、RFC、TLS 整合、瀏覽器根憑證——走了整整 10 年。照這個節奏：FN-DSA 廣泛可用要 2033、多變量 2034、SQIsign 2035。**而量子威脅在 2029 就到**。這就是「你拿著手上的武器上戰場，不是你希望有的武器」（Eric Rescorla 語）的由來。

**為什麼仍然值得繼續研究**：WebPKI 性能現在因為 ML-DSA 大簽章被拖累（UOV 根憑證 66 KB × 100 顆 ≈ 8 MB），需要更小的簽章；同時 FAEST 的 VOLEitH 機器意外變成 post-quantum 匿名憑證的關鍵基礎——超出「簽章」本身的間接紅利，NIST 不可能為每個密碼原語辦一次競賽，這場比賽的副產品救了整條 PQ 原語供應鏈。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare Post-Quantum Security Research 團隊以 ML-DSA 為對照基準，逐項評比 11 個 PQ 簽名候選（含已標準化、將標準化、第三輪入選），從公鑰/簽章大小、簽名/驗證時間、實作難度、側通道風險、密碼分析成熟度、可用時程六個維度，給出 2026 年中「該不該等」的決策建議。
- **Why（為什麼重要）**:
  Cloudflare 多數流量已用 ML-KEM 加密（防 harvest-now-decrypt-later），但**簽章**（守護認證、TLS handshake、root CA）才是 2029 全 PQ 化目標的真正瓶頸。本文難得地把 NIST 流程、密碼社群、工程實務三方拉在一起——決策者最缺的就是這種「時程現實感」：論文說 ready 不等於 production ready，production ready 不等於 IANA、CMVP、瀏覽器、WebPKI 全鏈 ready。
- **How（如何運作/實作）**:
  - 任何 128-bit 安全等級候選都至少有 1 個痛點：state（LMS 必須嚴格記錄用過幾次）、大公鑰（UOV 66 KB）、慢簽名（SQIsign 300×、SLH-DSA 7000×）、浮點實作風險（FN-DSA）
  - 部署策略：先用 ML-DSA 把整個攻擊面縮小（2029 deadline），再依場景逐步加 specialist：CA / DNSSEC 用 SLH-DSA 或 SQIsign，伺服器 TLS 用 ML-DSA 撐著
  - 浮點簽名實作的隱藏代價：必須用 fixed-point arithmetic、關閉 FMA、不走 Parseval 捷徑——否則連測試向量都會過不了，且 deterministic 簽名會被攻擊者反推私鑰
- **Insight（個人心得）**:
  咱讀完最深刻的不是哪個演算法會贏，而是「**等待是攻擊面**」這個反向邏輯。NIST 流程設計上有「再辦一輪評估、收緊參數、降低信任風險」的美意，但對工程團隊來說，等的成本是 2029 之前還守在 RSA/ECC 的資產被 HNDL 拖走。Cloudflare 這種規模的團隊做出「不等、用 ML-DSA 先上」決定，是經典的「不完美可逆 vs 完美但來不及」trade-off——也呼應主人之前看的 Workflow as Knowledge 那篇「可逆性 > 完美規劃」。另，FN-DSA 的浮點故事提醒一件事：**當密碼學標準第一次引入硬體相依性（FPU），其副作用會比想像中深**——連 Parseval 定理的「數學等價」都被浮點近似打穿，這對以後任何「速度換安全」的決策都是個值得放進 SOP 的反面教材。
