# We're making Tailscale faster
- 原始連結：https://tailscale.com/blog/making-tailscale-faster
- 閱讀時間：2026-09-24

## 摘要

Tailscale 2026-09-22 公開一篇 engineering update，把過去幾個季度悄悄做的 **data plane 加速**全部攤開來談——而且每一條都附了量化的數字和 commit 路徑。對主人來說這不是普通的 vendor blog：**主人的整個 Hermes 基礎設施都跑在這個 tailnet 上**（`100.94.155.28` 主機上的 memory-editor 8260、lmstudio-council 8100、hermes_webui 8787、PocketRisu 6001、ASMR Foley Lab 8000，全是 tailscale-serve 對自家網域開），而 49817469 那個網路瓶頸分類正是這篇要拆的對象。

**為什麼 small packet 一直是 Tailscale 的悄悄稅**
- 現實世界的 network packet 中位數大概 1 KiB，但 Linux 的 Generic Receive Offload (GRO) 是 64 KiB-aligned 的——wireguard-go 解密端只提供一個 64 KiB buffer 給 user space unpack，**每個 1 KiB 的小封包都被強迫複製進一個 64 KiB 的 buffer**。這是 container shipping 問題：港口蓋給 20 呎櫃，但貨物只有一張信封。
- 解法是「**別搬、只標起點/終點**」——Linux/Android 上現在直接把多個小封包留在他們從 kernel 讀進來的那塊記憶體，用 start/end offset 標記各自的範圍。**省下 copy 又省下配置**，單獨這一條在很多 network config 下帶來 ~5% speedup。
- 同時把 packet queue 縮短：測試顯示 queue depth 平常大多用不到，短 queue 既省記憶體又降延遲。多出來的 headroom 直接回饋到 subnet router / app connector 的工作集。

**Subnet router / app connector / exit node 的多佇列化（2026 下半年 landing）**
- 過去這三類節點是用**單一 ordered pipeline**：reader → crypto → writer 一條線串起來，多 stream 共用。理由是每個 connection 內部的封包順序不能亂（接收端會 reorder bug）。
- 改完後是**多 lane scaled to CPU cores，不是 scaled to peer 數**：每個 stream 拿到自己的 lane，lane 彼此平行。homelab 的小 subnet router 跟雲端 100+ peer 的企業 app connector 都受益，但受益最多的是 **app connector 跟 exit node**——它們天生是「many short-lived connections」的工作型態。
- 引用 Tailscale 自己的 Alex Valiushko：「從 kernel 讀出來到交還給 OS 這段，latency 直接降低。」對主人跑 tailscale-serve 對外暴露 webui 是直接利多：reader → crypto → writer 這段以前是單線瓶頸，現在會用光 CPU core。

**`writev` vectored write**
- 過去每個封包要走 user space → kernel 的 copy，kernel 還要把散在 buffer 裡的 metadata 拼起來。Linux 的 `writev`（v = vector）讓 Tailscale **只描述要搬什麼、不實際搬**。`writev` 在 spring 2026 已經 partial 落地，剩下配合 memory 改造再收割一波，排在 v1.104 之後的版本。

**Netmap caching：bad Wi-Fi 場景的 startup 救星**
- Tailscale 開機預設要打 control plane 拿 netmap——典型網路 100ms 就好，但**飛機 Wi-Fi、飯店 aggressive filter、或靠近 DERP 但 control plane 連不上**的環境就會卡死。
- 新機制：每台裝置在 disk 上 cache 一份 netmap snapshot；開機時**直接拿 cached netmap 跟其他 peer 建立 data plane**，再慢慢 background 更新控制面同步。這個互動是 peer-to-peer 協商，control plane 看不到流量（E2E 加密的延伸）。
- 量化的數字很驚人：**warm cache start 比 cold start 快一兩個 order of magnitude**。對用 tailnet 跑 CI / remote dev / agentic workflow 的人是真正的工作流改善。
- 限制：第一次必須成功連過 control plane 才有 cache；要 persistent disk；超大 tailnet 或 SD card / wear-sensitive storage 可選擇不開。Mobile 在 v1.104 之後才有，desktop v1.104 default-on。

**Tooling 缺口：Tailscale 想做的「native perf testing」**
- 最後一段直接認：**現有 perf tooling 不是 Tailscale-aware 的**。iperf 是 point-to-point、要兩端都裝；很多工具不支援 QUIC / HTTP/3；最關鍵——**沒人知道當下 connection 是走 DERP relay 還是 direct、是 fixed 還是 peer-relay、這條路徑這秒會變嗎**。Tailscale 正在 typeform 收 customer feedback，要做一個 Tailscale-native 的 monitoring/testing toolkit。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Tailscale 公開 2026 H2 data-plane 加速 roadmap，涵蓋四條獨立戰線：(1) **小封包 buffer 改造**——不再把小封包 copy 進 64 KiB buffer，改用 offset tag 多封包共享單一 allocation，順手砍 queue depth（v1.104 落地，~5% speedup）；(2) **Subnet router / app connector / exit node 多佇列化**——從單一 ordered pipeline 改成「per-stream lane × CPU 數」平行架構；(3) **`writev` vectored write**——user → kernel copy 次數下降；(4) **Netmap caching**——disk-cache 控制面 snapshot，讓 bad-network 場景的 startup 從 cold start 變 warm start，速度提升一兩個數量級。同時承認 perf tooling 是缺口，要做 Tailscale-native 的診斷工具。

- **Why（為什麼重要）**:
  主人整個 Hermes 基礎設施的對外暴露**全部依賴 tailscale-serve 對自家網域**（memory-editor、lmstudio-council、hermes_webui、ASMR Foley Lab、PocketRisu、tailscale-curation-server），且 `~/.openclaw/workspace/projects/linclaw` 的 node server、ComfyUI remote、YouTube AI summary server 都在同一個 tailnet。多佇列化直接受益於「subnet router / app connector 多 stream 並發」這個主人 tailnet 的真實形狀（多個 port 跑多個 daemon）；netmap caching 對主人這個**常在飯店 / 跨城市移動**的工作形態是真正的工作流改善。另外主人有 `tailscale-serve-url` skill，理論上可以拿來驗證「before/after 同一個 service 從 cold → warm start」的實測。

- **How（如何運作/實作）**:
  - **小封包 buffer**：在 user space 對 kernel read 的結果 buffer 用 start/end offset 標記每個封包，而非 per-packet copy；queue depth 從 profiling 數據回收，不硬撐 burst capacity。
  - **多佇列**：lane 數 scale to CPU cores、**非 scale to peer 數**；每個 TCP/UDP stream 拿到一條 lane，intra-stream ordering 保留，跨 stream 平行。
  - **`writev`**：kernel 把多個 iovec 一次性寫進 socket buffer，省下 user space 的 gather step。
  - **Netmap caching**：控制面 snapshot 序列化到 disk；開機時先以 cached netmap 建立 peer-to-peer 連線，再 background 重連 control plane 更新；對超大 tailnet / SD card 用 opt-out flag。
  - **未來 tooling**：要解決「不知道 connection 走 DERP 還是 direct」「不知道 peer relay 是否更短」「不知道路徑隨時間怎麼變」這三個監測盲點。

- **Insight（個人心得）**:
  這篇最值得主人留意的不是單一 5% speedup 或多佇列化，而是 Tailscale **把「瓶頸分類」這門工程做到了 product roadmap 等級**：他們明確承認「perf tooling 缺口」是他們主動要解決的客戶體驗問題，而不是把「請裝 iperf」丟回去。對主人正在做的 Hermes 觀測基礎設施來說是個鏡子——**永遠把可觀測性當一級 feature 做，而不是事後補丁**。具體到這主人 tailnet 的下一步：
  1. **v1.104 出了之後先 upgrade tailscale clients**，特別是主機那台跟常駐跑 daemon 的裝置；multi-queue 落地後重新測一輪 tailscale-serve 對外 6 個 port 的 latency 分布。
  2. **開 netmap caching**，但要 watch 一下主機磁碟 I/O（主人的 VirtualMac2 16GB RAM 那邊磁碟是 virtualized storage）；若 SSD 沒問題，bad-network 場景的開機時間會有感。
  3. **`tailscale-serve-url` skill 那個工作流**，把「同一個 service cold start vs warm start」做成固定 regression check——這篇講的「bad airplane Wi-Fi 場景」正是主人 mobile workflow 的常態。
  4. **連線路徑觀測**：主人目前沒有對 6 個服務做「DERP vs direct」的監控，可以借 Tailscale 公開的 status API 拉這條 metric，順手做 dashboard——這也是主人觀測面的一塊拼圖。
