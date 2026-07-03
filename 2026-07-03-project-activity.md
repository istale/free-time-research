# GitHub 專案動態
- 檢查時間：2026-07-03
- 檢查對象：usestrix/strix、JuliusBrussee/caveman、msitarzewski/agency-agents、vercel/eve、langchain-ai/openwiki

## 動態摘要

### usestrix/strix（AI 滲透測試）
- 類型：release / commit / issue
- 內容：最新 release 為 `v1.0.4`（2026-06-09）；main 分支今天（2026-07-03）連發三筆：`7e808f7` 新增 OAuth / AWS / prototype pollution / deserialization / Django 五個安全 skill（#617）、`c3997cd` 修正 OpenRouter stream 成本回報（#634）、`dc8b790` 修正 note ID 碰撞（#630），全部圍繞「真實攻防場景」與「成本/紀帳正確性」。開立中另有 issue #644「Bedrock + Claude 啟動時 `tool_choice.type` 欄位缺失」標記為 bug（2026-07-03），以及 PR #645「agent 漏呼叫 lifecycle tool 時 compile fallback 報告」、PR #643「non-mutating quality check target」。
- 連結：https://github.com/usestrix/strix/pull/617

### JuliusBrussee/caveman（克羅埃西亞洞穴人 token 壓縮）
- 類型：release / commit / pull request
- 內容：最新 release 為 `v1.9.0「Rock pinned. Rock verified. opencode rock work now」`（2026-06-12），主軸是「安裝 pinned + 完整性校驗」（#261、#262）；main 在 2026-06-12 同日衝刺：`32f37af` 把遠端 fetch 綁到 v1.9.0、`ddf2121` 修 opencode plugin 在 Bun 編譯 runtime 載入、`25d22f8` 更新 README；今天三條 open PR 全是 hook 子系統升級：#621 把 caveman mode 傳給 subagent（`SubagentStart` hook）、#622 改用 `additionalContext` 餵 `/caveman-stats` 給 macOS app、#623 抽出共用 mode parser 修三處飄移，整體朝「Claude Code 子代理一致性」推進。
- 連結：https://github.com/JuliusBrussee/caveman/releases/tag/v1.9.0

### msitarzewski/agency-agents（AI 代理軍火庫）
- 類型：commit / pull request
- 內容：此 repo 仍未發 GitHub release（API 回 404）；main 在 2026-07-01 連發 `fc5a192` 合併 PR #642（antigravity skills 設定）、`309a8e7` 修正 antigravity skills 路徑與 deterministic `SKILL.md` 產生，`7632f06` 補 README 安裝工具清單（2026-06-30）；開立中清一色是「新增代理角色」類 PR：#655「Add healthcare/ division：Clinical Evidence Agent + Sovereign Health Systems」、#654「AI Automation Engineer agent」、#653「Office Collaborator agent（解 #644）」，典型「prompt 角色典範館」路線。
- 連結：https://github.com/msitarzewski/agency-agents/pull/642

### vercel/eve（建構 Agent 的 Framework）
- 類型：release / commit / pull request / issue
- 內容：昨天（2026-07-02）剛發 `eve@0.19.0`，其中 minor change `92f5162` 新增 generic chat SDK channel for adapter-backed agents、patch `8892504` 修 Render 渲染；main 緊接著今天又三筆 hotfix：`419b648` 加強 bug report template 要求 repro 與 logs、`f9621b6` 改成「resume 缺附件時 degrade 而不是整輪失敗」、`6f9364a` server 關機時一併停止所有 sandboxes。開立中三條與 MCP 直接相關：#521（issue）要求 MCP connection URL 在 runtime 才解析以解 Vercel 部署問題、#520（PR）扁平化 MCP tool input schema 的 top-level union、#519（PR）把 subagent 連線授權事件 bubble 到 parent channel。
- 連結：https://github.com/vercel/eve/releases/tag/eve@0.19.0

### langchain-ai/openwiki（Agent 文件自動維護 CLI）
- 類型：commit / pull request / issue
- 內容：此 repo 尚未發任何 GitHub release，但 main 在 2026-07-01 同日衝刺：`58b4bd3` 標記 `release: 0.0.1`（#20）、`14ca04a` README 改進（#19），前一天 `36ee843` 合併 #17「prompt-improvements-concise」；意即 v0.0.1 已透過 commit 形式標記出爐。開立中三條都圍繞 provider / 協議面：#53（PR）「feat: add DeepSeek as a supported provider」、#52（issue）「ACP support」、#51（issue）「同一 repo 反覆 `--init` 成本變異 ~12×（GLM 5.2 / OpenRouter，無快取）」——最後一條是典型的 token cost 可觀測性坑。
- 連結：https://github.com/langchain-ai/openwiki/pull/20

## 重點觀察
- **5 個 repo 今天只有 2 個有正式 release（caveman v1.9.0、vercel/eve@0.19.0），其餘 3 個（strix、agency-agents、openwiki）都是「commit-only / 隱性 release」**；其中 openwiki 雖然 GitHub release 端仍是 404，但 `release: 0.0.1` 已 commit，這是開源新專案極常見的「先 commit 後 tag」釋出模式，值得追蹤 v0.0.1 tag 何時真正落 release。
- **「Agent 生態」是今天最強訊號**：vercel/eve 的 MCP runtime / sandbox 終止、caveman 的 subagent hook、strix 的 cost 紀帳、openwiki 的 provider 擴充、agency-agents 的角色典範——五個專案從不同切片（framework、tool、docs、persona、observability）同時在 push，代表 2026 Q3 的開源能量明顯從「模型本體」轉到「agent 操作層」。
- **vercel/eve 與 strix 都在做「失敗情境的優雅降級」**：eve 把缺附件改為 degrade、strix 把 agent 漏呼叫 lifecycle tool 改為 compile fallback 報告；兩條不同語境但同樣的工程哲學，呼應昨天觀察的「依賴治理取代模型本體成為修補主軸」。
- **provider / 連線彈性是熱修集中區**：openwiki 加 DeepSeek provider、eve 要求 MCP URL runtime 解析（unblock Vercel deploy）、caveman 把 mode 傳給 subagent——三條都圍繞「在同一框架內接更多上游」這條主軸。
- **cost / 觀測性終於浮上 issue 板**：openwiki #51「`--init` 12× cost variance」與 strix `c3997cd`「stream 成本回報」同時出現，呼應主人之前關心的「agent 跑一次到底燒多少錢」觀察題，可能成為下一階段跨 repo 共通基礎設施。