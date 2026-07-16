# GitHub 專案動態
- 檢查時間：2026-07-16
- 檢查對象：OpenCut、hallmark、mattpocock/skills、openai/codex、xai-org/grok-build（Trending Top 3 + Web 探索 2）

## 動態摘要

### OpenCut（OpenCut-app/OpenCut，Trending #1）
- 類型：release / commit / issue
- 內容：最新 release 為 `v0.3.0`（2026-04-15），主打 masks、keyframe 曲線圖編輯器、音量/速度控制、貼紙等；最新 commit `bab8af8` 是 2026-07-10 把 `rewrite` 分支合進 main，緊接著 `b59255c` 啟動 desktop app scaffold；今天 open issue #855 是 Termux kex 環境相關，#852 / #851 是貢獻者入口 404 與本地 dev server 連不到的兩個小 bug。
- 連結：https://github.com/OpenCut-app/OpenCut/issues/855

### hallmark（Nutlope/hallmark，Trending #2）
- 類型：commit / issue
- 內容：此 repo 沒有 published release（API 404，符合預期 — 它是一份 Claude Code/Cursor/Codex 的「anti-AI-slop」設計 skill，最新 v1.x 是文件本身）。最新三個 commit 都在 2026-06-04，集中處理 stylesheet merge 與 slop-test gate 數量一致性。今天（2026-07-16）同時冒出 issue #31「強化 slop 偵測 gate」與 #30「Brow. They all have brows.」（眉毛 macrostructure），加上文件修復 PR #29，使用者回饋集中在 macrostructure 規則擴充與 recipe/study 連結修正。
- 連結：https://github.com/Nutlope/hallmark/issues/31

### mattpocock/skills（mattpocock/skills，Trending #3）
- 類型：release / commit / issue
- 內容：最新 release 為 `v1.1.0`（2026-07-08），以 minor changes 形式發出；2026-07-14 一口氣把 `to-questionnaire` skill（WIP）切進 main，包含三條相關 commit（`e9fcdf9`、`7f68c06`、`bbce2f9`），描述是「把難以一次決定的選擇變成可填的 questionnaire」。今日熱門 issue #582 點出「Code Review Skill 應該驗 subagent 輸出而非直接信任」，呼應當前 agent framework 對 sub-agent 可信度的整體焦慮；#581 與 #580 則圍繞 `/code-review` 與 `/implement` 拆分、GitLab issue closeout 對帳。
- 連結：https://github.com/mattpocock/skills/issues/582

### openai/codex（Web 探索）
- 類型：release / commit / issue
- 內容：最新 release `rust-v0.144.5`（2026-07-16）改善危險指令偵測（更完整的 forced `rm` 形式），同日三條 commit 都是同日（2026-07-16）落地：`cbc83d9` 在 MCP 工具輸出保留加密內容、`1d94125` 把 cache-write tokens 加進 raw response schema、`800715d` 從 MCP tool call metadata 拔掉 template_id。今日 open issue 多與 quota/CLI 字元顯示相關，但更值得注意的是搜得到的 #26210「Encrypt multi-agent v2 message payloads」與 #28058「Regression: encrypted MultiAgentV2 messages remove readable task audit trail」—— 正是 HN 上 424 點熱度的「Codex 開始加密 sub-agent prompts」事件的本體。
- 連結：https://github.com/openai/codex/issues/28058

### xai-org/grok-build（Web 探索）
- 類型：commit
- 內容：沒有任何 published release、沒有 open issue（API 回空陣列），commit 歷史只有一條 `c68e39f`（2026-07-16）「Publish harness and TUI open-source — initial sync from the monorepo」。這正是 2026-07-15 HN 上 367 點「Grok Build is open source」的 repo 本體：今天才把 harness + TUI 從 monorepo 首次 sync 出來，議題/issue tracker 都還沒開，後續節奏值得持續追。
- 連結：https://github.com/xai-org/grok-build/commit/c68e39f

## 重點觀察
- 今天的 GitHub 動態非常「agent infra 化」：5 個 repo 中 3 個（mattpocock/skills、openai/codex、xai-org/grok-build）都直接服務 coding agent / sub-agent 場景，且都集中在 sub-agent 可信度、加密審計、記憶與開源化這幾條主軸。
- openai/codex 與 xai-org/grok-build 都在 2026-07-16 當天有實質 release/commit 落地（前者小修、後者首次開源），新聞熱度與程式碼動態完全對得上—— HN 的兩條 400+ 點大新聞背後確實有可驗證的 commit/issue。
- mattpocock/skills 的 `to-questionnaire` skill 顯示 agent UX 正在往「把模糊決策拆成結構化表單」演化；而 #582 對 subagent 輸出「直接信任」的質疑，與 codex #28058 對加密 sub-agent 訊息的審計缺口屬於同一類焦慮，方向一致。
- Nutlope/hallmark 雖然沒 release、commit 停在 6 月，但今天 macrostructure rule 的兩條使用者回饋代表「anti-AI-slop」這個 niche 社群已開始自發擴充 catalog，可視為長期演進中而非凍結。
- OpenCut 主軸是桌面/影片剪輯，跟其他四個 agent repo 完全無關，作為 trending 第一名主要撐起「非 AI 領域仍有強勢開源動能」的對照組——也提醒下次選 trending 時可考慮是否要刻意避開、避免清單都偏 AI 一邊。