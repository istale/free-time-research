# GitHub 專案動態
- 檢查時間：2026-07-02
- 檢查對象：FinceptTerminal、Onyx、TimesFM、NVIDIA/personaplex、NousResearch/hermes-agent

## 動態摘要

### FinceptTerminal（財務堡壘）
- 類型：issue / pull request / commit
- 內容：最新 release 仍為 `v4.1.0`（2026-06-11）；main 分支最新 commit 為 `6d82e1f`「quantcept link change」（2026-06-24），再往前是 `cc2d07d` 合併 PR #342「Fix Windows source build by using Qt bundled zlib」（2026-06-23）；開立中的 issue #347「[BUG] QZipReader removed from QtCore」（2026-06-30 更新）顯示 Qt 升級後的 API 缺口仍未補，PR #346 同步補上 30 秒 HTTP 逾時，平台穩定度仍是當前主軸。
- 連結：https://github.com/Fincept-Corporation/FinceptTerminal/issues/347

### Onyx（黑曜）
- 類型：release / pull request / commit
- 內容：最新 release 為 `v4.2.2`（2026-06-29），比上週 `v4.1.1` 跳一版；主分支今天（2026-07-02）一口氣冒出三個 mobile 相關 commit：`1e81e64` 行動版 settings 佈局（#12587）、`c183828` 恢復 in-flight chat + 自動命名 session（#12585）、`c5ee750` 修正 Google OAuth PKCE 驗證碼遺失（#12644）；開立中另有 PR #12647「agent selection — gallery, sidebar rail, avatars」與 issue #12645「SESSION_EXPIRE_TIME_SECONDS 32-bit 溢位導致 setTimeout 立即觸發」，行動版體驗與本機時間常數坑同步開火。
- 連結：https://github.com/onyx-dot-app/onyx/releases/tag/v4.2.2

### TimesFM（時光 FM）
- 類型：pull request / commit / issue
- 內容：最新 release 仍為 `v2.0.1`（2026-06-11），但 main 已在 2026-07-01 連發三筆更新：`26d3e89` 合併 PR #459「torch.compile 在 load_checkpoint 是 no-op」修補、`9b82723` 合併 PR #461 download-tracker、`2011cfb` 在 `_from_pretrained` 預先下載 config.json 並把版本 bump 到 2.0.2；也就是說 v2.0.2 隨時會出；近期 open issue 主要是 #460「acero」（2026-06-30 提問）與兩個 dependabot 升級 PR。
- 連結：https://github.com/google-research/timesfm/pull/459

### NVIDIA/personaplex
- 類型：pull request / issue / commit
- 內容：此 repo 仍無任何 GitHub release（API 回 404）；main 分支最新 commit 仍是 `3428dfd`「Update README.md」（2026-03-02），整體進入長期低頻維護；開立中最值得注意的是 PR #100「Add macOS Nix and MLX runtime support」（2026-06-27 開立），把 Apple Silicon 推上第一線；issue #99「system prompt 在 context window 後不保留」與 #97「模型無法分辨自己角色」都是語音對話體驗的老問題。
- 連結：https://github.com/NVIDIA/personaplex/pull/100

### NousResearch/hermes-agent
- 類型：release / pull request / commit
- 內容：今天之前剛發布 `v2026.7.1`「The Judgment Release」（2026-07-01），main 緊接著再發 `88d1d62`「fix(streaming): handle completed responses with empty/None choices」（#55933 / #56713，2026-07-02），其前是 `76be770` MoA 測試加固與 `7951250`「fix(moa): lift hidden Anthropic aux output cap」；開立中三個 PR 全是修補型：#56827 修正 desktop 在 remote mode 讀不到 composer 圖片預覽、#56825 阻斷 `sync_back` 寫回 Hermes state、#56824 在 SessionDB 開啟時修補孤兒 FTS5 shadow tables，整體偏穩定性 patch 集。
- 連結：https://github.com/NousResearch/hermes-agent/releases/tag/v2026.7.1

## 重點觀察
- **Onyx 與 hermes-agent 是本週唯二今天還在 push 動態的專案**，且都集中在 mobile/desktop 體驗與 streaming/state 邊角的修補，代表活躍社群正把精力收在「已上線功能的穩定」而非新模型版本。
- **TimesFM 雖未出新 release，但 main 已默默把版本字串 bump 到 2.0.2 並合併 torch.compile 修補**，下一次發版只是時間問題；對時序預測工作流使用者值得預先做相容性驗證。
- **FinceptTerminal 仍卡在 Windows / Qt 升級後的相容尾巴**（QZipReader 缺、zlib 內建化），commit 密度明顯低於上週，發版節奏可能還會再觀望一輪。
- **personaplex 完全沒有 GitHub release 機制**，只能從 PR 看進展；PR #100 引入 macOS Nix + MLX 是自 3 月以來最實質的功能推進，呼應 Apple Silicon 在音訊推論端的能見度提升。
- **跨 repo 共同訊號：依賴治理（msgpack、jupyterlab、Qt zlib、PKCE、setTimeout 32-bit）正在取代模型本體成為開源 AI/音訊專案的主要修補面**，值得作為後續觀察主人技術雷達的固定題。
