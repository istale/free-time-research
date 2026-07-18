# GitHub 專案動態
- 檢查時間：2026-07-18
- 檢查對象：codecrafters-io/build-your-own-x、PostHog/posthog、HenryNdubuaku/maths-cs-ai-compendium、ratel-ai/ratel、Skyvern-AI/rustwright（Trending Top 3 + Web 探索 2）

## 動態摘要

### codecrafters-io/build-your-own-x（codecrafters-io/build-your-own-x，Trending #1）
- 類型：commit / pull request
- 內容：沒有 published release（教學型 curated list，本身不打 release）。最新 3 條 commit：`aa17439` 2026-07-14 Merge PR #1844「Fix incorrect anchor link for AI model section」、`b916354` 2026-07-14「Fix incorrect anchor link for AI model section」、`264b454` 2026-06-25 Merge PR #1812「add-build-deep-learning-from-scratch」——主軸是 anchor 修正 + 把「build deep learning from scratch」收進 AI Model 段落。今天（2026-07-18）新增 PR #1847「Fix quote formatting by removing HTML entities」、PR #1845「Add digiwleea to Processor」、PR #1842「Add zero2robot (build a robot policy from scratch) under AI Model」——三條全部是內容/格式修正，沒有結構性事件，但能看出社群在持續把 RL / robotics policy 類別補進清單。
- 連結：https://github.com/codecrafters-io/build-your-own-x/pull/1847

### PostHog/posthog（PostHog/posthog，Trending #2）
- 類型：release / commit / pull request
- 內容：最新 release 為 `posthog-cli/v0.8.4`（2026-07-16）。最新 3 條 commit 全部是今天（2026-07-18）：`3545e4f`「feat(signals): autoresearch fix loop for MCP scout category reports (#72110)」、`f1c3d45`「feat(logs): generate metrics from logs at ingestion (#72104)」、`3eda4bf`「fix(flags): key the EU flags mirror with a sentinel cache key (#72042)」——訊號面非常一致：signals→MCP、logs→metrics、flags→EU cache，全部是「觀測 / 監控自動化」這條軸線。今天 open PR：#72117「chore(metrics): add product guardrails doc」、#72116「feat(metrics): surface trace exemplars in investigations」、#72115「fix(flags): retry last-called sync on ClickHouseAtCapacity」——三條都圍繞 metrics / flags 的可靠性細修，product analytics 平台正在把 AI 自動調查 (autoresearch) 與 ClickHouse 後端穩定化同步推進。
- 連結：https://github.com/PostHog/posthog/pull/72110

### HenryNdubuaku/maths-cs-ai-compendium（HenryNdubuaku/maths-cs-ai-compendium，Trending #3）
- 類型：commit / pull request / issue
- 內容：沒有 published release（教學書形式，不打 release）。最新 3 條 commit：`9e3c096` 2026-07-16「fix: rename chapter dirs with colons for Windows compatibility (closes #7)」、`0f379cc` 2026-07-15 Merge PR #13、 `f35f31a` 2026-07-15 Merge PR #15——主軸是 cross-platform path 相容性 + 社群 PR 收編。今天（2026-07-17）新增 PR #21「Refactor markdown for clarity and consistency across chapters」、PR #20「feat: run Python code in the browser via Pyodide」、Issue #19「hsd」（疑似測試 issue）——`Pyodide in-browser` 是這本教學書的下一個功能亮點，把 Python 範例從本地搬到瀏覽器直接跑，對讀者體驗是大升級。
- 連結：https://github.com/HenryNdubuaku/maths-cs-ai-compendium/pull/20

### ratel-ai/ratel（ratel-ai/ratel，Web 探索）
- 類型：release / commit / pull request
- 內容：最新 release 為 `sdk-py-v0.4.2`（2026-07-11）；但最新 3 條 commit 都是 7-16 / 7-17：`d46cf44` 2026-07-17「release: cut 0.5.0-rc.2 for core, sdk-ts, sdk-py (embedding perf, concurrent loads, typed errors)」、`588c682` 2026-07-17「Rat 385/conf embed bug performance fixes (#118)」、`838410b` 2026-07-16「release: cut 0.5.0-rc.1 for core, sdk-ts, sdk-py (configurable embedding models)」——今天（2026-07-18 GitHub 上 7-17 為最新 PR/issue 活躍日）的 open PR 全由單一 maintainer `rstagi` 連發：#119「feat: add @ratel-ai/mastra-adapter (OSI-16)」、#117「Extract @ratel-ai/ai-sdk-adapter (Phase 3, OSI-15)」、#116「feat(sdk): adapter conformance testkit (@ratel-ai/sdk/testkit)」——三條全部是「adapter 套件 + 對應 conformance testkit」的標準切版節奏（每個新 adapter 都附 OSI 編號與 testkit），呼應他們「context engineering / skills 標準」的主張；0.5.0-rc 路線圖幾乎已經成型。
- 連結：https://github.com/ratel-ai/ratel/pull/119

### Skyvern-AI/rustwright（Skyvern-AI/rustwright，Web 探索）
- 類型：commit / pull request
- 內容：沒有 published release（早期專案，infra library 通常 0.x 不打 GitHub release）。最新 3 條 commit：`1c0aa85` 2026-07-16「Benchmark case breadth: 100 live-validated cases + 7 parity-gap repros (#92)」、`dc6c2eb` 2026-07-16「Add a persistent agent CLI (accessibility-snapshot refs) (#91)」、`56724ad` 2026-07-16「MCP server: expose browser automation as tools for MCP-compatible agents (#84)」——三條全部落在同一天 (2026-07-16)，節奏是把 Skyvern 的 browser automation 同時鋪進「benchmark / agent CLI / MCP server」三個面。今天（2026-07-18）open PR：#96「Give remote-CDP actionability probes the full remaining action budget」、#93「mcp: attach to a remote browser over CDP」、#43「Bump @napi-rs/cli from 2.18.4 to 3.7.3 in /node」——`remote CDP` 是 Skyvern 接入既有 browser fleet 的關鍵介面，PR #84 + PR #93 連起來看就是「本地自動化」往「遠端 browser farm」擴張。
- 連結：https://github.com/Skyvern-AI/rustwright/pull/96

## 重點觀察
- 今天 Trending 換血幅度比昨天大：昨天 (2026-07-17) 是 `apache/ossie + Nutlope/hallmark + OpenCut-app/OpenCut`，今天直接換成 `codecrafters-io/build-your-own-x + PostHog/posthog + HenryNdubuaku/maths-cs-ai-compendium`，其中兩個（`build-your-own-x`、`maths-cs-ai-compendium`）是教學/curated list 類，沒有 release 只有文件型 commit，跟過去一週清一色 AI agent repo 的節奏明顯不同——可能反映 GitHub trending 演算法在週末偏向「學習資源 / 文件型」的週期性偏移。
- `PostHog/posthog` 是今天 5 個 repo 裡**唯一主分支有當天 3 條 commit 全落到 2026-07-18** 的專案，commit 訊號密度明顯壓過其餘 4 個；而且三條 commit 都跟「signals / logs / flags」有關，呼應 PostHog 把 AI autoresearch 接入 product analytics 平台的產品方向。
- Web 探索這次挑了 `ratel-ai/ratel`（在準備 0.5.0-rc，每個新 adapter 都附 conformance testkit + OSI 編號）與 `Skyvern-AI/rustwright`（Playwright 的 Rust 重寫，現正往 MCP server + 遠端 CDP 鋪路）——前者是「agent 中介層標準化」的代表、後者是「browser automation infra 重寫」的代表，剛好覆蓋了 2026 年 agent stack 的兩個關鍵支柱。
- `ratel-ai/ratel` 與 `Skyvern-AI/rustwright` 三個 open PR 的 maintainer 都是**單一帳號一口氣連發**（`rstagi` 與 `suchintan` / `LawyZheng`），節奏都是「一次鋪好 adapter / remote / benchmark 一整面」，屬於 0.x 早期但 maintainer 火力集中的典型樣態，跟 PostHog 多人協作的高密度小幅修補形成對照。
- 跨 5 個 repo 共同的橫向訊號：5 個裡有 3 個沒有 published release（教學 / infra library / curated list），剩下的 2 個 release 也都不是今天發；今天所有動能都落在 commit + PR 層級，並且「adapter / testkit / MCP / context engineering / remote CDP」這些詞在 PR 標題裡反覆出現，呼應目前 AI 工程化正在從「單體框架」往「可插拔的中介層 + 標準化測試套件 + browser-as-a-tool」演進。