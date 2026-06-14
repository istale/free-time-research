# GitHub 專案動態
- 檢查時間：2026-06-14
- 檢查對象：FinceptTerminal、Onyx、TimesFM、NVIDIA/personaplex

## 動態摘要

### Onyx（黑曜）
- 類型：release
- 內容：v4.1.1 安全強化版（2026-06-12）
  - 統合 SSRF 保護為單一管理設定
  - 全域 URL 驗證強化（web-connector）
  - Claude Fable 5 / Mythos 5 路由至 adaptive thinking
  - 強化 MinIO/S3 預設憑證（安全漏洞修補）
  - 非開發環境強制要求 USER_AUTH_SECRET
  - docx-preview 輸出消毒（XSS 風險）
- 連結：https://github.com/onyx-dot-app/onyx/releases/tag/v4.1.1

### FinceptTerminal（財務堡壘）
- 類型：release
- 內容：v4.1.0（2026-06-11）— Native C++20 財務智慧終端，Qt6 + 嵌入式 Python 分析，50+ 螢幕
- 連結：https://github.com/Fincept-Corporation/FinceptTerminal/releases/tag/v4.1.0

### TimesFM（時光FM）
- 類型：commit / release
- 內容：v2.0.1（2026-06-11）提及「Package updated to handle TimesFM-2.5」，預告 2.5 版 API；最新 commit 為 README 更新與 global-temperature example 遷移至 2.5 API
- 連結：https://github.com/google-research/timesfm

### NVIDIA/personaplex
- 類型：commit
- 內容：近期以 README 更新為主；PR #18 修正在模型初始化階段的記憶體占用問題（init-oom fix）
- 連結：https://github.com/NVIDIA/personaplex

## 重點觀察
- **黑曜（Onyx）安全進度**：v4.1.1 一次性修補多個安全問題（SSRF + S3 憑證 + 認證強化），與先前「安全強化階段」筆記吻合，顯示該專案正處於安全加固衝刺期
- **時光FM 2.5 預備中**：社群已開始遷移範例至 2.5 API，但正式 release tag 尚未出現；Master 先前標註「2.0→2.5 過渡密集修 bug 中」，目前仍適用
- **財務堡壘（FinceptTerminal）**：v4.1.0 為正式跨平台版本（Windows/Linux/macOS），可持續關注後續使用者反饋
