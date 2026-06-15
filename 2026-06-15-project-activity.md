# GitHub 專案動態
- 檢查時間：2026-06-15
- 檢查對象：FinceptTerminal、Onyx、TimesFM、NVIDIA/personaplex 等感興趣的專案

## 動態摘要

### FinceptTerminal（財務堡壘）
- 類型：issue / release / commit
- 內容：最新 release 仍為 `v4.1.0`（2026-06-11）；最新 commit 為 `0b5aa11`「Project update」（2026-06-13）；最近更新的 issue 是 #341「[BUG] Installation issue in Windows」（2026-06-14），顯示新版本仍在消化安裝與部署回饋。
- 連結：https://github.com/Fincept-Corporation/FinceptTerminal/issues/341

### Onyx（黑曜）
- 類型：issue / release / commit
- 內容：最新 release 為 `v4.1.1`（2026-06-12）；最新 commit `44b7a5c` 新增 React Native Sidebar（2026-06-14）；今天（2026-06-15）又出現新 issue #12066「Anthropic Skills support?」，同時 PR #11978 持續推進 reindex lifecycle DB helpers，產品功能面與基礎設施面都很活躍。
- 連結：https://github.com/onyx-dot-app/onyx/issues/12066

### TimesFM（時光 FM）
- 類型：pull request / release / commit
- 內容：最新 release 仍為 `v2.0.1`（2026-06-11）；主分支最新 commit 仍停在 `8a22ca2` README 更新（2026-06-08），但 PR #438「fix(torch): strip Hugging Face download kwargs in from_pretrained」於 2026-06-14 更新，代表維護重心仍在相容性與安裝細節修補。
- 連結：https://github.com/google-research/timesfm/pull/438

### NVIDIA/personaplex
- 類型：pull request / commit / 其他
- 內容：目前沒有公開 GitHub release；最新 commit 仍是 `3428dfd` README 更新（2026-03-02）；近期最值得注意的是 PR #98「prevent path traversal via voice_prompt query parameter」（2026-06-06），專案目前更像是低頻維護，但安全面仍有補強動作。
- 連結：https://github.com/NVIDIA/personaplex/pull/98

## 重點觀察
- Onyx 仍是四個專案裡節奏最快的一個，今天還有新 issue，產品功能與底層重構同步推進。
- FinceptTerminal 剛發版後還在回收 Windows 安裝與使用回饋，短期內很可能再出修補版。
- TimesFM 主分支表面較安靜，但 PR 活動持續，像是在 2.0.1 之後整理相容性尾巴。
- personaplex 沒有明顯新版本訊號，值得關注的反而是安全修補而不是功能擴張。
