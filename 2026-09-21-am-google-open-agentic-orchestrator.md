# Google's Open Agentic Orchestrator (AX)
- 原始連結：https://agentexecutor.io
- 閱讀時間：2026-09-21
- 來源：Hacker News（topstory id 49780797，發布於 2026-09-21）

## 摘要

Google 把內部研究與營運 agentic runtime 的經驗整合成一個開源、宣告式（declarative）控制平面，名為 **AX（Agent eXecutor）**，定位是「給 agent 用的 Kubernetes」。它試圖解決的是一個越來越尖銳的問題：現代 AI agent 既不是微服務也不是批次任務，它有狀態、會長跑、要呼叫模型 API 與 tool server、在沒人看管的時候可能把錢燒光。

**四大核心 primitive**
- **Workspace**：宣告式綁定 git repo、分支、檔案掛載與依賴，並可選擇用自然語言 goal 讓生成式 agent 在首次啟動時把環境準備好。
- **Task**：YAML 描述一次 agentic 任務，引用 workspace、宣告 goal、決定 debug 模式。
- **Network policy**：每個 task 沙箱化、可被 fence 網路，避免 agent 在模型呼叫之外亂連。
- **Model**：把模型當成一等資源管理，可綁不同 provider / endpoint。

**底層是 Agent Substrate**
AX 跑在 [Agent Substrate](https://github.com/agent-substrate/substrate) 上，這是一個為「stateful actor lifecycle + 高密度多工」打造的 runtime。三個關鍵工程特性：
- **Sub-second resume**：idle 等模型回應 / tool 回應 / 人類覆核的 agent 會被 checkpoint + suspend，< 1 秒內 resume、零冷啟動延遲。
- **Dense multiplexing**：單一 worker 同時跑數十個 task，把「等回應」的空閒時間變成可分享的運算容量，只在 agent 真正在「想 + 跑程式碼」時計費。
- **Billion-scale actor**：每個 task 是一個輕量 actor，單叢集可達數十億個並行 agent session。

**Generative runtime 是賣點之一**
- **Generative workspace**：用一句英文描述「我想要的環境長什麼樣」，AX 開機時把 goal 丟給一個 agent 去裝 toolchain、驗證相依套件後才讓正式 task 啟動。
- **Use cases**：互動式 coding agent、長跑 agent server、Jupyter notebook、headless browser QA、自訂 tool runtime——也可以用來大規模跑 RL trajectory 收集或 agent 評測。

**背景定位**
專案自敘來自 Google DeepMind 內部 agentic runtime 研究，加上大規模 isolation / resumption / scheduling 的工程經驗；產品方向是「open control plane for agent execution」，把 task、workspace、network policy、model 抽象成核心 primitive，讓開發者與研究者不用每次重造底層。

## 3W1H 分析
- **What（做了什麼 / 主題）**:
  Google 開源了一個名為 AX 的「agentic orchestrator / control plane」，搭配底層 Agent Substrate runtime，提供宣告式 YAML 介面來描述 agent task、workspace、network policy 與 model，並主打 sub-second suspend/resume、dense multiplexing、billion-scale actor 等特性，目標是讓 agentic workload 不再借用為微服務或批次任務設計的傳統 orchestrator。
- **Why（為什麼重要）**:
  現在跑一支 production agent 的成本模型完全錯位——agent 90% 的時間在等模型回應，等的時候 CPU/GPU 與 sandbox 都在燒錢；同時它需要嚴格隔離、checkpoint、可恢復，傳統 k8s 既貴又不原生支援。AX 試圖把「為 agent 而生的 runtime」這塊空白補上，跟主人現在 Hermes 那套 Kanban + executor/reviewer 設計哲學完全對位：都把「等回應」當成一等公民處理。
- **How（如何運作 / 實作）**:
  - 用 YAML 宣告 `Workspace`（綁 git repo + 分支）與 `Task`（指定 workspace + goal），`ax apply -f task.yaml` 跑起來
  - 每個 task 跑在獨立 actor 上，idle 時 suspend + checkpoint，resume < 1s
  - Dense multiplexing 讓單 worker 同時跑數十 task，計費以「真正在想 + 跑程式碼」為單位
  - Generative workspace 用英文 goal 讓一個 bootstrap agent 把環境準備好，適合「沒人想寫 Dockerfile」的場景
  - 底層依賴 [Agent Substrate](https://github.com/agent-substrate/substrate) repo 處理 actor lifecycle 與排程
- **Insight（個人心得）**:
  這篇讓咱最在意的不是「Google 又開源了一個 runtime」，而是它正面承認了一件事——**orchestration 已經從「應用層框架」（LangChain / CrewAI）走到「基礎設施層」**。當 Google 願意把 agent runtime 跟 k8s 類比，就代表 agent 已經不是實驗室玩具、而是真要長期跑的 workload。
  對主人而言，這既是信號也是壓力：信號是 Hermes Kanban + heartbeat + suspend/resume 的方向走在對的路上（人家大廠也認同 idle-aware 是正解）；壓力是 Google 把整套抽象做成 open control plane，下一步若推出更完整的 model/tool/scheduler 整合，獨立 orchestrator 的護城河會被壓縮。
  咱的具體觀察是——**主人應該認真看一下 Agent Substrate 那個 repo**，它公開的是 runtime 本體，AX 是上面那層控制平面；如果 Substrate 設計乾淨，主人反而有機會把 Hermes 做成「跨 runtime」的 orchestrator 薄層（類似 kubectl vs k8s），而不是再寫一套 runtime。這條路跟主人「保守減法、端到端驗證、別重寫 runtime」的多條 memory 偏好高度吻合。