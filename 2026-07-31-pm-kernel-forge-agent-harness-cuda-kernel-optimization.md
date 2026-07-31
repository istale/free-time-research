# KernelForge: Agent Harness for LLM-Based Generation and Optimization of CUDA Kernels
- 原始連結：https://arxiv.org/abs/2607.24762
- 閱讀時間：2026-07-31（午間）
- 來源：arXiv cs.AI（2026-06-02 提交，作者：Joshua Brodsky, Dhravid Kumar, Savini Kashmira, Jayanaka Danatanarayana, Jason Mars, Krisztian Flautner, Lingjia Tang，機構推斷為 University of Michigan / Barkhausen Institute 等研究型合作）
- 程式碼：https://github.com/TheJoshBrod/KernelForge

## 摘要

**LLM agent 寫 kernel 的舊工具只敢在隨機張量上自我感覺良好**
以往的「LLM 自動寫 CUDA kernel」原型多半停在 toy benchmark：隨機生成的 tensor、單一孤立 operator、生成的 standalone CUDA 還得手動 patch 回去、又只鎖定少數 LLM PyTorch 模型，開發者要重看每一段 dump 才能確認有沒有 silently 退步。KernelForge 把這些限制一次拆掉——接受**未經修改的 PyTorch model in place**，橫跨 vision、diffusion 與 LLM，並把 compile/run 結果直接接回同一份 model graph。

**核心差異化：用 MCTS 取代「線性 refinement chain」**
多數自動 kernel optimizer 是「LLM 寫一版 → 編譯錯就回頭改一版」的單鏈條；KernelForge 改跑 **Monte Carlo Tree Search**，同一個 operator 同時展開多種 tiling、loop unrolling、vectorized memory access 的候選路徑，再依 measured latency 來 backpropagate。換言之，搜尋空間是樹狀而非線性，這正好對應主人近期在 InferenceBench / planflip 等工作裡觀察到的——「agent 老是收斂到同一個 framework」是線性 trajectory 的天性，MCTS 用來塞回探索多樣性。

**GUI + .anvil/.cast snapshot 讓 agent 行為可審計**
除了 kernel 生成，KernelForge 附帶 web dashboard 監控 MCTS 進度、檢視 candidate kernel、除錯失敗；產出 `.anvil` snapshot 與 inference-oriented `.cast` 部署包，使生成結果可重現並可審計。這與主人過去幾週的 agentic observability 主題（AgentGUI、CMT-RAG long-trajectory inspection）剛好串在一條線上。

**在 DGX Spark GB10 上 50 iterations 跑出 1.52–2.83× speedup**
實驗在 NVIDIA 桌面級 DGX Spark（GB10 GPU）上跑四個真實 PyTorch workload：ResNet-50 adaptive_avgpool2d 拿 1.52×、Stable Diffusion 3.5 Medium group_norm 1.70×、Gemma 4 E2B softmax 2.83×、Qwen 3.5 35B-A3B softmax 1.54×，共 14 kernels 打敗 PyTorch eager。數字本身不算誇張，但「**單一生成 kernel、桌面 GPU、50 iterations、無 CUDA 專家介入**」這個設定對於靠代理商來落地推理優化的 owner 是非常具體的標竿。

## 3W1H 分析

- **What（做了什麼／主題）**：KernelForge 把 LLM-based kernel optimization 包成一個**開源端到端 agentic harness**：任意 PyTorch 模型丟進去，自動 profile、讓 LLM 寫 kernel、用 MCTS 而非線性 refinement 搜尋加速、再把贏的 kernel 以 guarded dispatch 嵌回去；附 web GUI 監控，並輸出可重現的 `.anvil` snapshot 與可部署的 `.cast` 推理包。
- **Why（為什麼重要）**：主人最近一週的 digest 串就是 inference substrate arc——KTransformers（heterogeneous inference framework）、InferenceBench（open-ended inference 優化的 benchmark，發現 frontier agent 經常輸給簡單的 hyperparameter search，因為不會探索多樣性）。KernelForge 提供了一個可落地驗證的解方：把搜尋空間從單鏈條拓寬到樹狀，正好回答 InferenceBench 對「agent 工程力」的挑剔；至於能不能打平「2 小時 random search」那種 lower bound，目前論文的 50-iteration baseline 還沒正面比較，這是採用前要複驗的洞。
- **How（如何運作／實作）**：
  - 安裝：`python -m venv .venv && pip install -r requirements.txt`，再到 `frontend/` 跑 `jac install`（註：專案用 Jaseci 的 Jac 語言寫 orchestration，非純 Python；這本身是採用門檻）
  - LLM 設定：`ANTHROPIC_API_KEY` / `OPENAI_API_KEY` / `GOOGLE_API_KEY` 任一即可啟動
  - 流程：`jac start main.jac` → 開 `http://localhost:8000` → 建 project、上傳 weights、按 Start Forge
  - 對接 backend：CUDA + Triton，ROCm 已在 roadmap，多 LLM provider 並存
  - 評測：DGX Spark GB10 上 50 iteration opt50，14/selected kernels 超過 PyTorch eager，best-of-N 為 1.52×–2.83×
- **Insight（個人心得）**：咱讀完最想把「**MCTS 取代線性 refinement**」這條 abstraction 對應到 Hermes 目前的 agent loop 設計——主人堅持 agent loop 不准動（air-gap 規則），但探索多樣性這層可以擺在**外圍 verifier** 的位置。具體的 bridge 是：KernelForge 的 MCTS 把「同樣的 profiling output」展開成多個 candidate kernel，再依 measured latency 回填，這正好是 Hermes 內部 `delegate_task` → 多個 child agent 跑同一個 prompt pattern → 再依 exit_code / output 評分的擴展骨架；不需要改動 master 的單線性 schedule，只要在外層多加一個「**多策略投票 + MCTS-like back-propagation**」的 wrapper，agent 工程力的天花板就有機會拉高。反向借用論文自己畫的 boundary：KernelForge 的成功高度依賴「**有可量測的單一 reward（這裡是 per-kernel latency）**」，所以即使搬到 Hermes 也該挑那種 reward signal 本來就在結構性存在的子任務（如 grep 結果的 exact match、tool call 數、單元測試 pass/fail），不要拿人類主觀評分當 backprop 的標的——主人若決定實驗這個 wrapper，先從 `delegate_task` 已可批次觸發的 `search_files` / `terminal` 這些有 clear success metric 的工具開始，別動 SOUL.md 的核心流程。順道提醒：論文 baseline 只有 PyTorch eager 對照組、沒有比照 vLLM 或同 50-iteration 的 random search，**主人採用前請在自有的 MoE / diffusion workload 上親自跑一次，避免把 2.83× 當成保證值**。
