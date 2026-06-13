# GitHub 專案動態
- 檢查時間：2026-06-13
- 檢查對象：FinceptTerminal、Onyx、TimesFM、personaplex 等感興趣的專案

## 動態摘要

### FinceptTerminal（財務堡壘）
- **類型**：release + commit
- **內容**：
  - v4.1.0 發布（2026-06-11）— Native C++20 金融智慧終端，Qt6 + 嵌入式 Python 分析，50+ 螢幕
  - 今日有活躍 commit（Project update, General updates），持續開發中
- **連結**：https://github.com/Fincept-Corporation/FinceptTerminal

### Onyx
- **類型**：release
- **內容**：v4.1.1 發布（2026-06-12）— 多項安全修正（SSRF 保護、S3/MinIO 憑證強化、USER_AUTH_SECRET 要求）、Claude Fable/Mythos 5 路由修復、密碼欄位 UI 修正
- **連結**：https://github.com/onyx-dot-app/onyx

### TimesFM
- **類型**：release + commit
- **內容**：
  - v2.0.1 發布（2026-06-11）— 支援 TimesFM-2.5 API
  - 近一週多筆 commit：修复 model loading 問題、forecast_naive slicing bug、global-temperature 範例更新
- **連結**：https://github.com/google-research/timesfm

### personaplex
- **類型**：commit
- **內容**：近期無新 release，最後 commit 為 2026-03-02 README.md 更新；年初有記憶體優化相關修正（init-oom fix）
- **連結**：https://github.com/NVIDIA/personaplex

## 重點觀察
- **FinceptTerminal**：v4.1.0 剛發布，今日仍有活躍更新，專案處於熱絡維護狀態
- **Onyx**：安全相關修正密集，v4.1.1 主打 SSRF/S3 安全性，可留意是否涉及已公開漏洞
- **TimesFM**：2.0 → 2.5 版本過渡期，近一週 commit 集中在 bug fix 和 API 相容性，社群活躍
- **personaplex**：相對靜止，NVIDIA 內部可能仍在準備下一版 release
