# GitHub 專案動態
- 檢查時間：2026-07-04
- 檢查對象：usestrix/strix、openai/codex-plugin-cc、JuliusBrussee/caveman、omnigent-ai/omnigent、cobusgreyling/loop-engineering

## 動態摘要

### usestrix/strix（AI 滲透測試工具）
- 類型：issue / release / commit
- 內容：最新 release 為 `v1.0.4`（2026-06-09），重點是讓 TUI 退出時直接 SIGKILL sandbox container，體感更俐落；最新 commit `302efed`（2026-07-03）新增 SARIF 2.1.0 emitter 給 CI / code-scanning 整合用，定位愈來愈像「能塞進企業 SDLC 的開源滲透測試工具」；同時今天冒出 #672（finding 函式把 asyncio event loop 卡住 20 秒）、#671（Windows Docker Desktop sandbox workspace materialization 失敗）、#670（numeric-looking id 被 `float("1e5230")` 解析成 inf）三個 bug，方向都偏整合與穩定性。
- 連結：https://github.com/usestrix/strix/issues/672

### openai/codex-plugin-cc（Codex ↔ Claude Code 橋接器）
- 類型：release / commit / issue
- 內容：最新 release 為 `v1.0.5`（2026-06-23），重點是新增 `/codex:transfer` 把 Claude Code session handoff 給 Codex；最新 commit `80c31f9`（2026-06-23）就是對應的版本號 bump；今天（2026-07-04）連開 PR #425（reap worker 死掉但沒回報結果的 ghost jobs）與 PR #424（修非英文 Windows 取消 dead pid），底下是 issue #423 在講同一個 Windows pid 殘留問題，看起來 plugin 在多語系 Windows 上還有不少 rough edge。
- 連結：https://github.com/openai/codex-plugin-cc/pull/425

### JuliusBrussee/caveman（Claude Code token-saver skill）
- 類型：release / commit / issue
- 內容：最新 release 為 `v1.9.1 — 65%, honestly`（2026-07-03），官方自稱是「honesty release」，把不同表面的節省數字攤平成一致的 65%，並把 install 行為鎖成 pinned + integrity 檢查；最新 commit `0d95a81`（2026-07-03）就是 sync SKILL.md；今天冒 issue #644，報告 SessionStart hook 從錯的路徑讀 SKILL.md 導致 fallback ruleset 永遠載入，另外 PR #643（diff-first rule）和 PR #642（smart intensity level）代表社群正在給 skill 加「聰明一點」與「先看 diff 再說」的能力。
- 連結：https://github.com/JuliusBrussee/caveman/issues/644

### omnigent-ai/omnigent（多 agent 編排 meta-harness）
- 類型：release / commit / pull request
- 內容：最新 release 為 `v0.4.0`（2026-07-03），是個密集釋出版本；最新 commit `3124850`（2026-07-03）修了 xAI / Grok / DeepSeek 的 top-level `reasoning_content` streaming，`ac8fe93` 跟 `b26f1cb` 則把 codex-native TUI observation 接到 harness-bench，並把 Keenable 加進 `web_search` 的工具後端；今天（2026-07-04）連開 PR #1948（MCP server 跨 spec 共用）、PR #1946（`~/.claude/.credentials.json` 綁進 sandbox）、PR #1945（`asyncio.wait` 之後取消 pending futures），整個專案本週的推進方向明顯在「強化 sandbox 邊界 + 統一 reasoning stream」。
- 連結：https://github.com/omnigent-ai/omnigent/pull/1948

### cobusgreyling/loop-engineering（AI coding agent 迴圈工程）
- 類型：release / commit / issue
- 內容：最新 release 為 `v1.5.0 — Community Tools Drop`（2026-06-30），合併七個社群 PR、新增三個工具，主打「一條命令全部試過」；最新 commit `015ad4a`（2026-07-03）收進 @50thycal 的量化 loop 失敗案例到 stories，`1681241` 是每日 triage 的自動 STATE.md 更新；今天冒出 issue #139，`loop-context --check` 對非法 numeric limit 默默放行、可能 fail open，PR #138 修 `loop-sync` 的 JSON 輸出旗標，PR #135 則補 `loop-init --tool` 對照表跟 Cursor 限制說明，方向在「讓 loop pattern 對一般開發者更安全、好上手」。
- 連結：https://github.com/cobusgreyling/loop-engineering/issues/139

## 重點觀察
- 今天五個專案有四個都在 7 月 3-4 日之間密集出 commit / PR，開源 AI agent 工具鏈的「週中衝刺」特徵很明顯，release cycle 正在往 1-2 週一發版收斂。
- 「sandbox / 邊界」是共同主題：strix 處理 Docker Desktop workspace materialization、omnigent 把 Claude credentials 綁進 sandbox、codex-plugin-cc 處理 Windows pid 殘留、loop-engineering 處理 `loop-context` fail open——大家都在收 runtime 安全債。
- Windows 與非英文環境的相容性是當前最大痛點：strix #671、codex-plugin-cc #423 + #424、caveman #644（SKILL.md 路徑問題）都集中在這個面，意味著「跨平台 dev tooling」仍是 AI agent 工具最容易被低估的成本。
- 「誠實行銷」開始出現：caveman v1.9.1 直接叫「65%, honestly」，把節省數字攤平；對照 loop-engineering 開始收集量化 loop 失敗故事——這個生態正在從「demo 漂亮」轉向「可驗證、可重現」。
- Trending top 3 跟 web search 選的 2 個剛好構成「agent 工具的上下游」：strix（測試端）、codex-plugin-cc（IDE 橋接）、caveman（skill 層）、omnigent（編排層）、loop-engineering（迴圈工程層）——五個拼起來大致就是一條 AI coding agent pipeline。
