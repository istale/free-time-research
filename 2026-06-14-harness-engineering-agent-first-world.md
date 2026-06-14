# Harness engineering: leveraging Codex in an agent-first world
- 原始連結：https://openai.com/index/harness-engineering/
- 閱讀時間：2026-06-14

## 摘要

我今天讀的是 OpenAI 的技術文章 [Harness engineering: leveraging Codex in an agent-first world](https://openai.com/index/harness-engineering/)。這篇文章最值得看的地方，不是單純宣告「agent 能寫很多程式」，而是很具體地拆解：當團隊決定讓 agent 成為主要執行者後，工程系統本身要怎麼重構。

文章描述他們從空白 repo 開始，用 Codex 建出一個內部產品，而且強調「0 行人手寫程式碼」這個約束。真正關鍵不在於炫技，而在於這個約束逼團隊把注意力從「人怎麼補洞」轉成「環境還缺什麼能力，才能讓 agent 穩定完成工作」。也因此，工程師的角色被重新定義成設計腳手架、拆解任務、建立回饋迴路，以及持續把隱性知識轉成 agent 看得懂的倉庫內文件與工具。

我覺得最有價值的核心觀念，是「讓 agent 看得懂」比「塞更多規則給 agent」重要。文章明確反對把所有規範都堆進單一大型 `AGENTS.md`，而是把 `AGENTS.md` 當成目錄，再把真正的系統知識分散到結構化、可驗證、可版本化的 `docs/` 內。因為對 agent 來說，看不到的知識就等於不存在；如果架構決策、產品原則、技術債與執行計畫只留在 Slack、Google Docs 或人腦裡，agent 就無法穩定地推理與延續這些脈絡。

另一個很實戰的部分，是他們把 UI、logs、metrics、traces 都做成 agent 可直接操作的環境。Codex 不只改 code，還能透過 Chrome DevTools 驅動介面、讀 observability stack、重現 bug、驗證修復，甚至長時間自主執行單一任務。這代表 agent 能力的提升，不只靠模型本身，而是靠你願不願意把驗證面、診斷面、回饋面都工程化成它可讀可寫的系統。

文章也提醒了一個很容易被忽略的現實：agent throughput 一高，團隊的 merge 哲學、架構約束與清理策略都得跟著變。OpenAI 的做法是用強架構邊界、客製 linter、結構測試與固定的「golden principles」持續做垃圾回收，避免 agent 把 repo 內現有的壞模式複製擴散。換句話說，agent-first 開發不是放飛自動化，而是用更嚴格的系統設計去換取更高的產出速度。

## 3W1H 分析
- What（做了什麼/主題）:
  OpenAI 分享他們如何以 Codex 為主要執行者，從零打造內部產品，並總結 agent-first 工程流程中的 repo 設計、驗證回路、架構治理與長期維護方法。
- Why（為什麼重要）:
  多數人談 coding agent 時只看模型能力，但真正決定能不能進入正式研發流程的，是 repo 知識結構、驗證可觀測性、機械化約束與清理機制。這篇把這些關鍵條件講得很清楚。
- How（如何運作/實作）:
  以短 `AGENTS.md` 作為入口，把系統知識放進版本化文件；讓 agent 直接使用標準開發工具、Chrome DevTools 與 observability stack；再用 custom linter、結構測試、背景清理任務與 execution plans 維持一致性與可演進性。
- Insight（個人心得）:
  這篇最值得抄的不是「全自動寫碼」口號，而是「agent legibility」這個設計原則。真是的，很多團隊會把 agent 當超強實習生，但這篇更像是在提醒你：如果 repo 本身不夠可讀、可驗證、可約束，再強的 agent 也只是在混亂系統裡加速製造混亂。
