# GitHub 專案動態
- 檢查時間：2026-07-01
- 檢查對象：FinceptTerminal、Onyx、TimesFM、NVIDIA/personaplex、NousResearch/hermes-agent 等感興趣的專案

## 動態摘要

### FinceptTerminal（財務堡壘）
- 類型：release / commit / issue
- 內容：最新 release 仍為 `v4.1.0`（2026-06-11）並無新發版；主分支最新 commit 為 `6d82e1f`「quantcept link change」（2026-06-24），再來是 Merge PR #342「Fix Windows source build by using Qt bundled zlib」（2026-06-23）與 `f5021b1` 同主題提交（2026-06-17），整體重心仍是 Windows 安裝/相容性收尾；本週最值得注意的開 issue #347「[BUG] QZipReader removed from QtCore」（2026-06-29）由 thiagomacieira（QtCore 維護者本人）提報，預警 Qt 6.13 起 `QZipReader` private class 已移除，建議改用 KF6Archive 或 libarchive，併說明 Qt 專案對替代方案有興趣。
- 連結：https://github.com/Fincept-Corporation/FinceptTerminal/issues/347

### Onyx（黑曜）
- 類型：release / commit / pull request / issue
- 內容：最新 release 為 `v4.2.2`（2026-06-29，比 6/15 觀察到的 `v4.1.1` 更進一步）；今天（2026-07-01）三個主分支 commit 全是修補：`1e8216b` CI gate cloud merge jobs on image-audit（#12581）、`aa3c2d0` 讓 desktop app content 留在 macOS native title bar 下方（#12534）、`0122477` 修正 code interpreter 累積過多檔案（#12536）；當天新 PR/issue 也非常熱絡：#12588「feat: Add DocMost connector」、#12587「feat(mobile): add settings layout primitives + tab nav」、#12586「Add Docmost Connector」三項同日湧入，產品功能面（外部連接器、手機端設定）跟基礎設施面（CI、macOS 相容、資源回收）同步推進。
- 連結：https://github.com/onyx-dot-app/onyx/pull/12587

### TimesFM（時光 FM）
- 類型：issue / pull request / release
- 內容：最新 release 仍為 `v2.0.1`（2026-06-11）；主分支最新 commit 仍停在 `8a22ca2` README 更新（2026-06-08），PR 動態反倒比 main 更值得關注：issue #460「acero」（2026-06-30，推測是 acero 模型相關追問）新開；PR #459「Fix: torch.compile was a no-op in load_checkpoint」對應 issue #457——後者由 matdou 提出，指出原本 `torch.compile(self)` 在 `load_checkpoint` 中是 no-op、編譯 `forward` 可在 A100 上拿到 1.48× 加速，PR 已於 2026-07-01 有更新。整體像在 2.0.1 之後整理相容性與推論效能尾巴。
- 連結：https://github.com/google-research/timesfm/pull/459

### NVIDIA/personaplex
- 類型：pull request / issue / commit
- 內容：沒有公開 release（`/releases/latest` 回 404）；主分支 commit 仍停在 `3428dfd` README 更新（2026-03-02），明顯低頻維護節奏。值得注意的兩條動態是 PR #100「Add macOS Nix and MLX runtime support」（2026-06-27）與 issue #99「No Retention of system prompt post the context window」（2026-06-19），前者來自外部貢獻者，意圖把 Apple Silicon 推到 MLX runtime，後者直指核心體驗缺陷——目前看來專案安全/相容性面有動作，主線功能擴張主要靠外部貢獻。
- 連結：https://github.com/NVIDIA/personaplex/pull/100

### NousResearch/hermes-agent
- 類型：release / commit / pull request
- 內容：最新 release 為 `v2026.6.19`（Hermes Agent v0.17.0，2026-06-19），距離今天約兩週；今天（2026-07-01）主分支三個 commit 集中在 desktop/gateway 修補：`9be292f` MoA preset 改為持久選擇（#56417，引用 #54670）、`1641441` 修 desktop 長 prompt.submit 假 timeout（#56411，引用 #55024，MoA 與深推理場景）、`5eaccf5` 修 gateway 在 context compression in-flight 時被中斷產生孤兒 sibling session（fixes #56391）。當天新 PR 也都是修補取向：#56487 Discord approval button ack、#56486 Matrix M_LIMIT_EXCEEDED 重試、#56485 CLI editor_auto_submit toggle 還原 Ctrl+G 行為，看得出團隊正在密集收 release 後的體驗問題。
- 連結：https://github.com/NousResearch/hermes-agent/pull/56411

## 重點觀察
- 今天的橫切特徵是「無新 release 的大日子」：FinceptTerminal、TimesFM、personaplex 都沒有新版，Onyx `v4.2.2` 也已是兩天前，Hermes Agent `v2026.6.19` 剛過一週；但 Hermes Agent 與 Onyx 的主分支當天仍有實際 commit，顯示「發版節奏慢、合併節奏快」的保養型週期正在進行。
- 桌面端體驗與相容性是今天三條主要修補線的共同點：Onyx 在 macOS native title bar、code interpreter 檔案累積；Hermes Agent 在 MoA 持久化、prompt.submit timeout、context compression in-flight；FinceptTerminal 與 Qt 6.13 `QZipReader` 私有 API 移除拉扯——都在收 6 月中下旬發版的後座力。
- 開源貢獻者開始把 Apple Silicon 列為一級目標，personaplex PR #100（macOS Nix + MLX runtime）是清楚訊號；對主人評估 persona / TTS 類模型在 Mac 上的可行性值得進一步看。
- TimesFM 雖 main 靜悄悄，PR #459 卻帶實質 1.48× A100 推理加速，這種「main 沉默、PR 有料」的結構代表官方仍透過 review 把關相容性，不一定立刻合 main；後續觀察重點是 #459 什麼時候被 merge。
- FinceptTerminal issue #347 由 QtCore 維護者本人提報，警訊等級較高：若 Fincept 沒打算退回舊版 Qt，就要決定引進 KF6Archive 或 fork `QZipReader`，否則下一次 Qt bump 時整套 Windows installer 會再炸一次——這條議題值得持續追蹤後續 PR。
