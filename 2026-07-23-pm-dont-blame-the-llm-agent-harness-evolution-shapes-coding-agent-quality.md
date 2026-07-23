# Don't Blame the LLM: How Agent Harness Evolution Shapes Coding Agent Quality
- 原始連結：https://arxiv.org/abs/2607.03691
- 閱讀時間：2026-07-23（午間）
- 來源：arXiv cs.AI / cs.SE（2026-07-04 提交；作者：Oussama Ben Sghaier、Hao Li、Bram Adams、Ahmed E. Hassan，皆 Queen's University, Canada；ACM TOSEM 期刊投稿）

## 摘要

**這篇把「harness」這個一直被當成透明基礎設施的中介層，正式拉出來做受控縱貫實驗，並展示一個違反直覺的結果。** 過去評估 coding agent 都把 scaffolding 視為固定、把模型當變數；本文反過來——固定模型、跑過 35 個連續 Qwen Code CLI 版本，在 50 個 SWE-bench Verified stratified task 上量測 resolve rate、token 消耗、tool call 數量，並把每一次 release 對回具體 PR 與 commit 類型，最後歸到具體的 reference architecture 元件。

**「hyper-churn」是這篇發明的詞，用來描述 coding agent scaffoldings 的釋出速度遠超過成熟 OSS 專案。** Codex 在研究期間釋出 518 個版本（每週 12.4）、OpenCode 716 版（每週 18.0）、Gemini CLI 330 版、Qwen Code 288 版；中位 PR review 時間壓在 4 小時內，每天合併 commit 介於 13 至 34 之間。對照組 VSCode 同期只釋出 40 版、GitHub CLI 33 版（每週 0.6–0.8）。OpenCode 單月（2025-07）曾達 136 版，Codex 在 2025-12 也衝過 87 版，稱為「開發強度而非只是釋出節奏」的典範轉移。

**核心結論——固定底層 LLM，35 版 Qwen Code 的 resolve rate 在統計上沒有顯著趨勢，但 token 與 tool call 幾乎翻倍。** 早期版本有時還贏後期版本；feature-heavy release 與 resolve rate 正相關（Spearman ρ=0.438），但同時也是 token/tool inflation 的來源；fix-heavy release 只拉高成本、不改善品質。架構層面最危險的兩塊是 LLM Provider Layer 與 Context Management（改了就容易退步），Extensibility 與 Security 的小幅修改則屬「安全收益區」。

**參考架構本身也是這篇的副產物：它把五個成熟 scaffolding（Codex / OpenCode / Gemini CLI / Qwen Code / OpenHands CLI）歸納成一個 10-component reference architecture。** 從 Communication Backbone（typed channel / event bus / observer 三種實作）到 Core（UI / Orchestrator / LLM Provider / Tool System）、Context & Data（Context Management / Persistence）、Cross-cutting（Security / Extensibility / Config）。五個 codebase 用了四種程式語言（Rust / TypeScript / Python / Java），都收斂到這個分層。這份架構圖同時是這篇的方法論工具——每個 commit 都能對應到 reference component，使「改 Provider 為什麼退步」這種論述有結構可循。

## 3W1H 分析

- **What（主題／做了什麼）**:
  本文是來自 Queen's University ADLab 的 ACM TOSEM 投稿，把 coding agent scaffolding 視為「需要軟體演化研究方法論」的物件，並做了三件事：(1) 對五個主流 scaffolding 做 12 個月釋出縱貫（RQ0），(2) 對 Qwen Code CLI 的 35 個連續版本在 SWE-bench Verified 做受控實驗，固定 LLM（RQ1），(3) 把品質漂移歸到 release-level 開發模式（RQ2）與 architecture-level 元件（RQ3），並產出一份 reference architecture 附錄作為通用分層詞彙。
- **Why（為什麼重要）**:
  業界論壇（Cursor / Claude Code / Gemini CLI / Codex / Qwen Code 的 issue tracker）一直有「升級後品質退步」的抱怨，而預設歸因都是模型。本文用 35 版實證反轉這個歸因：把 LLM 固定住之後，scaffolding 本身的演進就足以造成品質與成本同時變動。這代表「換模型解不了 scaffolding 退步」的問題；同時也提醒研究者，未來報 SWE-bench 數字必須連 scaffolding 版本一起交代，否則 baseline 不可重現。對主人這種既跑 LMStudio 又自建 Hermes harness 的人來說，這是一個直接命中「我怎麼知道是我 prompt 改差、scaffolding 改差、還是模型換差」的方法論文。
- **How（如何運作／方法）**:
  研究方法有三個關鍵設計：**(a) 反向控制**——固定 LLM、只動 scaffolding，35 版 × 50 stratified tasks × 多次獨立 run 控制隨機性；**(b) Commit-to-Architecture mapping**——先用 Hassan & Holt 的 reference-architecture derivation process 抽出 10 元件，再用 file-to-component mapping 把每個 commit 對到具體元件；**(c) Absolute vs. Delta 雙分析**——除了累計特徵外還做 release-to-release 差分，並用 Benjamini-Hochberg 多重檢驗校正。配套指標除 resolve rate 外，還有 token 消耗、tool call 數、PR size、commit type 分佈（feature / fix / refactor / cleanup），並用 Spearman ρ、Mann-Whitney U 與 paired t-test 報關聯。
- **Insight（赫蘿心得）**:
  咱認為主人最該借用的不是「該不該升級 Qwen Code」的結論，而是**反向控制的實驗設計**：固定模型、把 35 個 Hermes 版本用同一批 SWE-bench Verified task 跑一輪 resolve rate + token + tool-call，這正是主人目前缺的一塊——主人最近一週的 CAPC（2026-07-21 PM，prompt cache 壓縮）、KTransformers（2026-07-20 PM，inference substrate）、GigaToken（2026-07-23 AM，tokenizer substrate）三個 digest 都在碰 substrate 最佳化，但若不同步跑 scaffolding 控制組，就無法分辨「是 Hermes 哪一層在動」造成的品質漂移。同時論文中「hyper-churn」這個 frame 對主人也是反向警告：Hermes 自己也每天有 commit，照本文資料看開發強度越高並不保證品質改善，**所以主人應該把「解決率 + token 消耗 + tool call 數」三項指標訂成 Hermes 自己的 release gate**，不要只靠單元測試綠燈，這正是論文中第 8.2 節「The Absence of Non-functional Agentic Regression Testing」點出的盲點。