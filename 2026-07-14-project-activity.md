# GitHub 專案動態
- 檢查時間：2026-07-14
- 檢查對象：今日 GitHub Trending Top 3（OpenCut-app/OpenCut、moeru-ai/airi、github/spec-kit）+ Web 探索精選 2（mozilla-ai/otari、rowboatlabs/rowboat）

## 動態摘要

### OpenCut-app/OpenCut（CapCut 開源替代，瀏覽器影片編輯器）
- 類型：release / commit / issue
- 內容：最新 release 仍為 `v0.3.0`（2026-04-15，masks + keyframe 曲線編輯器 + 音量/速度控制 + stickers 的功能大版本），但 main 分支 2026-07-10 連發 `b59255c`「feat(desktop): scaffold desktop app」與 `bab8af8`「Merge branch 'rewrite'」，清楚標示整個 repo 正在從純 Web 編輯器改寫為「Web + Desktop 雙軌」架構，是當週最值得追的策略性變更。當天 open issue 集中在使用者體驗細節：#838（2026-07-14）要 still waveform / volume visualizer、#837（2026-07-13）回報 Moon CI 解析不存在的 master 分支、#834（2026-07-12）反映官網被 rate-limited/down——都是 release tag 之後實際運營冒出來的「日常問題」，恰好印證 tag 與 main 嚴重脫鉤。
- 連結：https://github.com/OpenCut-app/OpenCut/issues/838

### moeru-ai/airi（自架 Grok Companion / VTuber AI 靈魂容器）
- 類型：release / commit / pull request
- 內容：最新 release 為 `v0.11.0`（2026-07-08，重點是 stage UI 可手動停止語音 + 失敗 tool call 從 chat 端 retry + 內建 LLM 橋接），距離今天（2026-07-14）僅一週，發版節奏算快。2026-07-13 當天三條 commit 都圍繞「衛生 + 錯誤處理」：`563a738` 修 settings 圖示 safelist、`88983a1` 重整 audio pipelines 的 event types 與 error handling、`5b47e8a` 把 `.env` 加進 `.gitignore` 防 credential 洩漏。三條 open PR 全部偏向安全/收斂面：#2054「validate synced message ownership」、#2052「hide provider setup in Steam onboarding」、#2051「add opt-in companion observation mode」——一個遊戲/桌面代理框架，能把 owner-validate 與 Steam onboarding 列為優先 PR，顯示其威脅模型已經從「能不能跑」轉向「能不能安全跑」。
- 連結：https://github.com/moeru-ai/airi/pull/2054

### github/spec-kit（GitHub 官方出品，Spec-Driven Development 工具包）
- 類型：release / commit / issue / pull request
- 內容：最新 release 為 `v0.12.14`（2026-07-13，當天發版；release 內含 Spec Kit Memory extension 上 community catalog 與 Test-First Governance preset 兩個 extension），緊接著 commit `654793b`「chore: release 0.12.14, begin 0.12.15.dev0」直接切到下一個 dev 週期，發版節奏非常緊（幾乎每 1–2 天一個 patch）。當天（2026-07-14）open issue #3507 直接戳痛點：「`/speckit-implement-waves` 應該把每個 phase 跑在 subagent 避免 context overflow」——意即 spec-kit 自己也撞上 2026 下半年所有長流程 spec-kit 都會遇到的 context window 限制；同期 PR #3509 修 init-options.json 缺尾換行、PR #3503 文件化 `__SPECKIT_COMMAND_` token 給 portable cross-command refs，都是「為了把 spec-kit 嵌入更多 agent runtime」的鋪墊。
- 連結：https://github.com/github/spec-kit/issues/3507

### mozilla-ai/otari（自架 OpenAI 相容 LLM gateway，40+ provider 統一端點）
- 類型：release / commit / pull request
- 內容：最新 release `v0.2.0`（2026-07-03，Postman auth 改走 Authorization Bearer 等 bugfix）；當週 commit 與 PR 集中在「品質 + 文件 + deploy 易用性」三條軸：`3566b41`（2026-07-11）替 README 補 demo gif、`dd8b71d`（2026-07-07）強制 ruff PERF rules 跨 codebase、當週 open PR #250（2026-07-13）把 MCP/guardrail URL check 從 blocking event loop 改成 non-blocking + provider 維度去重、PR #251（2026-07-13）加 free Render Blueprint、PR #248（2026-07-10）補 OpenAPI ApiKeyAuth security scheme。整體訊號是「不再加新 provider，改把 v0.2.0 的 40+ provider 跑穩並降低自架門檻」，與另一個 LLM gateway（OpenRouter / Portkey）的差異化戰場已經從 provider 數量轉向「Render 一鍵部署 + OpenAI 相容 + 內建 guardrail」三件套。
- 連結：https://github.com/mozilla-ai/otari/pull/250

### rowboatlabs/rowboat（開源 AI coworker / Claude Desktop 本地優先替代）
- 類型：release / commit / issue / pull request
- 內容：最新 release `v0.7.1`（2026-07-07，當週釋出 HN 後衝到 218 分 / 94 評論）；2026-07-13 三條 commit 全部圍繞「升級與維運」：`4182794` Merge AI SDK 7、`cc2daf1`「chore(x): migrate to AI SDK 7」、`21bf4b9` 把 7/12 maintenance 批次 merge 進 main。當天（2026-07-13）open issue #749「Broken RTL / BiDi rendering in chat messages」是國際化細節；PR #747「feat(x): model tiers for spawned sub-agents」直接把 sub-agent 分層模型選擇做出來，呼應今日 #3507「spec-kit implement-waves 也想跑 subagent」的需求；PR #748「Granola-parity meeting capture: resident app + ambient detection + auto-stop」則是對標商用 AI 會議助理的功能。整體訊號：AI coworker 賽道正從「能不能記住對話」升級到「能不能調度多模型 sub-agent + 接管會議現場」。
- 連結：https://github.com/rowboatlabs/rowboat/pull/747

## 重點觀察
- 今天 Trending Top 3 在表面主題上跨度極大（影片編輯 / VTuber 靈魂容器 / spec-driven dev），但 5 個 repo 串起來的核心訊號其實只有一個：「AI 工具要從『能跑』變成『能長期跑在桌面/瀏覽器/IDE 旁邊』」——OpenCut 在改寫成 desktop app、airi 在加 Steam onboarding、spec-kit 在文件化 portable cross-command token、otari 在做 Render 一鍵部署、rowboat 在做 resident app 與 ambient meeting capture，每個專案都把 deploy surface 從「給開發者跑指令」擴張到「給終端使用者在系統層常駐」。
- 「sub-agent 作為解 context window 的標準答案」這個主題在 24 小時內被兩個完全不同領域的 repo 各自推進：spec-kit issue #3507 要 `/speckit-implement-waves` 把每個 phase 跑在 subagent 避免 context overflow、rowboat PR #747 直接實作「spawned sub-agents 的 model tiers」。意味著 2026 下半年的 spec/agent runtime 主流共識已經是「長流程必須切成 sub-agent 圖，每個 sub-agent 帶自己的 model tier」——這跟主人「議會互動紀律 v2」的 OVER_COUNTER header 是同一條線：把「整段 session」拆成「帶宣告式 context 的小單位」，差別在 spec-kit/rowboat 是程式碼層面，議會是協議層面。
- 觀察 cron 監控方法論本身：今天 trending top 5 中第 2 名（HKUDS/Vibe-Trading）昨天才被巡邏收錄，若死守「取前 3」就會原地打轉；同樣地 Tier B 的 otari / rowboat 若不額外做 14 天內「stars ≥ 1k 或 points ≥ 5」門檻過濾，HN Algolia 默認結果會塞進一堆 1-pt 的 dead repo（gavingu2255-ai/WLM-Open-Source、Emmi-AI/noether、metaforensics-ai/semantics-av-cli 等）。今天這套「Trending top 3 + 跳過昨日已收錄 + HN 14d 內高訊號」三段式比昨天的純取前 3 更穩。
- 安全與合規依然是這週清單上每個 repo 都內建的事後驗收項：airi 三條 PR 兩條是 ownership/onboarding 收斂、otari PR #250 把 MCP/guardrail URL check 改成 non-blocking + dedupe、spec-kit PR #3503 文件化 `__SPECKIT_COMMAND_` token 強調 portable 邊界、rowboat issue #749 抓 RTL/BiDi bug——沒有任何一個 repo 把「安全」當作獨立 release 來做，而是把它編進每個日常 PR 的 acceptance checklist，這是 2026 下半年開源治理的成熟樣貌。
- 5 個 repo 的 release 與 main 脫鉤程度分化明顯：spec-kit 幾乎逐日發版、airi 一週一發、otari 兩週一發、rowboat 一週一發、OpenCut 反而 release 停在 2026-04 但 main 已「rewrite branch merge」並 scaffold desktop app；同樣是「release 頻率 ≠ 活躍度」鐵律，但 OpenCut 是「release tag 沒追上 main」，spec-kit 是「release 已經追上 main 還嫌慢」——同樣的觀察句對這兩個 repo 結論完全相反，cron 觀察時必須用「release + commit 雙軸」交叉判斷，不能只看單一指標。