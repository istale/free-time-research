# Your agent needs a computer, not a container — introducing @cloudflare/computer
- 原始連結：https://blog.cloudflare.com/cloudflare-computer/
- 閱讀時間：2026-08-04
- 作者：Matt Carey、Aron Carroll（Cloudflare）

## 摘要

Cloudflare 在「Agents Week」正式開源 `@cloudflare/computer`，一套 open-source agent runtime，主張「與其給 agent 一個 container，不如給它一台自己的電腦」。

**產業壓力：container 撐不住 agent 規模**
- 雲端 hyperscaler 的 container 總量根本不夠應付「每個使用者都給自家 agent 一個 container」這種規模；業界對 CPU compute 的需求已出現「desperate, panicked」的供需缺口。
- Coding agent 的典型工作流（filesystem、shell、tools、跑 code、測試）天生就是「給一台電腦」的形狀，container 只是其中一個片段。

**核心抽象：workspace + 多元執行 backend**
- **Workspace**：以 SQLite 為底的虛擬檔案系統，可從 cloud storage、git repo 或自訂來源 priming；提供 `node:fs` 相容 wrapper，第三方 JS lib 可直接套用。
- **兩種 runtime（皆 open-source 並由 `@cloudflare/computer` 提供）**：
  1. **Isolate backend**：用 [`just-bash.dev`](https://justbash.dev/) 把 shell code 翻譯成 JS，跑在 dynamic worker；快、便宜、檔案系統直接走 worker binding。
  2. **Container backend**：用 [Cloudflare Containers](https://developers.cloudflare.com/containers/) 給完整 Linux；透過 FUSE mount 同步檔案，container 看到的檔案變更會回寫 workspace。
- 兩個 backend 共用同一份檔案系統；agent 可用 `backend` 參數決定每個 task 走哪一條路。

**安全 / 可觀測性**
- 所有 read/write/edit/exec 操作都 gate、audit、observe，可細粒度設定 agent 允許做什麼，留下清楚的 paper trail。

**目標 KPI**
- Cloudflare 自家目標：agent 工作流中「container 處理的比例 < 10%」，純 isolate 就能撐住 JS app 建置 / 測試 / deploy、客製化文件生成、瀏覽器自動化等任務。
- 早期測試顯示 frontier model 已經很會自己挑 backend，需要完整 Linux 的情境才會 fallback 到 container。

**為什麼放在 Durable Object 上跑**
- Cloudflare 的架構選擇：harness（agent loop）跑在 Durable Object（isolate），container 是 attach 的工具，閒置時 isolate 可休眠、需要時再 attach container。

## 3W1H 分析

- **What（做了什麼 / 主題）**:
  `@cloudflare/computer` 是 Cloudflare Agents Week 的主打 open-source 釋出，提供一個「workspace + 多 backend」的 agent runtime 抽象：把 isolate 與 container 兩種 compute primitive 收進單一 SDK，讓 agent 在 isolate 跑得快、在 container 跑得深，兩者共用同一份 SQLite-backed virtual filesystem，並提供 `node:fs` 相容 wrapper、AI SDK 工具組（read/write/edit/ls/exec）。

- **Why（為什麼重要）**:
  container-based agent runtime 在數千萬到數十億等級 concurrent agent 時會撞上全球 CPU 算力天花板。Cloudflare 用 10 年押注 isolate + Durable Object 的橫向擴展能力，想把「container 是預設」這條路換成「container 是 fallback」，讓 agent 規模化在經濟上可行——這對正在做 agent harness / runtime 的人來說，是一條直接可借鏡的設計主軸。

- **How（如何運作 / 實作）**:
  - workspace 透過 SQLite 維護 virtual filesystem，populate 自 S3 相容 storage / git / 自訂來源
  - isolate backend 用 `just-bash` 把 shell 翻成 JS、在 dynamic worker 上執行；container backend 透過 FUSE mount 讓 Linux process 看到 workspace
  - `exec(string, options)` 是統一介面；AI SDK toolkit 把 `backend` 參數暴露給 LLM，讓 model 自己決定哪條路
  - 所有操作 gated / audited / observed，方便除錯、合規、事故回放
  - production path：harness 在 Durable Object（isolate），container 按需 attach、用完就 hibernable

- **Insight（個人心得）**:
  主人正在推 hermes-agent-lite 的 air-gapped / enterprise-lite 路線，這篇文章點出一個值得抄進去的設計原則：**「default-fast, fallback-heavy」**——不是每個 agent 動作都需要重型 compute，把 isolate 當 fast path、container 當例外，能在保持 agent 能力完整的前提下，把單位成本壓到接近 serverless。這跟主人一貫的「保守減法 + 端到端驗證」哲學一致：對 runtime 結構做精簡，而不是寫新抽象把既有穩定層吞掉。另外，`just-bash` 把 shell 翻 JS 的招數很值得研究——若 Lite 版要做 sandboxed tool execution，這可能比直接拉 container 輕很多。