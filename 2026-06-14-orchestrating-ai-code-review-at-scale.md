# Orchestrating AI Code Review at scale
- 原始連結：https://blog.cloudflare.com/ai-code-review/
- 閱讀時間：2026-06-14

## 摘要

我今天讀的是 Cloudflare 的技術文章 [Orchestrating AI Code Review at scale](https://blog.cloudflare.com/ai-code-review/)。這篇最有價值的點，不是單純展示「AI 幫忙審 PR」，而是把 AI code review 當成一個可營運、可擴充、可控成本的 CI 系統來設計。

Cloudflare 放棄了「把整個 diff 丟給單一大模型」的做法，改成 coordinator 加上多個專職 reviewer 的編排架構。不同 reviewer 分工處理安全、效能、文件、工程規範、release 與 AGENTS.md 等面向，最後再由 coordinator 去重、判斷嚴重度，輸出單一結構化 review。這樣做的核心優勢是把 agent 的職責切窄，降低泛用 prompt 帶來的雜訊與幻覺。

在工程設計上，他們把整套流程做成 plugin system。每個 plugin 只透過受控的 context API 註冊 agent、AI provider、prompt 區塊與權限，而不是直接互相耦合。這讓 GitLab、Cloudflare AI Gateway、內部 Codex 規範與遙測模組可以獨立演進，對大規模多 repo 場景特別重要。

我覺得另一個很實際的點，是他們把「成本與速度」直接納入架構。依照 PR 變更量分成 trivial、lite、full 三種 tier，小改動只跑少量 reviewer，大改動才開滿全部 specialist。文章裡提到平均每次 review 約 0.98 美元、中位數耗時約 3 分 39 秒，而且透過 prompt cache 把命中率做到了 85.7%，代表真正能進生產的關鍵不是模型多強，而是 orchestration、cache 與分級策略有沒有設計好。

最後，Cloudflare 刻意把產出控制在低噪訊、高訊號。他們總 findings 很高，但平均每個 review 約 1.2 個 finding，背後靠的是明確寫出「不要抓什麼」。這提醒我：AI reviewer 最難的從來不是多提意見，而是知道哪些意見不該提。

## 3W1H 分析
- What（做了什麼/主題）:
  Cloudflare 介紹他們如何把 AI code review 做成 CI-native 的多代理審查系統，並實際部署到大量 merge request 上。
- Why（為什麼重要）:
  多數 AI review demo 都停留在「可以評論程式碼」，但要真正放進工程流程，必須同時解決準確率、噪訊、可觀測性、成本與擴充性。這篇文章展示了從 demo 到 production 的關鍵差異。
- How（如何運作/實作）:
  以 OpenCode 為底層，外層用 plugin 架構組合 GitLab、AI Gateway、規範檢查與遙測能力；執行時由 coordinator 按風險 tier 啟動不同 specialist reviewer，最後整合成單一 review 結果，並搭配 prompt cache 控制成本。
- Insight（個人心得）:
  這篇最值得抄的不是 prompt 文案，而是「系統設計觀」。把 agent 分工切細、把插件邊界切清楚、把成本與訊號品質當一級指標，這比追求一個萬能 reviewer 更務實。對任何想把 agent 放進正式開發流程的人，這篇都很有參考價值。
