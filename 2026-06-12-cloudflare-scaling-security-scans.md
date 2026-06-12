# Scaling Security Insights: how we achieved a 10x increase in global scanning capacity
- 原始連結：https://blog.cloudflare.com/scaling-security-scans/
- 閱讀時間：2026-06-12

## 摘要
這篇文章來自 Cloudflare，原文連結：<https://blog.cloudflare.com/scaling-security-scans/>。主題是他們如何把 Security Insights 的全域安全掃描能力從約 10 scans/sec 提升到超過 120 scans/sec，而且不是單純靠加機器，而是拆解整條資料路徑上的瓶頸。

系統原本的掃描流程是：排程器決定哪些 account 或 zone 該被掃描，接著把訊息送進 Kafka，再由多個 Go checker 去檢查設定與資產，最後透過內部 API 把結果寫進 Postgres。問題在於掃描頻率太低，許多風險可能要一到兩週後才被發現，而且部分免費方案帳號甚至沒有預設自動掃描。若要把自動掃描擴大到所有帳號，吞吐量必須先提升約 10 倍。

作者先從 Kafka 消費端下手。由於 Kafka partition 內訊息必須依序消費，慢訊息會卡住後面的工作，形成 head-of-line blocking。Cloudflare 沒有先暴力增加 partition，而是把 checker 改成批次消費，讓單批內的多筆訊息用 goroutine 平行處理；同時再把 consumer group 分成 fast lane 與 slow lane，把預期很慢的工作導到專用車道，避免少數超大 account 或 zone 拖垮整批處理速度。

第二個瓶頸在資料寫入。原本 API 端對每筆 insight 都各自執行一次 `INSERT ... ON CONFLICT DO UPDATE`，大型請求甚至可能帶來 50 萬次 round trip。後來他們改成混合策略：小量資料用 `UNNEST`，大量資料用 `COPY`，兼顧小批次延遲和大批次吞吐。這個點很關鍵，因為很多系統的「規模化失敗」其實不是演算法問題，而是把單筆寫入模式硬撐到 bulk workload。

第三個瓶頸更有意思：API 是 active-active 跑在 Portland 與 Amsterdam，但主資料庫在 Portland。結果 Amsterdam 節點查 DB 的延遲高很多，平均 API 呼叫在 Portland 是 10ms，在 Amsterdam 卻接近 3 秒，進一步耗盡 client-side connection pool，導致 timeout 與 Kafka partition 處理落後。Cloudflare 最後把 API 切成 active-passive，讓活躍 API 跟著 primary DB 走，直接消掉跨洲延遲造成的連鎖反應。

最後是排程器。舊排程模型會在固定週期內把大量 account 一起判定為到期，造成某些時段瞬間爆量；大型 account 底下又可能連帶觸發海量 zone 掃描。修法包括：把 zone 與 account 的排程拆開、隨機化既有 `last_scheduled_at`、加入每半小時重算的 adaptive rate limiting。這讓掃描工作可以更平均地鋪在時間軸上，不會因為調高掃描頻率或帳號成長又把系統打爆。

整體來看，這篇文章最有價值的地方不是「Cloudflare 很會調效能」，而是它示範了如何按資料流逐段拆瓶頸：Kafka 消費模式、DB 寫入策略、跨區網路拓樸、排程分布，全都可能是吞吐量上不去的真正原因。最後成果也很直接：峰值超過 120 scans/sec、所有免費帳號預設開啟掃描、不同方案的掃描頻率同步提升，還順帶解鎖更細粒度的 on-demand scan。

## 3W1H 分析
- What（做了什麼/主題）: Cloudflare 分享他們如何重構 Security Insights 掃描管線，將全球安全掃描吞吐從約 10 scans/sec 提升到超過 120 scans/sec。
- Why（為什麼重要）: 安全風險如果一兩週後才被掃到，對現代自動化攻擊環境來說太慢了。這篇文章很實際地展示了「先理解瓶頸，再擴容」比直接加資源更有效。
- How（如何運作/實作）: 核心手法包括 Kafka 批次消費加 goroutine 平行處理、fast lane/slow lane 拆分、Postgres `UNNEST`/`COPY` 混合 bulk write、API 從 active-active 改為貼近主資料庫的 active-passive，以及加入 adaptive rate limiting 的新排程器。
- Insight（個人心得）: 我最有感的是它把「分散式系統效能問題」拆成拓樸、排程、資料寫入模式三層一起看。很多團隊看到 timeout 就先加副本或拉長 timeout，這篇反而證明先追指標、追路徑、追不平均分布，通常更接近真正解法。
