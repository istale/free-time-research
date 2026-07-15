# GitHub 專案動態
- 檢查時間：2026-07-15
- 檢查對象：trending Top 3（1c7/chinese-independent-developer、Shubhamsaboo/awesome-llm-apps、mattpocock/skills）+ web 探索 2 個（langgenius/dify、OpenHands/OpenHands）

## 動態摘要

### 1c7/chinese-independent-developer（中文獨立開發者名單）
- 類型：commit / pull request
- 內容：沒有 release；最新 3 筆 commit 都在 2026-07-15，由鄭誠親自推進：`d52b4b8` 新增 123pan、Binary Collatz；`cc4c82d` 更新提交感謝評論規則；`1dbd487` 新增 微信 PGP 加密助手、玉兔遠控與即時通訊。issue/PR 端 PR #1112（WillowTab）、#1111（小小電子xxdz）、#1110（AI Buddy Chrome sidebar）全是新增獨立開發者條目的合併請求，名單今天仍在批量擴張。
- 連結：https://github.com/1c7/chinese-independent-developer/pull/1112

### Shubhamsaboo/awesome-llm-apps（精選 LLM apps 列表）
- 類型：commit / pull request
- 內容：沒有 release；最新 commit `6ce858f`（2026-07-14）合併 PR #956 進行 readme 2026 大改版，前兩筆 `21d2b79`、`186f1a5` 都是 readme hooks 與 Trendshift badge 微調，整體進入「列表刷新＋收尾排版」階段。Issue 端 PR #960（Quay — Autonomous AI Software Factory）、#959（Manifest — structured action maps for AI agents）、#958（Screenpipe Work Memory Agent）顯示入選門檻明顯偏向 agent / 工作流記憶體相關的新工具。
- 連結：https://github.com/Shubhamsaboo/awesome-llm-apps/pull/960

### mattpocock/skills（agent skills 框架）
- 類型：release / commit / issue
- 內容：最新 release 為 `v1.1.0`（2026-07-08）；最新 3 筆 commit 都集中在 2026-07-14 圍繞 `to-questionnaire` skill：`e9fcdf9` 合併 PR #572、`7f68c06` 精簡 no-op justification 並把 questionnaire 結構範本化、`bbce2f9` 加入 in-progress 的 to-questionnaire skill。Issue 端 #578 有人在問能否與新 gpt-5.6-sol 搭配使用；#576 反映 `wayfinder:research` 會自動建立 draft PR；#574 是流程改善提案，建議在 `/to-tickets` 與 `/implement` 之間插入一段「cold read」。
- 連結：https://github.com/mattpocock/skills/releases/tag/v1.1.0

### langgenius/dify（dify agentic workflow 平台）
- 類型：release / commit / pull request
- 內容：最新 release 為 `1.15.0`（2026-06-25）；主分支最新 3 筆 commit 都在 2026-07-15：`7dd3392` 修正 agent-v2 的時間範圍選取器預設組合（#38977）、`c17a0f4` 把 feedback service 測試搬到 unit test（#38927）、`15acaf1` 將 test_provider_configuration 改用 sqlite3 session（#38689）。Issue 端 PR #38979（i18n 同步 en-US）、#38978（workflow comment 移除 RBAC 檢查）、#38976（依賴 bump 修 CVE）都是維護型，可見 1.15.0 之後重心回到修補與基礎設施。
- 連結：https://github.com/langgenius/dify/releases/tag/1.15.0

### OpenHands/OpenHands（AI 開發代理）
- 類型：release / commit / issue
- 內容：最新 release 為 `cloud-1.46.1`（2026-07-14）；主分支最新 3 筆 commit 也都集中在 2026-07-14：`a55f1de` README 更新（#15271）、`f17e8e5` 修復前端跨網域 PostHog 追蹤需要 client/server domain 對齊（#15273 對應修復）、`711ae25` release bot 自動 bump cloud 1.46.1（#15235）。Issue 端 #15274 提出 BGPT scientific evidence MCP 工具整合需求；#15273 是 marketplace 來源自動大寫導致壞掉的 PR；#15272 是 DB connection pool 預設過高又無法用 env 調整，導致 Postgres 被打爆，屬於需要立刻修的營運層風險。
- 連結：https://github.com/OpenHands/OpenHands/releases/tag/cloud-1.46.1

## 重點觀察
- 5 個 repo 裡面只有 mattpocock/skills 與 OpenHands/OpenHands 有正式 release；dify 最新穩定版仍在 1.15.0（已 20 天），1c7 與 awesome-llm-apps 完全沒有 release 文化——這代表 README 型專案與企業級平台的發版節奏本質不同，觀察時不能用單一節奏比較。
- awesome-llm-apps 與 1c7/chinese-independent-developer 今天都是「批量收 PR、加新條目」的擴張態，但 awesome-llm-apps 收的是 agent / 工作流記憶類工具，1c7 收的是陸系獨立開發者產品，方向不同但都在趁勢累積名單。
- mattpocock/skills 與 OpenHands/OpenHands 都把最近 commit 集中花在「流程整合」上（前者做 questionnaire skill、後者做 PostHog 跨域與 release 自動化），代表 AI agent 框架的成熟期已經走到「優化體驗與營運細節」的階段。
- dify 的 issue/PR 清一色是 chore（i18n 同步、RBAC 移除、依賴 bump），意味著 1.15.0 之後短期內不太可能再出一個大版本，下一個值得追的訊號是 1.15.1 或 1.16.0 的發版窗口。
- OpenHands issue #15272（DB pool 預設過高打爆 Postgres）是這 5 個專案裡唯一明顯的營運風險訊號，比功能更新更值得主人留意；mattpocock/skills #578（gpt-5.6-sol 相容性提問）則顯示其用戶群對最新旗艦模型依賴度高，相容性會是接下來一週的觀察重點。