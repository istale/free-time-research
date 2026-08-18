# Agentao: A Governed Local-First Runtime for Tool-Using LLM Agents
- 原始連結：https://arxiv.org/abs/2608.13574
- 來源：arXiv cs.AI（2608.13574，2026-08-17 提交）
- 閱讀時間：2026-08-18（午間）

## 摘要

本文由 Bo Jin、Qiang Jiao、Xin Tong 提出 **Agentao**，一個專為本地端 tool-using LLM agent 設計的「governed local-first runtime」。論文核心論點：當 agent 已經從「對話者」進化為「執行系統」（呼叫工具、修改本地狀態、使用持久記憶、跟外部 protocol 互動），傳統 chat-style 的 prompt 邊界已經撐不住 — over-privileged actions、弱 auditability、prompt injection、tool poisoning、uncontrolled side effects 全部會從 host / agent 邊界滲進來。

**Governance 與 execution 的徹底分離**
- Agentao 把「模型產生的 action proposal」跟「host 授權的 execution」切成兩個獨立層。任何工具呼叫、檔案寫入、網路請求都必須先經過 host contract，再由 runtime core 排程；模型本身**不能直接執行**任何有副作用的動作。
- 這個分離不是理論安全保證 — 論文明說「Agentao does not provide formal safety guarantees」，而是把 permissions、state、protocol boundaries、execution traces 全部變成 explicit runtime abstractions。

**Layered architecture（六大層）**
1. **Host-facing surfaces** — 對外的 UI / API / hook 介面
2. **Host contract** — host 跟 agent 之間的授權合約（who-calls-what 的明確邊界）
3. **Runtime core** — 排程、policy enforcement、event emission
4. **Permission-mediated tool system** — 每個 tool 帶有顯式權限描述，呼叫前要 match policy
5. **Supporting subsystems** — memory、replay、plugins、skills、sub-agents、protocol integration
6. **Structured event interface** — 所有執行事件都以結構化形式吐出，供 audit / debug

**Threat model 與 audit envelope**
論文從 threat model 倒推設計：把 prompt injection、tool poisoning、uncontrolled side effect 都建模成「應該被 host 攔下來」的請求。Execution trace 是 replayable 的，host 可以事後重放整個對話並比對「模型說要做什麼」vs「runtime 實際做了什麼」。

**核心 insight**：對於本地端 agent runtime，「治理」不是後來加上去的 compliance layer，而是要把 permissions、state、protocol boundaries 從一開始就當作 first-class runtime abstractions。這對應到 master 的 `horo-agent` / Hermes-agent downstream 工作：air-gapped 場景下 host 控制權比 open-cloud 更關鍵，這套 layered model 正好提供了一個可以借鏡的設計骨架。

## 3W1H 分析

- **What（做了什麼/主題）**:
  論文提出 Agentao — 一個 governed local-first runtime，透過「model proposal」與「host-authorized execution」的徹底分離，以及六層 architecture（host surfaces / contract / runtime core / permission-mediated tool system / supporting subsystems / structured event interface），把 LLM agent 從 chat-style prompt 邊界升級為可治理、可審計、可 replay 的執行系統。

- **Why（為什麼重要）**:
  現在主流 agent framework（LangGraph / AutoGen / CrewAI）幾乎都是「模型直接執行」 — 一旦 prompt injection 或 tool poisoning 進來，整個本地狀態就會被污染。本地端 / air-gapped 場景下，host 對執行權限的控制力必須大於模型本身。Agentao 把這個需求形式化為「permissions / state / protocol / trace 都是 first-class abstractions」，對 enterprise-lite / downstream 場景的 horo-agent 設計有直接參考價值。

- **How（如何運作/實作）**:
  - **Action proposal layer**：模型只產出結構化「要做什麼」的描述（tool name + arguments + rationale），不直接觸發 side effect
  - **Host contract layer**：host 註冊一份「誰可以呼叫什麼、在什麼條件下、副作用範圍多大」的授權合約
  - **Runtime core**：執行排程、攔截 host policy violation、emit structured event
  - **Permission-mediated tool system**：tool 帶有顯式 metadata（read-only / write / network / subprocess），runtime 比對 host policy 才放行
  - **Replay subsystem**：所有 execution trace 以 event log 形式儲存，host 可以隨時 replay 整段對話做 forensics
  - 程式碼已公開（論文附 https URL），可作為實作 reference

- **Insight（個人心得）**:
  Agentao 的設計哲學跟 master 的 `horo-agent` / Hermes-agent-lite downstream 路線 — 「保守減法 + host 控制權優先」 — 高度吻合。具體兩個可借鏡點：(1) 對「模型提出 vs runtime 執行」的二元切分，正好對應到 hermes-agent 現有的 tool dispatcher 邊界，未來若要做 audit envelope，可以直接套用 Agentao 的 structured event 格式；(2) threat model 倒推設計的順序（先列攻擊面，再設計 layer）比「先做 framework 再補 security patch」更乾淨 — 對 air-gapped 部署尤其關鍵，因為事後 patch 路徑幾乎不存在。