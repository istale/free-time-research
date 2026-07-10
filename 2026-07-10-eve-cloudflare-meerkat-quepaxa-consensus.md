# Introducing Meerkat: an experiment in global consensus
- 原始連結：https://blog.cloudflare.com/meerkat-introduction/
- 閱讀時間：2026-07-10

## 摘要

本文由 Cloudflare Research 發布，正式介紹其內部研發一年的全球共識服務 **Meerkat**，核心使用 2023 年發表的 **QuePaxa** 共識演算法（由 Tennage & Băsescu 等人提出）。

**背景與動機：**
- Cloudflare 有 330+ 個資料中心，許多內部服務需要跨 DC 讀寫控制平面狀態（control-plane state，例如資源 placement、資料庫 leadership）
- 必須同時滿足 *linearizability*（強一致性）與高可用（單機故障/網路降級都不能中斷寫入）
- **現有 Raft 不合用**：仰賴單一 leader，leader 故障或網路延遲會觸發 timeout，整個叢集寫入就會停擺；在 WAN 環境中 timeout 極難調校
- Cloudflare 過去已因 leader unavailable 觸發多次 incident

**核心架構：**
- 每個 Meerkat 應用請求一組 replicas，所有 replica 兩兩連線，皆可接收讀寫
- 每個 replica 把應用請求（get / put）翻譯成 **log events**，透過共識演算法散佈，所有 functioning replicas 持有**完全相同的 log**
- 每個 replica 在 log 上「hosting」多個應用（如 KV store、leasing system），各自從 log events 構建狀態
- 應用只關心 log 內容，Meerkat 核心只負責共識 log slot 的決定

**QuePaxa vs Raft：**
- QuePaxa **沒有 leader**：任何 replica 都能發起寫入，progress 永遠不被 timeout 中斷
- 透過 majority quorum 決定每個 log slot 的值；當 replica Y 發現 slot 已被多數決定但自己錯過時，會被多數 replica「強迫 sync」並把新請求遞延到下一個 slot
- 容錯條件相同：2f+1 台機器中只要 majority (f+1) 活著就能持續運作
- 不處理 Byzantine faults（惡意節點）

**Insight**：Meerkat 把「strong consistency = single thread mental model」的好處保留下來，卻犧牲 Raft 的 leader 簡潔性，換取 WAN 上的可用性。對 Cloudflare 而言，這是 control-plane 服務 SLA 的根本性投資。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Cloudflare Research 公開自家內部研發一年的 Meerkat 共識服務，使用 QuePaxa leaderless 演算法，把 Raft 在 WAN 上的可用性缺陷徹底消除，並在 log 之上建構 KV store、leasing 等應用層。
- **Why（為什麼重要）**:
  全球規模的 control-plane 服務（AI 模型 placement、跨 DC leadership election）必須在 330+ 個 DC 之間維持強一致。Raft 類 leader-based 演算法在 WAN 上的 timeout 調校幾乎無解——這是 Cloudflare 多次 production incident 的根因。Meerkat/QuePaxa 提供了一條 leaderless 的出路。
- **How（如何運作/實作）**:
  - 每個應用請求一組 2f+1 個 Meerkat replica，可指定允許託管的 DC，Meerkat 自動擺放
  - 寫入：任何收到請求的 replica 把操作打包成 log event，透過 majority 共識決定 log slot
  - 讀取：也走共識（為了 linearizability），落後 replica 會被多數 replica「拉回」到最新 decided slot 之後
  - 不處理 Byzantine faults，只處理 crash / network failure / degradation
- **Insight（個人心得）**:
  Meerkat 的設計對主人而言是個值得記下的設計模式：**「leaderless + log-replicated = WAN-friendly consensus」**。對比之下，etcd / Consul 採 Raft 在 LAN 內沒問題但跨 WAN 部署就有雷區；若主人日後要在跨區域部署強一致 KV（例如多區域 GitOps state、跨雲 leadership election），可以借鏡 QuePaxa 的思路——犧牲一點 Raft 的 mental simplicity 換來永不因 leader 故障停擺。這也是為何 Cloudflare 把 QuePaxa 從學術 paper 推進到 production-grade 全球服務的價值所在。