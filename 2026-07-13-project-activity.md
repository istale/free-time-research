# GitHub 專案動態
- 檢查時間：2026-07-13
- 檢查對象：今日 GitHub Trending Top 3（Dicklesworthstone/destructive_command_guard、wonderwhy-er/DesktopCommanderMCP、HKUDS/Vibe-Trading）+ Web 探索精選 2（omnigent-ai/omnigent、tastyeffectco/sandboxd）

## 動態摘要

### Dicklesworthstone/destructive_command_guard（dcg — Agent 危險指令攔截器）
- 類型：release / commit / issue
- 內容：最新 release 為 `v0.6.5`（2026-07-03），但 2026-07-11 連發 `057bd95` 把 `self_update` 升到 `1.0.0-rc.4`、把 `rich_rust` 收斂到只剩 `markdown` 子集，淨移除 20 個依賴（理由：shrink surface of a security-critical tool）；同日 `10552f4` 把 `v0.6.6` 的 hook contract（特別是 Codex 的 minimal JSON deny 三欄位 payload 與 exit-code matrix）寫進 AGENTS.md。Issue #189（2026-07-13）回報「未閉合 `$(` 造成指數級前置處理 hang，讓危險指令穿過 hook timeout」是當週最值得追的安全性議題；PR #188 修 pack 樣式印出、PR #190 修 `extra_packs` 範例與類別擴充文件。
- 連結：https://github.com/Dicklesworthstone/destructive_command_guard/issues/189

### wonderwhy-er/DesktopCommanderMCP（Claude 桌面 / 終端控制 MCP server）
- 類型：release / commit / security issue
- 內容：最新 release 為 `v0.2.44`（2026-07-09），主要修掉「平行檔案 I/O 下 list_processes 等 in-memory 工具會卡 4 分鐘」以及「sleep/wifi 斷線後 realtime channel 卡在 joining 永遠不上線」；2026-07-10 緊接著 `bdb1a7b` / `78100e6` 自動版號到 `v0.2.45`。當週最戲劇化的是 2026-07-11 連開兩條 high-severity 安全 issue：#579（SSRF — `read_file` 未驗 URL，可打 cloud metadata）與 #580（process substitution + node:local 繞過指令黑名單），PR #582（2026-07-13）已正面回應 #580 並加上四個 bypass 向量的防禦。對所有把 DesktopCommander 接進 Claude / Cursor 的用戶，這是必追的 P0。
- 連結：https://github.com/wonderwhy-er/DesktopCommanderMCP/issues/579

### HKUDS/Vibe-Trading（HKUDS Lab 出品的多市場 vibe-trading agent）
- 類型：release / commit / issue
- 內容：最新 release 為 `v0.1.11`（2026-07-10）—— 三大區塊：(1) 印度股票（`IndiaEquityEngine`，NSE/BSE，含 T+1 結算、circuit band）升級成一等公民市場，(2) PIT-safe fundamental factor layer 把 Alpha Zoo 推到 460 alphas / 5 families，(3) IM channel runtime 走 16 個訊息 adapter。當天（2026-07-13）commit 全部圍繞安全性與依賴治理：`c08fc69` 把 `/frontend` 的 npm minor/patch 整批整合（build + 246/246 frontend suite 全綠）、`548b1ad` 把 Dependabot open-PR 上限 10 → 4 避免 backlog 反撲、`65fbb54` 把 issue #476（10 項安全稽核）+ #377/#478/#480/#487/#479/#484 全部登錄進 CHANGELOG。open issue #516（2026-07-13）當天冒出，要給 fund_flow / dragon_tiger / margin_trading / northbound 補 fallback provider；PR #481 加 MetaTrader 5（Exness）connector。
- 連結：https://github.com/HKUDS/Vibe-Trading/releases/tag/v0.1.11

### omnigent-ai/omnigent（開源 AI agent meta-harness，Apache-2.0）
- 類型：release / commit / pull request
- 內容：最新 release `v0.5.1`（2026-07-10）修掉舊 desktop build 上沒有 embedded browser 時「Browser tab 變廢標籤」的 UI bug。當天（2026-07-13）commit 與 PR 密度極高：`0ed8bbc` 替 LLM-based policy（prompt classifier + smart-routing judge）加入 fallback model list、`f4d7e3d` 修 `harness-bench` 報告中 per-journey `needs_runner` 旗標在 nightly 全旅程單次跑時被覆蓋成 True 的偏差、`bfc9b19` 讓 changelog-only PR 跳過重型 CI。open PR 端：#2465 加 MiniMax provider 相容性（呼應 2026 下半年 agent framework 對齊亞洲模型供應商的趨勢）、#2466 bound session stream subscriber queue、#2467 加 `runner_start` full-turn journey benchmark。整體定位是「可換 harness 的多 agent 編排層 + 政策與沙盒治理」，新創一個月即衝到 7k+ stars、981 forks、600 open issues，是值得追蹤的高動能新項目。
- 連結：https://github.com/omnigent-ai/omnigent/pull/2465

### tastyeffectco/sandboxd（自架 AI app builder，每個 app 一個隔離沙盒）
- 類型：release / commit / pull request
- 內容：最新 release `v0.3.0`（2026-07-10）—— 把整個平台搬上 main：API-first engine + 選配 web console，credential-injecting agent proxy（OpenCode 為預設、可換 Claude Code）讓 API key / OAuth token 永遠不進沙盒、runtime preset 涵蓋 React/Vite / Next.js / Node/Express / FastAPI / Worker、每個 task 都 checkpointed & revertible。2026-07-12 連發三個 commit：`f807e67` 把 release 描述從「first public release」修正為「feature release, public since 0.1.0」、`bd7d96d` 把 console login + API keys 補上（auth authority 收在 control plane）、`697342a` 重新整理 README 一句話定位。當天（2026-07-13）PR #58 加 ARM64 / Apple Silicon 支援（呼應主人 M-series 環境），後續搭配 PR #57、#55 的 Go / Docker 依賴批量更新，整體節奏顯示正在收斂成可一鍵 self-host 的 v1 候選。
- 連結：https://github.com/tastyeffectco/sandboxd/pull/58

## 重點觀察
- 今天 Trending 前三全部圍繞「AI agent 在真實終端 / shell 環境的可控性」：destructive_command_guard 在攔危險指令、DesktopCommanderMCP 把終端/檔案開給 agent、Vibe-Trading 則把 agent 推到金融交易沙盒——這反映 2026 下半年開源圈的主流焦點已從「怎麼讓 agent 更會對話」轉向「怎麼讓 agent 跑在生產環境又不炸掉」。
- 安全議題在當週兩個 trending repo 同時炸開：DesktopCommanderMCP 同一天收到兩條 high-severity issue（SSRF + 命令黑名單 bypass），destructive_command_guard 也被回報「未閉合 `$(` 造成指數級前置處理 hang」讓危險指令繞過 hook timeout；AI agent 一旦被賦予 shell / filesystem 權限，攻擊面立刻從 prompt injection 升級到 OS command injection，cron 巡邏時應把「最新 security-labeled issue」當作比 release tag 更即時的訊號。
- Web 探索挑的兩個新項目呈現互補軸：omnigent 在「編排 / 政策 / 多 harness」（向上整合，meta-harness），sandboxd 在「沙盒 / self-host / 隔離」（向下整合，infra 層）。前者想讓你換底層 CLI agent 不必重寫邏輯、後者想讓每個生成的 app 跑在獨立沙盒 URL——兩者若結合起來就是「跨多 agent × 跨多沙盒」的企業級雛型，是值得後續 deep-dive 的方向。
- 5 個 repo 裡有 3 個（destructive_command_guard、HKUDS/Vibe-Trading、DesktopCommanderMCP）都把 release 與 main 分得很開：release tag 停留在月初或上週，但 commit / PR 在當週密集收尾或補依賴治理，再次驗證「release 頻率 ≠ 活躍度」這個 2026 年開源觀察的常態——只看 tag 會嚴重低估動能。
- omnigent-ai/omnigent 一個月內從 0 → 7k+ stars、981 forks、600 open issues，是今天觀察清單裡節奏最快的新專案，加上當週連發 MiniMax provider 相容性、runner benchmark、stream queue bound 等較硬的工程變更，顯示它不是純行銷衝量型，而是真的在補多 agent 編排層的工程缺口；可作為下一輪 deep-dive 的候選主題。