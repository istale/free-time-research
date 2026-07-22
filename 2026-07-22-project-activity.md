# GitHub 專案動態
- 檢查時間：2026-07-22
- 檢查對象：jcode、llmfit、AstrBot、QwenLM/qwen-code、Canner/WrenAI

## 動態摘要

### 1jehuang/jcode
- 類型：release / commit / issue
- 內容：最新 release 為 `v0.54.4`（2026-07-20）；主分支最近三個 commit 全落在 7/20–7/21，包含「Rename Flagship plan display to Solo」、「Give Ultra access to Fable 5」、以及 `fix(models): exclude unstable Nemotron Super route`；今天連開三個 issue #543 / #542 / #541，集中在 OpenAI 嚴格 schema / model picker / context_window 設定三條相互關聯的相容性回饋，顯示社群正在密集驗收新版本。
- 連結：https://github.com/1jehuang/jcode/issues/543

### AlexsJones/llmfit
- 類型：release / commit / pull request
- 內容：最新 release 為 `v1.1.6`（2026-07-21）；今天（2026-07-22）連續 merge 兩個社群提交的 GPU benchmark 數據（NVIDIA GeForce RTX 2070 Super、RTX 5070），代表專案以「眾包硬體資料」為主要擴張動能；目前 open 的 #786、#784（release 1.1.7 PR）、#767（http 1.4.1 → 1.4.2）都在排隊，下一個 patch 版本應該很快。
- 連結：https://github.com/AlexsJones/llmfit/pull/786

### AstrBotDevs/AstrBot
- 類型：release / commit / issue + pull request
- 內容：最新 release 為 `v4.26.7`（2026-07-18）；今天最新的 commit 是 `5425470`「fix: Faiss read/write failure on Windows with non-ASCII user paths (reopened)」（#8323），顯示 Windows + 非 ASCII 路徑這個 issue 已經修了又回鍋；活躍的 PR #9346 提出「orthogonal context config + /compact command」，是一次比較大幅的 context 介面重構，社群討論度明顯偏高。
- 連結：https://github.com/AstrBotDevs/AstrBot/pull/9346

### QwenLM/qwen-code
- 類型：release / commit / pull request
- 內容：最新 release 為 `v0.20.1`（2026-07-21），Qwen 官方 coding agent CLI；今天（2026-07-22）一連收進 #7475（triage signature 注入 model name）、#7474（mobile-mcp 不再納入 core 版本號）、#7477（embedded web-shell UX 打磨）三條 fix，PR 編號已經推進到 #7484，節奏非常密集；同期 PR #7482「feat(autofix): stop a PR that fails to push for N rounds」說明 autofix 流程已具備明確的失敗熔斷機制。
- 連結：https://github.com/QwenLM/qwen-code/pull/7484

### Canner/WrenAI
- 類型：release / commit / pull request
- 內容：最新 release 為 `wren-v0.13.1`（2026-07-21）；今天的三個 commit 全部圍繞「strip trailing semicolon before sql/dry_run」這個跨 dialect（Spark / Redshift / MySQL）的一致性修補，代表 SQL 安全淨化邏輯正被同步推上各個 adapter；同期 PR #2558「fix(langchain): coerce None in format_dry_plan_content」處理 LLM 規劃結果為 null 的邊界，呼應近期對「LLM 規劃 → SQL 執行」可靠性的關注。
- 連結：https://github.com/Canner/WrenAI/pull/2560

## 重點觀察
- 五個 repo 全部都是近 48 小時內有 release 或密集 commit 的活躍項目，今天的樣本明顯偏向「agent / coding CLI / LLM ↔ SQL / LLM 相容性」這條主軸，跟這兩週業界在 coding agent 上的加速一致。
- jcode、QwenLM/qwen-code 都還在快速疊代產品名 / provider 抽象層，今天的 issue / PR 多半在處理 OpenAI strict schema、model picker、autofix 等「真實使用面」的摩擦，是少數能直接反映終端使用者痛點的訊號源。
- llmfit 的擴張模式很特別：靠社群提交不同 GPU 的 benchmark 資料累積 coverage，本質上是一個「眾包硬體地圖」，相較一般模型/工具專案，觀察重點應放在資料多樣性而不是 release 速度。
- WrenAI 的修補集中在 SQL 語法淨化（trailing semicolon）與 LangChain 規劃結果的 None 處理，說明 text-to-SQL 領域的成熟度訊號正在從「能不能跑」轉向「能不能安全落地」。
- AstrBot 是這批裡唯一一個有 Windows + 非 ASCII 路徑這類「作業系統角落 bug」反覆回鍋的專案，提醒在評估 agent 框架時，跨平台 / 跨 locale 的 robustness 仍然是真實部署風險。
