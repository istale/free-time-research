# Orchestrating AI Code Review at Scale
- 原始連結：https://blog.cloudflare.com/ai-code-review/
- 閱讀時間：2026-06-11

## 摘要

Cloudflare 在 CI 流程中引入 AI code review，但他們發現傳統的「單一大 prompt 叫模型多想」方式雜訊太多、成效不穩。於是他們改用多專職 reviewer 的協作架構：

- **協調器（Orchestrator）**：負責分配任務、統合結果
- **最多 7 個專職 Reviewer**：安全、效能、文件、規範、錯誤處理、測試覆蓋、依賴管理等，各自有窄範圍的檢查清單
- **最終輸出**：去重 + 分級，輸出單一結構化的 review 報告

技術實作細節：
- Plugin 架構：每個 reviewer 是獨立的 plugin，方便擴展
- JSONL 串流：即時回饋進度
- 心跳日誌：追蹤每個步驟的執行狀態
- Token 截斷 + 重試機制：避免少數步驟超時卡住整個流程

## 3W1H 分析

- **What（做了什麼/主題）**：在 CI/CD pipeline 中规模化部署 AI code review
- **Why（為什麼重要）**：傳統單一 prompt 方式雜訊多、準確度不穩，難以在生產環境實用
- **How（如何運作/實作）**：用協調器 + 多專職 reviewer（最多 7 個），各自職責明確，最後由協調器整合輸出
- **Insight（個人心得）**：
  1. 真正有效的不是「叫模型多想一點」，而是把 reviewer 職責切窄，連「不要抓什麼」都寫清楚
  2. 工程落地比 prompt 更重要——可觀測性（心跳日誌、JSONL 串流）和穩定性（截斷重試）才是進生產的關鍵
  3. 這種架構很適合拿來思考 agent 編排：邊界清楚的小代理 + coordinator 整合，勝過追求萬能代理