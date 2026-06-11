# The AI engineering stack we built internally — on the platform we ship
- 原始連結：<https://blog.cloudflare.com/internal-ai-engineering-stack/>
- 閱讀時間：2026-06-11

## 摘要
這次我讀的是 Cloudflare 的工程文章：[The AI engineering stack we built internally — on the platform we ship](https://blog.cloudflare.com/internal-ai-engineering-stack/)。它不是在講單一 AI 功能，而是在拆解一套真正能讓數千名工程師日常使用的 AI 工程基礎設施：用 Cloudflare Access 做 Zero Trust 驗證、用 AI Gateway 做統一路由與成本控管、用 Workers AI 跑開源模型、用 MCP Server Portal 整合內部工具，最後再把 Code Review、知識圖譜、沙箱執行與長流程工作流串成完整平台。

文章最有價值的地方，是它把「AI 導入工程團隊」從 prompt 技巧拉回平台設計。Cloudflare 在最近 30 天裡，有 93% 的 R&D 組織成員使用自建 AI coding stack，累積 4,795 萬次 AI 請求，並把 2,413.7 億 tokens 經過 AI Gateway 路由。這些數字背後不是單靠模型能力，而是靠一個統一入口：工程師只要執行一次 `opencode auth login`，後面模型供應商、MCP server、agent、權限與命令設定都由 discovery endpoint 和 proxy Worker 自動下發，避免 API key 落在個人機器上。

我特別在意兩個設計。第一，所有 LLM 流量都先經過 proxy Worker，再轉進 AI Gateway，這讓 Cloudflare 可以後補 per-user attribution、模型目錄、權限控管與 Zero Data Retention，而不用逐一改 client 設定。第二，他們把「知識」和「治理」一起納進系統：Backstage 形成 16K+ entity knowledge graph，AGENTS.md 幫 agent 建立 repo 在地上下文，AI Code Reviewer 與 Engineering Codex 則負責把品質與規範壓回開發流程。意思很明白：真正可擴張的 agent system，不是多接幾個模型，而是把認證、路由、觀測、知識與 enforcement 全部產品化。

## 3W1H 分析
- What（做了什麼/主題）: Cloudflare 公開他們內部 AI engineering stack，說明如何把 AI coding assistants、MCP tools、模型路由、沙箱執行與 code review 串成公司級工程平台。
- Why（為什麼重要）: 多數團隊卡在「有人會用 AI」，但很少真的解決安全、權限、模型治理、知識注入與規模化 rollout。這篇文章示範了怎麼把 AI 從個人玩具變成組織級基礎設施。
- How（如何運作/實作）: 以 Cloudflare Access 做驗證，proxy Worker 作為唯一入口，AI Gateway 負責供應商路由與成本/資料政策，Workers AI 承接部分推論，MCP Portal 聚合內部工具，再加上 Backstage、AGENTS.md、AI Code Reviewer 與 Workflows 形成完整閉環。
- Insight（個人心得）: 真是的，這篇最值得抄的不是某個 prompt，而是「先做控制平面，再談 agent 體驗」。只要入口統一，後面不管要換模型、補權限、加審計、做匿名計費，都還有餘裕；入口分裂的話，後面每次擴充都會變成災難。
