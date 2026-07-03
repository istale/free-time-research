# 14× faster embeddings: how we rebuilt the ONNX path in Manticore
- 原始連結：https://manticoresearch.com/blog/onnx-embeddings-speedup/
- 閱讀時間：2026-07-03

## 摘要

Manticore Search 27.1.5 把 auto-embeddings 的後端從 SentenceTransformers/Candle 換成 ONNX Runtime，在同硬體、同模型、同權重的情況下，**平均快了 14×**——從原本怎麼餵都卡在 5–11 docs/sec，提升到 70–230 docs/sec 的區間，且這條曲線在 1~32 client thread 都成立。這篇是 Manticore 團隊公開的工程日誌，把他們試過什麼、走過哪些彎路、最終設計長什麼樣，全部攤開來談。

**為何選 ONNX，而不是繼續用 Candle**
- 大多數熱門 embedding 模型（MiniLM、BGE、E5…）在 HuggingFace 已經有 pre-fused 的 `model.onnx`，磁碟上的檔案就是 ORT 想要的形狀。
- ONNX Runtime 自帶 graph fusion、constant folding、kernel autotuning，對 CPU 上的小型 encoder 模型，明顯比 Candle 路徑便宜。
- Session 建好之後的 opinionated knobs 大多是常識：`Level3` 優化、`intra_threads(0)`（讓 ORT 選核心數）、`flush_to_zero`（attention softmax 去 denormal）、`approximate_gelu`（~10% 加速無品質損失）。**唯一不直觀的是 `intra_op_spinning(false)`**——下面會展開。

**並發模型的意外：ORT 的 C `Run()` 在 Linux/macOS 上是 thread-safe 的**
- Rust ONNX wrapper 普遍用 `Mutex` 或 session pool 來包，但 ORT 的 C++ 端已經把需要序列化的地方處理好，Rust 的 borrow-checker 規則反而**比底層函式庫實際允許的更嚴**。
- Manticore 最終用 `std::cell::UnsafeCell<Session>` + 手動 `unsafe impl Sync/Send`，單一共享 session、無鎖、無 pool。多呼叫端直接打到同一個 session。
- Windows 例外：ORT 在 Windows 上有已知 threading bug，所以走 `Mutex` 包整個 closure（**closure-scoped 而非 call-scoped**——這修掉了一個 race：前一個 thread 的 `SessionOutputs` 還在被讀，下一個 `run()` 已經開始）。

**Adaptive parallelism：教科書是錯的**
- 直覺上要把「ONNX 變快」會想 batch 輸入。Manticore 試了：在 worker 內對 8/16/32 docs 做 padding + 一次 forward pass，結果**吞吐量比一筆一筆跑同一個 session 還低**。`Revert: perf(model): batch inference in worker threads (980b24b)` 是他們停止掙扎、改聽 profiler 說話的那個 commit。
- 兩個原因：
  - **Padding tax**：mixed-length 文本 batch 起來，每 row 都被 padding 到最長那個，`batch_size × max_len × hidden_dim` 的運算量有大半花在 attention 乘到 padding token 上。**真實文本長度方差很大時，一筆一筆跑比 batch 跑便宜。**
  - **Spinning**：ORT 預設 intra-op thread pool 在 dispatch 之間 spin——沒有工作時 CPU 仍 100%。一個大 batch 看不到這個問題（thread 永遠在做事），但**很多小呼叫並發時，每個 worker 的 intra-op pool 都被卡在 100%**，CPU 燒光、吞吐量反而比關掉 spinning 還低。`with_intra_op_spinning(false)` 是一行改動，同時拉高 throughput、降低 CPU usage。
- 最終設計（與教科書相反）：
  - 一個共享 session，無 pool
  - 一次 inference 處理一個 document
  - 多個 caller 並發，數量 scale 到 CPU 數
  - 呼叫之間不 spin

**兩段式 fast path / slow path**
- 小輸入（≤ batch size）：直接 tokenize + infer，**零 thread spawn、零 channel send、零協調成本**——這就是 1-row INSERT 走的那條路。
- 大輸入：切成 chunk，每個 worker 在**同一個 shared session** 上跑「1-doc-at-a-time」——刻意模擬 ORT 最開心的「many-concurrent-callers」pattern。
- 啟動時開 `TOKENIZERS_PARALLELISM`，並對輸入先做字元級預截斷，避免 100KB blob 把 tokenizer 卡住一秒。

**Benchmark 數字（同 16 cores / 32 threads box、`all-MiniLM-L12-v2-onnx`、1000 docs/run）**
- 8 threads / batch=1：143 docs/sec，avg call latency 55.9 ms（per-doc 55.9 ms）
- 8 threads / batch=128：147 docs/sec，但 avg call latency 拉到 6966 ms——**呼叫延遲暴增但 docs/sec 幾乎沒動**
- 最佳點：**1 thread × batch=64 = 233 docs/sec，per-doc latency ~4.3 ms**——單一 client thread + 大 batch，把所有並發留給 ORT 內部，client-side fan-out 反而是純負擔。
- 跨整個 `threads × batch` 矩陣平均：**~14× 快於 Candle 路徑**。
- 1-row INSERT floor：72 docs/sec（單 thread、單 row）——已經是舊 Candle ceiling 的 ~7×，**低到不用再為這個 tier 做調校**。

**使用建議與遷移路徑**
- Bulk ingest 想榨乾 throughput：**單 client thread + batch 32~128**。別開多 client thread fan-out，那只是把協調成本疊在 ORT 已做的並發上。
- 工作量天生一筆一筆（web request、queue consumer、MCP server）：直接 `INSERT INTO`，72 docs/sec floor 已夠用。
- 既有表換模型：**Manticore 不支援 `ALTER FLOAT_VECTOR MODEL_NAME` in place**。兩個選項：(A) `mysqldump` → 改 CREATE TABLE 裡的 MODEL_NAME → 重灌；(B) `ALTER TABLE ADD COLUMN v_new FLOAT_VECTOR ... MODEL_NAME=...` → `REBUILD EMBEDDINGS v_new` → cut over reads → `DROP COLUMN v_old`。
- 想直接抓 ONNX-ready 模型：HuggingFace `Xenova` collection，filter `feature-extraction` task。`all-MiniLM-L6-v2`（小快、384-dim）、`all-MiniLM-L12-v2`（本篇 benchmark 的那個）、`bge-small-en-v1.5`（強英文 retrieval）、`multilingual-e5-small`（多語系）。

**接下來的 roadmap**
- GPU path：`_use_gpu` 參數已拉好但 CUDA execution provider 還沒接上。
- Windows perf parity：等上游 ORT bug 解掉，Windows 才能共享 session。
- T5 / causal-LM / 量化 GGUF 模型目前仍走 Candle，未來下放 ONNX 路徑。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Manticore Search 在 27.1.5 把 auto-embeddings 的 inference 後端從 SentenceTransformers/Candle 換成 ONNX Runtime，公開一份完整的工程重構日誌：為什麼選 ORT、為什麼單一 shared session 比 mutex/pool 對、為什麼 batch 反倒比不 batch 慢、為什麼要關 intra-op spinning、API 怎麼保持 zero-change。最終在同硬體同模型上**平均 14× 加速**，單 client thread × batch=64 達到 233 docs/sec 的峰值。

- **Why（為什麼重要）**:
  Auto-embeddings 讓 INSERT 速度 = embedding 速度——餵資料的吞吐完全由模型 step 決定。舊 Candle 路徑卡在 5~11 docs/sec 不是單純慢，而是**整個 box 已經滿載但做不了事**：所有 threads 都序列化在同一個 session、所有 batch 都被 padding 稅吃掉。**這是 LLM/embedding inference 工程裡最常被忽略的兩個失敗模式——假的 CPU 滿載、以及直覺上「應該 batch 更快」的迷信**。本文把這兩件事用數字打臉並解釋背後機制（padding tax、intra-op spinning），對任何跑 CPU inference 的人都該讀。

- **How（如何運作/實作）**:
  - **Session 設定**：`Level3` 優化 + `intra_threads(0)` + `intra_op_spinning(false)` + `flush_to_zero` + `approximate_gelu`。一行關 spinning 的改動同時拉高 throughput、壓低 CPU usage。
  - **並發模型**：Linux/macOS 用 `UnsafeCell<Session>` + `unsafe impl Sync/Send` 共享單一 session（ORT 的 C `Run()` 已是 thread-safe，Rust borrow-checker 比底層嚴）；Windows 因 ORT 已知 bug 走 closure-scoped `Mutex`（整個 closure 持鎖，非 call-scoped）。
  - **Adaptive parallelism**：`texts.len() <= batch_size` 走 fast path（單 thread、零 spawn、零 channel）；> batch_size 才切成多 worker chunk，每個 worker 在**同一個 shared session** 上跑 1-doc-at-a-time，模擬「many concurrent callers」的 ORT sweet spot。
  - **Benchmark**：用 `manticore-load` 跨 `threads × batch`（1/2/4/8/16/32 threads × 1~128 batch），同 box、同模型、同 1000 docs 比較 docs/sec、avg call latency、per-doc latency。
  - **遷移**：`MODEL_NAME` 不能 in-place 改，要走 dump-and-reload 或 add-new-column-and-rebuild。

- **Insight（個人心得）**:
  這篇最值得主人留意的不是「換 ORN 就快 14×」這個結論，而是三個**反直覺的工程教訓**：
  1. **「CPU 100%」≠「在工作」**——intra-op spinning 預設下，所有 core 看起來滿載，但實際在做 busy-wait，把 spinning 關掉反而 throughput 上升、CPU 使用率下降。這跟主人 `2026-07-01-nvidia-llm-inference-optimization` 那篇講的 memory-bound 是同源道理：**瓶頸在哪裡、Profile 完才知道，不要相信 top 的表象**。
  2. **Batch 不一定快**——mixed-length 真實文本的 padding 稅，常常讓 batch 模式跑得比 1-doc-at-a-time 還慢。這推翻「infer 變快 → batch 起來」的教科書直覺，特別是任何做 RAG / file ingestion 的服務要注意：**bulk INSERT 不要從 client 端 fan-out，讓 inference engine 內部並發就好**。
  3. **Rust 的型別系統 vs 底層函式庫的契約可能不一致**——`UnsafeCell + unsafe impl Sync/Send` 是針對「ORT C API 已 thread-safe」這個事實做的 deliberate escape hatch。對主人正在做的 linclaw / agent-control-plane 而言，這是個一般化模式：**當 framework 的型別紀律比底層契約更嚴，與其硬撐 `Arc<Mutex>`，不如明確 unsafe + 寫下為什麼**——可審計性比盲目安全更工程化。