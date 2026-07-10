# GitHub 專案動態
- 檢查時間：2026-07-10
- 檢查對象：MadsLorentzen/ai-job-search、SmartlyDressedGames/U3-SDK、addyosmani/agent-skills、NVIDIA/personaplex、NousResearch/hermes-agent

## 動態摘要

### MadsLorentzen/ai-job-search（Claude Code 求職框架）
- 類型：commit / pull request
- 內容：無 GitHub release（fork-style 框架，沒有正式版號）；主分支最新三個 commit 全部落在 2026-07-09：`fea59fd`「docs: document complete seen_jobs.json schema including rank fields (#107) (#111)」、`429e32f`「fix(salary): handle missing/null city & resolve custom baseline percentage bug (#98)」、`6a12406`「docs: document freehire-search in README and SETUP (#103)」——三件事都是圍繞「資料 + 評分 + README」，代表框架當下重心是把「如何評估 / 配對職缺」這條鏈路收齊；今天（2026-07-10）開了三個新 PR：#117「Surface interview prep after apply」（應徵後露出面試準備）、#116「Add salary matching regression coverage」（薪資配對加回歸測試）、#115「Add Jobdanmark detail HTML fallback」（丹麥求職站加 HTML fallback），方向是「把 PR / 申請 / 面試準備」這條 user journey 收斂成完整閉環。
- 連結：https://github.com/MadsLorentzen/ai-job-search/pull/117

### SmartlyDressedGames/U3-SDK（Unturned 遊戲引擎 SDK）
- 類型：release / commit / issue
- 內容：最新 release `v3.26.3.4`（2026-07-07）標題是「3.26.3.4 Patch」；主分支最新 commit `ea7b4973`（2022026-07-07）「Remove a dependency on macOS build target (Thanks DiFFoZ and kozak145!)」、`b09ec74a`「Add link to video demo」、`daeb7f7`（2026-07-06）「Add game files」；今天（2026-07-10）三個 open issue：#5519「Pull Requests are missing」、#5518「"Built-in" error when entering the server」、#5514「Performance: reduce main-thread overhead from per-light updates and per-frame water queries」——前兩個是運維 / 構建問題，#5514 是真正的效能 PR material（per-light / per-frame water query 的主執行緒熱點）；總體節奏是「patch 修小坑 + 玩家社群提大型 perf 題」。
- 連結：https://github.com/SmartlyDressedGames/U3-SDK/issues/5514

### addyosmani/agent-skills（生產級 AI coding agent 工程技能集）
- 類型：release / commit / pull request
- 內容：最新 release `0.6.3`（2026-07-03）；主分支最新三個 commit 全部落在今天（2026-07-10）：`6bcfeb9`「Merge pull request #218 from nguyenducthaonguyen/docs/update-cursor-setup」、`3780895`「Merge pull request #368 from mvanhorn/fix/249-docs-document-https-plugin-install-workaroun」、`e6bab79`「Merge pull request #372 from ShiroKSH/fix/eval-validation-windows」——三個 merge 全部圍繞「plugin install 與環境設定」的工程化：Cursor 設定更新、https plugin 安裝 workaround、Windows 上的 eval 驗證；今天（2026-07-10）還同時開出三個新 PR：#380「docs: separate repo orchestration rules from Claude Code platform limits」（把 repo 編排規則與 Claude Code 平台限制分開寫）、#379「refactor(validate-skills): extract lint rules into a shared lib」、issue #378「Enhance performance-optimization: deepen backend/database substance」——三個方向（跨平台文件化、linter 共用化、performance skill 內涵加深）都是把「技能集」往「可被多 agent 複用」推進。
- 連結：https://github.com/addyosmani/agent-skills/pull/380

### NVIDIA/personaplex（NVIDIA 對話式語音基礎模型）
- 類型：commit / pull request / issue
- 內容：無 GitHub release；主分支最新 commit 仍停在 `3428dfd`「Update README.md」（2026-03-02），前兩個是 `052206a`（2026-02-09）、`b62355a`（2026-01-24）Merge PR #18 tsdocode/fix/init-oom——也就是說「過去 4 個月 main 完全沒新 commit」，這是個偏慢節奏的模型倉；今天（2026-07-10）值得觀察的是 PR #100「Add macOS Nix and MLX runtime support」（把模型跑進 macOS Apple Silicon 的 MLX backend）以及 issue #99「No Retention of system prompt post the context window」+#97「The model cannot distinguish its role」——前者是「在 Mac 本機就能跑」的 runtime 擴張，後者兩個 issue 都是「對話角色 / system prompt 維持」的本質缺陷，後者要靠 model 行為改，前者只要靠工程整合。
- 連結：https://github.com/NVIDIA/personaplex/pull/100

### NousResearch/hermes-agent（Nous Research 的 Hermes Agent 框架）
- 類型：release / commit / issue
- 內容：最新 release `v2026.7.7.2`（2026-07-08）「Hermes Agent v0.18.2」；主分支最新三個 commit 集中在今天（2026-07-10）：`f8361d2`「fix(tools): enforce registry result contract (#61787)」、`a0972b9`「fix: widen None-deref guards to config-derived sibling sites + tests」、更前一個 `838aa74`（2026-07-01）「fix(agent): guard .get(key, "").method() None dereference in adapters」——三件事都圍繞「None deref / registry contract」這個錯誤處理的根因：先在 adapters 發現 .get(key, "").method() 會 NPE，再擴大到 config-derived sibling sites，最後升級成 registry result contract 的強制檢查，是個「小 bug → 系統性 contract 強制」的標準演化；今天（2026-07-10）開出 issue #61897「Kanban board shows correct task count in header but renders no cards (archived tasks hidden)」、#61896「Add 'kimi-for-coding-highspeed' to the static model list for 'kimi-coding' provider」、PR #61895「fix(desktop): prevent event loop stalls on Windows cold start」——分別是 UI rendering、新模型掛載、Windows 桌面冷啟動 event loop 阻塞，顯示「桌面 GUI + 多 provider + Windows 相容」三條線同時在被加壓。
- 連結：https://github.com/NousResearch/hermes-agent/pull/61895

## 重點觀察
- 今天的 5 個 repo 拆成兩組：trending top 3（ai-job-search、U3-SDK、agent-skills）都是「人寫給人 / 人寫給 agent」的工具型專案，節奏都偏快；fallback 2 個（personaplex、hermes-agent）一慢一快——personaplex 4 個月 main 沒新 commit 但靠 PR 在擴 runtime，hermes-agent 7/8 才出新版本今天就冒出 3 個「None deref / contract 強制」修補，後者模式才是 AI agent 框架當下真實的成熟路徑。
- 「merge PR bot 驅動 main」是 addyosmani/agent-skills 跟 hermes-agent 的共同特徵：今天 main 上 6 個新 commit 全部是 `Merge pull request #X` 形式——代表兩個專案都走「先 PR 後 main」流程，看活躍度不能只看 main SHA，要看 PR 開關速度；agent-skills 今天 3 個 merge + 3 個新 PR，hermes-agent 3 個 fix + 3 個新 issue/PR，後者的 PR/issue 流量更大。
- 「跨平台 / 跨 runtime 擴張」是當下共同主題：personaplex PR #100 把模型往 macOS MLX 推、hermes-agent PR #61895 解 Windows desktop 冷啟動 event loop、agent-skills 多個 PR 處理 Cursor / Windows 環境的 plugin install——AI 工具要走出「只在 Linux / Mac dev machine 跑」的限制，這三條都在往「多 OS / 多 provider / 多 runtime」收斂。
- 「錯誤處理從 ad-hoc patch 升級到 contract 強制」在 hermes-agent 看得很清楚：`838aa74` 修 adapters 的 None deref → `a0972b9` 把同型 bug 推廣到 config-derived sibling sites → `f8361d2` 升級成 registry 層級的 result contract 強制——這是一個框架成熟的典型三段式，值得當成觀察其他 agent framework 演化的鏡子。
- 今天這 5 個 repo 完全沒有撞到前幾週常見的「內部元件互踩 / 邊界 race」主題——caveman、codex-plugin-cc、page-agent、chrome-devtools-mcp 那些「boundary race / dangling symlink / re-render dispose」的故事暫時沒上演，反倒是「跨平台相容 + 錯誤處理 contract + 多人協作 merge 流程」這種偏「production engineering」的味道更重。
