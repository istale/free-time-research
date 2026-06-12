# GitHub 專案動態
- 檢查時間：2026-06-12
- 檢查對象：FinceptTerminal、Onyx、TimesFM、personaplex 等感興趣的專案

## 動態摘要

### FinceptTerminal
- 類型：issue
- 內容：Issue #298「[BUG] Network Error. Check your connection.」於 2026-06-12 06:21 UTC 再次更新，顯示網路連線問題仍有人回報；昨日發布的 v4.1.0 仍在觀察修補後回饋。
- 連結：https://github.com/Fincept-Corporation/FinceptTerminal/issues/298

### FinceptTerminal
- 類型：release
- 內容：最新 release 仍為 `v4.1.0`，發布時間 2026-06-11 06:01 UTC；緊接著的 release workflow commit `7508c1e` 更新了 README 下載連結與 `updates.json`。
- 連結：https://github.com/Fincept-Corporation/FinceptTerminal/releases/tag/v4.1.0

### Onyx
- 類型：release
- 內容：`v4.1.0` 於 2026-06-11 21:19 UTC 發布，今天清晨已有新 commit `cfd3c15`，內容是限制 indexing worker 記憶體與 Google Drive 擷取文字大小，顯示 release 後仍持續快節奏修補。
- 連結：https://github.com/onyx-dot-app/onyx/releases/tag/v4.1.0

### Onyx
- 類型：pull request
- 內容：PR #11937「refactor(tokens): centralize design tokens in @onyx-ai/shared」在 2026-06-12 07:55 UTC 更新，前端設計 token 正在集中化，顯示產品表層也同步重構中。
- 連結：https://github.com/onyx-dot-app/onyx/pull/11937

### TimesFM
- 類型：release
- 內容：`v2.0.1` 於 2026-06-11 20:20 UTC 發布；repo 最新公開 commit `8a22ca2` 是 2026-06-08 的 README 更新，表示這波版本可能以整理與兼容修補為主。
- 連結：https://github.com/google-research/timesfm/releases/tag/v2.0.1

### TimesFM
- 類型：pull request
- 內容：PR #415「fix: add MPS (Apple Silicon) support for PyTorch backend」在 2026-06-12 04:09 UTC 更新，Apple Silicon / MPS 支援仍是近期重點。
- 連結：https://github.com/google-research/timesfm/pull/415

### personaplex
- 類型：pull request
- 內容：目前沒有 GitHub release；最近的活躍變更是 PR #98「prevent path traversal via voice_prompt query parameter」，於 2026-06-06 更新，重點落在安全修補。
- 連結：https://github.com/NVIDIA/personaplex/pull/98

### personaplex
- 類型：issue
- 內容：Issue #97「The model cannot distinguish its role」仍是近期使用層面的代表性問題，反映 persona / role conditioning 在實戰部署上還有不穩定案例。
- 連結：https://github.com/NVIDIA/personaplex/issues/97

## 重點觀察
- FinceptTerminal 昨日剛發 `v4.1.0`，今天就有新的 bug 回報浮上來，代表 release 後回饋期已開始。
- Onyx 與 TimesFM 都在 2026-06-11 發布新版本，且 2026-06-12 仍有 PR / commit 活動，節奏明顯比 personaplex 更快。
- personaplex 目前比較像早期維護階段：沒有 release，但安全性與角色控制問題值得持續觀察。
