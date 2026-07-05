# GitHub 專案動態
- 檢查時間：2026-07-05
- 檢查對象：openai/codex-plugin-cc、JuliusBrussee/caveman、alibaba/page-agent、ChromeDevTools/chrome-devtools-mcp、huggingface/speech-to-speech

## 動態摘要

### openai/codex-plugin-cc（Codex ↔ Claude Code 橋接器）
- 類型：release / pull request / commit
- 內容：最新 release 仍為 `v1.0.5`（2026-06-23），主分支最新 commit `80c31f9`（2026-06-23）也就是這個 version bump；自 1.0.5 之後 main 沒新 commit，但今天（2026-07-05）連開兩個 PR：#436 修 review gate README anchor（順手加 regression assertion 防 stale anchor）、#435 解掉 background job zombie state（`listJobs()` 加 30 分鐘 auto-GC、`--fresh` 加 `clearTerminalJobs()`），整個 plugin 現在走 PR-driven 而非直接 main commit。
- 連結：https://github.com/openai/codex-plugin-cc/pull/435

### JuliusBrussee/caveman（Claude Code token-saver skill）
- 類型：release / commit / pull request
- 內容：最新 release `v1.9.1 — 65%, honestly`（2026-07-03），主分支最新 commit `0d95a81`（2026-07-03）sync SKILL.md 副本；前一個 commit `e2c09c9` 就是把 savings claim 攤平成一致的 65% 並 bump PINNED_REF v1.9.0 → v1.9.1、`dc95e91` 是一次性 Skill + Readme 更新；今天（2026-07-05）出現 PR #649「Fix caveman-stats blank rendering and Portuguese defaulting」——前者解掉 VSCode extension 不渲染 `decision: 'block'` reason 導致 stats 整片空白的問題（改寫到 stderr + exit code 2），後者把語言偵測預設從「Portuguese first」改成「English first」，代表 prompt-injection 風險面也在收。
- 連結：https://github.com/JuliusBrussee/caveman/pull/649

### alibaba/page-agent（瀏覽器內 GUI agent）
- 類型：release / commit / pull request
- 內容：最新 release `🌟v1.11.0`（2026-07-03）附 `page-agent-ext-1.11.0-chrome.zip` Chrome extension asset，主分支最新 commit `68245e8`（2026-07-03）就是 docs CHANGELOG，前一個 `f738310` 直接 bump version、`f8af560` 移除 legacy motion-css（#583，已知 performance 問題）；今天（2026-07-05）開 PR #585「fix React re-render disposal race and pass AbortSignal to hooks」處理兩個 issue：#570（MCP 任務啟動後 `setConfig()` 觸發 React re-render 把同 tick 內的 task `agent.dispose()` 掉）和 #555（`onBeforeTask/onBeforeStep/onAfterStep/onAfterTask` 沒拿到 AbortSignal 沒法優雅 cancel），方向是「修 MCP + React reactive 邊界互踩的 race」。
- 連結：https://github.com/alibaba/page-agent/pull/585

### ChromeDevTools/chrome-devtools-mcp（Chromium 官方 MCP server）
- 類型：release / commit / pull request
- 內容：最新 release `chrome-devtools-mcp-v1.5.0`（2026-07-03），主分支最新 commit `913308263`（2026-07-03）就是 release-please bot 出的 1.5.0 changelog，前兩個 commit 是這個版本的兩個 feature：`67a56c055` 新增 `get_heapsnapshot_duplicate_strings` MCP tool（#2280，Chromium 工程師 Dominik Inführ 自己提的）、`a9228141` 修 extension-enforced output path validation（#2269，screencast / heap snapshot 也要套同一條 final-path check，防 dangling symlink 越界）；今天（2026-07-05）兩個 dependabot PR：#2300 puppeteer 25.2.1 → 25.3.0、#2299 chrome-devtools-frontend 1.0.1652307 → 1.0.1656291。
- 連結：https://github.com/ChromeDevTools/chrome-devtools-mcp/pull/2300

### huggingface/speech-to-speech（HF 即時語音 pipeline）
- 類型：release / commit / issue
- 內容：最新 release 仍為 `v0.2.10`（2026-06-11）——一個月沒出新版本；主分支最新 commit `4d5cf80c`（2026-07-03）Merge PR #331「Fix Docker image build」，前一個 commit `396947af` 是具體 fix、`af0dda53` 是另一次 dependabot actions group bump；今天（2026-07-04）連開 issue #334「Add missing faster-qwen3-tts features for the ggml backend」、PR #333（draft）native Keenable web search for OpenAI-compatible LLM backends（重點是把 web_search / fetch_page 變成 handler-side tool，realtime client 不必自己處理 function call，end-to-end 1.3s 出語音答案），方向是「speech-to-speech 內建 web-grounded 即時回答」。
- 連結：https://github.com/huggingface/speech-to-speech/pull/333

## 重點觀察
- 今天五個 repo 有四個（caveman、page-agent、chrome-devtools-mcp、codex-plugin-cc）都在 7/3 出了一個新 release，週初衝刺的特徵依舊明顯；唯一例外是 huggingface/speech-to-speech，上次發版是 6/11，已經在等 0.2.11 或 0.3.0 的訊號。
- 「PR-driven 而非 main commit」成為 agent / skill / IDE-bridge 專案的常態：codex-plugin-cc 自 v1.0.5 後 main 沒新 commit，但 7/5 仍冒出兩個扎實 fix PR（#436 docs anchor、#435 zombie GC）——觀察一個 project 不能只看 main 的 sha 變沒變，要看 PR merge 節奏。
- 安全 / 路徑 / 邊界 race 是當週共同主題：chrome-devtools-mcp #2269 解 dangling symlink path validation、codex-plugin-cc #435 解 stale background job state、page-agent #585 解 React re-render 把同 tick 起的 agent dispose 掉、caveman #649 解 Portuguese-defaulting prompt injection——都是「內部元件越來越多後，邊界處開始互踩」。
- 觀察到一個跨 repo 的 spam 訊號：今天 mailtopia 這個 GH 帳號在 codex-plugin-cc #437、caveman #651、chrome-devtools-mcp #2301 連發三則「build me an OS from this Markdown」的低品質 issue，模式完全相同，看起來是自動化 spam 在掃多個 AI agent repo；可以順手回報或忽略，但未來觀察 open issues 時要把這種 noise 扣掉。
- AI-native 工具鏈的「內建 MCP / web search / 取消語意」三件套正在收斂：page-agent 把 AbortSignal 傳進 hook、chrome-devtools-mcp 把 heap snapshot 暴露成 MCP tool、speech-to-speech 把 web_search 變成 handler-side tool——結論是「模型能跟什麼互動」的表面正在快速從文字 IO 往「可取消 + 可搜尋 + 可觀測」三個維度擴張。