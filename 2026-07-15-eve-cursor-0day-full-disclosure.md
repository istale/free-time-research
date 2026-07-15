# Cursor 0day: When Full Disclosure Becomes the Only Protection Left
- 原始連結：https://mindgard.ai/blog/cursor-0day-when-full-disclosure-becomes-the-only-protection-left
- 閱讀時間：2026-07-15（晚）

## 摘要

Mindgard 於 2026-07-14 公開揭露一個存在於 Cursor IDE（估值約 $60B、7M+ active users、1M+ paying、50K+ companies）的任意代碼執行（RCE）漏洞。漏洞極簡單：Cursor 在載入專案時會掃描 git binary 路徑，而「workspace 本身」被列為其中之一——只要 repo 根目錄放一支惡意 `git.exe`，Cursor 會自動執行、零提示、零警告。

**漏洞的技術本質**
- 路徑解析邏輯把 workspace root 視為可信任的 git binary 來源
- 用 Windows Calculator 改名成 `git.exe` 放進 repo root 就能 demo（PoC 截圖就是一堆 calc 視窗自動跳起來）
- 在 Cursor 3.2.16（2026-04-30 最後驗證）持續存在
- 觸發後會「週期性重複執行」——不是一次性，是 IDE 正常運作中持續呼叫 repo 內的可執行檔

**為何這次走「Full Disclosure」而非協調揭露**
- 2025-12-15 首次回報 security-reports@cursor.com
- 7 個月內 Mindgard 走完 security.txt、HackerOne、CISO 直接聯繫、LinkedIn 公開求助等所有路徑
- HackerOne 一度把 report 關成 Informative/Out of Scope，重新申訴才 reopen
- Cursor 端 197+ 版本更新過去、70+ 版本釋出，**漏洞原封不動也無任何溝通**
- Mindgard 最終判定：繼續等 = 讓使用者活在「假安全感」中，選擇全披露

**修補前的暫時緩解**
- 企業/managed Windows：用 AppLocker 或 Windows App Control 對 `%USERPROFILE%\source\repos\*\filename.exe` 設 path-based deny（不要用 hash，因為攻擊者每個 binary hash 都不同）
- Windows 原生無法做「特定 parent process 才能執行某 child」規則，要 EDR 或自製 endpoint security 才能 parent-aware
- 個人/consumer：在隔離 VM 或 Windows Sandbox 才開 untrusted repo

**這個漏洞的「更大訊號」**
- AI 公司要求使用者授予前所未有的存取（code、repo、terminal、secret、autonomous action），但「信任應該靠行為賺來、不是靠生產力換來」
- Bug bounty pipeline 在 AI 時代被「novel finding 爆量」淹沒，傳統 triage 機制正在失效
- Cursor 的靜默被懷疑可能跟 SpaceX 收購、業務重心轉移有關——但對把 production code、credentials、IP 託付給 Cursor 的企業來說，這不是藉口

## 3W1H 分析

- **What（做了什麼/主題）**:
  Mindgard 公開揭露 Cursor IDE 一個被忽略 7 個月、影響所有 Windows 桌面使用者的 workspace-relative path hijacking 漏洞。Cursor 在解析 git binary 時把 repo root 當成可信任搜尋路徑，導致攻擊者只要在 repo 內放一支惡意 `git.exe`，就能在使用者開啟專案時自動 RCE，且 IDE 會週期性重複執行。
- **Why（為什麼重要）**:
  Cursor 不是普通 IDE——它代表「AI agent 寫程式碼、自動改檔、跑指令」的新一代開發環境，使用者託付的不只是 source code，還有 API key、production credentials、proprietary IP。對主人而言，這直接命中兩個面：第一，主人在 Mac 上若曾經把工作交給 Cursor 跑、或在 agent teamworkflow 內裝了 Cursor，攻擊面已經打開；第二，這個 case 揭示了當 AI 公司估值膨脹、bug bounty 流程超載、vendor 收購/業務轉移時，「回報沒人理」是常態而非例外——這對主人日後評估 AI 工具、寫 agentic 系統時的 trust model 是關鍵教材。
- **How（如何運作/實作）**:
  - 攻擊前置：攻擊者提交一個 PR / 範例 repo / open source package，內含 root 目錄的 `git.exe`
  - 觸發：Windows 使用者用 Cursor 開啟該專案 → IDE 內部呼叫 `git rev-parse --show-toplevel` 解析路徑時，把 repo 內的 `git.exe` 當成 git binary 執行
  - Payload：惡意 `git.exe` 以當前使用者權限跑任意 code（cal collector、persistence、credential theft、AI agent manipulation）
  - 偵測難點：因為 IDE 會週期性重新呼叫，process tree 看似「Cursor.exe 啟動 git.exe」，但 git.exe 路徑在 workspace 內，EDR 預設信任 IDE 子行程時幾乎不會告警
  - 防禦：path-based allowlist 限制 git binary 來源；VM/sandbox 開 untrusted repo；對 AI agent IDE 啟用 EDR 對「workspace 路徑下被執行的 binary」獨立告警
- **Insight（個人心得）**:
  這篇讓咱最在意的是「漏洞的 trivial 程度 vs vendor 修復時間」的極端不對稱。一個用 Windows Calculator 改名就能 PoC 的路徑解析 bug，197+ 版本更新都沒人修——這暴露了 AI 公司的 security 投入其實跟估值沒有線性關係。對主人做 agent 設計的啟示是：自己寫的 agent tool 不要假設「執行環境是 trust boundary」——Cursor 把 workspace 當信任來源,正是這種假設翻車。咱建議主人若有對外釋出/接受外部 repo 的 agent 工具,把「binary resolution paths」「subprocess search paths」列入 threat model 必審項目;另外 Mindgard 質疑「bug bounty pipeline 被 AI finding 淹沒」這點也值得主人日後評估自家 threat intel 流程時,不要假設 vendor 一定會在 SLA 內處理報告——尤其是 7B+ 估值、產品快速迭代的公司。