# Daedalus-150M: A Convolution-Attention Hybrid Designed for CPU Inference
- 原始連結：https://arxiv.org/abs/2608.20210
- 程式碼：https://github.com/unseen1980/daedalus（Apache-2.0，model + checkpoints + 4-bit GGUF release）
- 閱讀時間：2026-08-22（午間）
- 來源：arXiv cs.AI 昨日新論文（2026-08-20 16:09:43Z 發布；`cat:cs.AI+OR+cat:cs.CL` API 查詢 sortBy=submittedDate descending max_results=20）

## 摘要

**150M 參數的 CPU-first 小語言模型，把 18 個 transformer block 裡 12 個換成「2-timestep 寬的短 convolution」、只留 6 個 full-attention block**——一個為「單用戶、單卡、4-bit、純 CPU」設計的 hybrid 架構。訓練 59.9B tokens from scratch，5-task 平均分 47.31（勝過 GPT-2 124M / Pythia-160M / OPT-125M / GPT-neo-125M 全部 3–6× 多訓練量，也勝過 MobileLLM-125M 發佈分數；只輸給 SmolLM2-135M 2T tokens 那個）。驗證集 bits-per-byte 0.8685 / 645M tokens held-out。論文裡最關鍵的兩個 trick 是 (1) **寫死 winning condition 之前**先訓練同 size 同 data 的 all-attention twin，最後 hybrid 只贏 0.81%、但 4-bit 檔案小 6.3%、CPU decode 1.76× fast @ 2048 tokens（vs 外部 135M peer 達 2.08×）；(2) **speedup shape 是結果**——empty context 時兩個 twin 幾乎一樣快（hybrid 沒什麼好贏的），speedup 隨 context length 增加才出現，這正是「attention KV cache 隨 context 線性增長、conv state 永遠 2-timestep 寬」會預測的形狀，不是 leaner model 會出現的形狀。

**為什麼 CPU decode 1.76× 是非線性 memory-bandwidth 解釋的**：論文的 first-order memory-bandwidth model 在 2048 tokens 只能預測 1.17×，剩下 1.76 / 1.17 = 1.50× 的 speedup 來自 **latency-bound cache traversal 與 layer count 本身**——每個 attention layer 都必須在每個新 token 重新讀一次 cache，conv layer 不會。所以 per-token context cache volume 從 24-layer all-attention 的 12,288 bytes 砍到 hybrid 的 6,144 bytes（剛好一半），2048 tokens 等於從 25.2 MB re-read 降到 12.6 MB re-read，加上 attention layer 從 18 個降到 6 個，cache traversal 數從 18 降到 6，兩件事疊起來 memory-bandwidth model 預測不到的那段 latency 消失。

**程式碼、checkpoint、4-bit GGUF 全部 release + 跑得動**：`brew install llama.cpp` + `hf download Unseen1980/daedalus-checkpoints instruct/model-q4_0.gguf` + `llama-cli -m ~/daedalus/instruct/model-q4_0.gguf -cnv`，8 threads CPU decode。架構形狀 18 blocks（d_model 768 / vocab 49,152 / context 2048），block 類型分布 C C C C A C C A C A C A C A C C A C（A=GQA 12 query / 4 KV heads、C=depthwise kernel-3 兩步寬 fixed state）。模型也明確承認失敗：unmitigated 4-bit quality cost 約半個 conv channels inert 拿不掉、vocabulary 對 150M 太大。對主人來說：直接可在 16GB Mac 跑、Apache-2.0 + llama.cpp runtime、horo-agent lite / air-gapped 下游若要 ship 內建 SLM 而不是依賴 LMStudio server，這條架構線是可落地 prototype 的候選人。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Daedalus-150M 是一個 150M 參數的 CPU-first SLM：把 18-block transformer 拆成 6 attention + 12 short convolution（kernel-3、2-timestep fixed state），訓練 59.9B tokens from scratch，量化成 4-bit GGUF 在 llama.cpp 上跑出 2048-context CPU decode 1.76× speedup vs 同 size 同 data 的 all-attention twin，2.08× vs MobileLLM-class 外部 peer。論文把「fixed-state convolution layer」當作 KV cache 的替代品——conv 不需要回頭讀歷史，所以 cache 不會隨 context 變大；代價是 long-range modeling 弱（只靠 6 個 attention layer 串接）。所有 tradeoff 都先寫死 winning condition 再驗證，避免 cherry-pick。
- **Why（為什麼重要）**:
  主人目前的 horo-agent / horo-webui air-gapped 下游在 16GB Mac 上若要 ship 內建 SLM（不依賴 LMStudio server、不需要 GPU），目前能選的選項不是太大跑不動（Qwen3-TTS 1.7B 已經吃滿 + KV cache 沒空間）就是太小太笨（GPT-2 124M 等級）。Daedalus 證明一個介於中間的設計點存在：150M + 6 attention layer + 4-bit + llama.cpp + context 2048 + CPU-only，這個 sweet spot **horo-agent lite 完全裝得下**。對「語音優先 agent」(今天 AM pick Qwen3-TTS) 也是天然搭——TTS 端跑 H100 server-side、SLM 端跑 CPU client-side 對話管理，latency 預算拆開。
- **How（如何運作/實作）**:
  - **架構**：18 blocks、d_model 768、vocab 49,152、context 2048；block 排成 C C C C A C C A C A C A C A C C A C。attention block 用 GQA（12 query / 4 KV）省 KV cache bandwidth，conv block 用 depthwise kernel-3 + 兩步 fixed state（不管 context 多長，state 永遠兩個 timestep 寬）
  - **訓練**：from scratch / 59.9B tokens / 自訂 winning condition（hybrid 在 5-task 上只贏 0.81%，但工程 metric 全面贏——這是事先寫下的規則，不是看到結果才說的）
  - **量化與 runtime**：`Q4_0` GGUF + llama.cpp。CPU decode 4-bit / 8 threads @ 2048 context：739 tok/s Daedalus vs 420 tok/s dense twin = 1.76×；vs 外部 peer = 2.08×。memory bandwidth first-order 預測只 1.17×，剩 1.50× 來自 latency-bound cache traversal + layer count reduction
  - **驗證**：5-task (HellaSwag / ARC-Easy / PIQA / OpenBookQA / WinoGrande) — peer 全部 re-scored 同一個 harness，不是 paper-quoted 數字；bits-per-byte 0.8685 / 645M-token held-out set；shape-of-table 論證（speedup 隨 context 增長才出現）
  - **失敗也寫出來**：unmitigated 4-bit quality cost、~一半 conv channels inert 拿不掉（不是 tweakable）、vocab 對 150M 太大。這是 paper 的「brevity」證據，不是藏在 supplementary 裡
- **Insight（赫蘿心得）**:
  主人今天 AM pick 是 Qwen3-TTS sub-50 ms inference-optimization（**GPU 端的 shared-scheduler 軸**），今天 PM 選 Daedalus-150M 剛好是 **CPU 端的 cache-bypass 軸**——兩個 pick 對齊成「主人 inference-optimization thread 在兩端的兩個 leg」。但我真正想抽出來的不是 pair，是論文的 **「winning condition 先寫死、再驗證」方法論**：Daedalus 在品質 metric 上只贏 0.81%（統計噪音邊緣），但在工程 metric 上贏 1.76× / 2.08×，而作者選擇在論文中把 0.81% 跟 1.76× **並列**而不是只看後者——因為 winning condition 是事先定的「hybrid 必須 5-task 不輸 twin」、現在 hybrid 只贏 0.81% 就直接誠實寫出來。這跟 hermes-agent-lite 的 substrate decision 完全同構：主人若要在「重新寫 runtime」vs「保留現有 air-gapped 行為 + 減法」之間選，後者的 winning condition 是「現有 hermes-agent-lite baseline 的 E2E 行為必須不退步」——而 2026-07-25 那次打回就是「沒先把 winning condition 寫死、看到減法省 code 就以為贏」，這正是 Daedalus 用 twin + pre-registered metric 解決的那個 failure mode。具體可移植啟示：(1) **horo-agent lite 之後若要 ship 內建 SLM，Daedalus 150M + llama.cpp 是目前最乾淨的選擇**（Apache-2.0 / 4-bit GGUF / 16GB Mac 跑得動 / context 2048 對對話足夠），第一個 PR 可以直接拿 `unseen1980/daedalus` HF checkpoint 接到 hermes-agent 的 tool-call loop；(2) **論文的 first-order bandwidth model 解釋不了 1.76× 的 1.50× 殘差**——主人之前在 8/04 KV cache 完整性 / 8/07 vLLM disagg / 8/11 Muse Glimmer / 8/20 DFlash 2 這條軸累積的「latency-bound cache traversal 是 hidden cost」觀察，今天在 CPU 端有了 quantitative 證據，可以收進下一輪 hermes-agent-lite 的 cost model。

## 附錄：reject-set tracing（08-21 codification #10 配套）

今日 PM tier-1 (arXiv) 評估 3 個同分候選後決定：

| 候選 | arXiv | 為何最終不選 |
|---|---|---|
| **Daedalus-150M** | 2608.20210 | **★ 採用**：CPU 端 cache-bypass 軸 + 與今日 AM（TTS inference-impl on GPU）形成 H100/CPU 雙 leg + Apache-2.0 GGUF 直接可在 16GB horo-agent lite 落地 + 論文 winning-condition pre-registration 方法論同構主人 air-gap 決策模式 |
| Break It Down, Pass It On | 2608.20274 | skill 跨任務轉移研究：text skill 高於 code skill、subtask-level 高於 task-level、提出 skill utility score。與主人 substrate-arc 軸不重疊（不是 inference / 不是 agentic substrate / 是純 skill-memory 經驗論），且 benchmark 不在主人既有 benchmark family |
| Pandora's AI Model Routing Box | 2608.20316 | routing 與 costly value estimation：模型路由最佳化，主人的 horo-agent lite 在 air-gapped 環境沒有「multiple specialist」假設，路由問題不存在於此 stack |