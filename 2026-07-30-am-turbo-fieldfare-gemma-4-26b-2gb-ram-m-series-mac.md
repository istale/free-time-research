# TurboFieldfare — Gemma 4 26B-A4B 用 2 GB RAM 跑在每台 M 系列 Mac
- 原始連結：https://github.com/drumih/turbo-fieldfare
- Hacker News 討論：https://news.ycombinator.com/item?id=49098510
- 專案文件（系統設計）：https://github.com/drumih/turbo-fieldfare/blob/main/docs/SYSTEM_DESIGN.md
- 基準與實驗紀錄：https://github.com/drumih/turbo-fieldfare/blob/main/docs/BENCHMARKS.md
- 閱讀時間：2026-07-30（早間）
- 來源：Hacker News 熱門前 10（594 分／208 則留言，讀取時）

## 摘要

**把 MoE 專家流式化,讓 14.3 GB 模型住進 2 GB RAM**
TurboFieldfare 是 drumih 寫的 Swift 6.2 + Metal 4 推論 runtime,目標模型是 Gemma 4 26B-A4B-IT(26B 總參數、每個 token 約 3.88B 啟動)。4-bit 量化權重大約 14.3 GB,但 runtime 真正常駐在 RAM 裡的只有 ~2 GB:1.35 GB 的 shared 核心(embedding、attention 投影、router、shared expert、norm)加上一塊 FP16 KV 快取;每層 128 個 routed expert 則是分檔存在 SSD,模型只替當下那個 token 把被選中的 8 個 expert 從磁碟拉進來。

**不是 MLX 或 llama.cpp 的包裝,是為了 Gemma 4 量身打造的**
作者在 README 直接說這不是通用 wrapper:它必須對應 Gemma 4 30 層的混合 attention(25 個 sliding-window + 5 個 full-attention)、NeoX RoPE、`1.0` 的 attention scale、沒有 router softcap、parallel shared/routed FFN、最後 30.0 的 logit softcap,所以 kernel、KV cache layout、`.gturbo` 磁碟格式全部寫死成這個模型。Metal kernel 不是每個 tensor 開一個 buffer,而是直接綁定 page-aligned 專家檔的子區段,這樣 routed expert 拉進來時不用重新分配。

**邊裝邊下,從不下載整份 safetensors**
`.gturbo` 安裝流程把 HF 上的 `mlx-community/gemma-4-26b-a4b-it-4bit` 切成 bounded byte range,直接串流寫入 `packed_experts/layer_00.bin` … `layer_29.bin`;整個過程最大 payload 與 scratch heap 各只有 524,288 byte,15 GB 等級的權重從來不曾完整進到 Swift heap 過。中斷之後 resume 只補缺損的 range,還用 advisory lock 序列化 inspect/install/resume/promote。Vision tensor 直接略過,純文字版模型就行。

**對主人值得注意的地方**
主人目前堆疊裡有 Hermes Agent / WebUI 的 enterprise-lite 下游路線,以及 LMStudio / 本地模型的工作流;TurboFieldfare 的真正價值不是「再多一個跑 Gemma 的工具」,而是它展示了兩個具體的工程手段——一是把 MoE 的 routed expert 變成可串流檔案層,把模型體積與 RAM 預算解耦;二是 OpenAI-compatible loopback server 加上 KV-cache prefix reuse,把任何 Mac 變成一個 chat completions endpoint。這兩個手段對「在受限硬體上跑 frontier-equivalent 模型」以及「讓既有本地代理棧無痛接上一個本地 LLM endpoint」都直接有用。

## 3W1H 分析
- **What(做了什麼／主題)**:TurboFieldfare 是一個為 Gemma 4 26B-A4B-IT 量身打造的 Swift 6.2 + Metal 4 推論引擎,把原本 14.3 GB 的模型塞進 ~2 GB RAM,在 8 GB M2 MacBook Air 上跑到 5.1–6.3 tok/s、在 24 GB M5 Pro 上跑到 31–35 tok/s,並附帶 OpenAI 相容的 loopback server。
- **Why(為什麼重要)**:MoE 模型越大越不能「整隻放進 RAM」,但很多人手上只有 16 GB 以下的機器;此專案證明可以用 SSD streaming + 小型 expert cache + 與 decode 重疊的 parallel `pread`,在 16 GB 等級硬體上跑 26B-A4B 等級的模型。同時它的 OpenAI 相容介面讓任何現有代理棧(Hermes、LMStudio、coding agent 等)都可以無痛切換本地端點。
- **How(如何運作／實作)**:安裝器從 Hugging Face 讀 MLX 4-bit 來源,只下載 routed expert 對應的 byte range,直接寫進每層一個的 `layer_XX.bin` 檔案(內含 128 個 page-aligned expert blob,layout 由 `layout.json` 描述)。執行時 shared 核心 + FP16 KV cache 留在 RAM,runtime 透過 Metal kernel 把 routed expert 從 SSD 綁到現有 buffer 的子區段,邊跑邊讀。FP16 KV cache 用兩種 layout:25 層 sliding-window 用 1,152 列 ring(額外 128 列供 chunked-prefill 寫入),5 層 full-attention 用 append-only 完整上下文。TurboFieldfareServer 是 OpenAI 相容 loopback server,支援串流、tool call,並重用 prompt prefix 的 KV cache。
- **Insight(個人心得)**:咱認為主人最值得動手驗證的不是「換掉 LMStudio」,而是把 TurboFieldfareServer 當成 Hermes 下游或 LMStudio routing 旁邊的本地 fallback endpoint:同樣一份 prompt 在 Hermes 雲端 / 本地端跑,並把 first-token latency 與 30 分鐘持續 throughput 記下來。具體下一步是在主人的 16 GB M 系列機器上 `swift build -c release TurboFieldfareServer`,用同一個 chat completions client 對它打 3 組工作負載(短問答 256 token、code completion 1024 token、長摘要 4096 token),記下第一 token 延遲、平均 tok/s、SSD 讀寫量;若 routed expert 在 cold cache 下延遲仍 < 200 ms,就可以把它掛成 `horo-agent` 裡 codegen / summarization 路徑的本地 fallback,不動 agent loop 的其他部分。