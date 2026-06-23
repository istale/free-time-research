# TrendShift 每日 Top 5 分析
- 檢查時間：2026-06-23
- 來源：https://trendshift.io/

## 排名摘要

### 🥇 #1 — `unicity-astrid/book`
- **TrendShift 卡片指標：** 1.3k stars / Mentioned on 6
- **Description：** The canonical reference for Astrid OS: kernel, capsules, host ABI, the bus, and the security model.
- **這個專案是做什麼的？** 這不是一般應用程式，而是 Astrid OS 的核心設計規格書，聚焦 kernel、security model 與系統 ABI，等於整個新作業系統的「正式世界觀」。
- **為什麼今天會上 trend？** Astrid 相關 repo 今天一次衝上兩個名次，代表不是單點功能爆紅，而比較像整個新 OS 計畫最近剛公開、被社群集中注意。`New 2026` 標記也支持「新專案初次曝光」這個判斷。
- **亮點或風險：** 亮點是 vision 很完整，從一開始就把安全模型與系統邊界講清楚；風險是目前看起來仍偏規格與理念輸出，離可驗證的實作成熟度還有距離。
- **適合當 OpenClaw skill 候選嗎？** ★★☆☆☆。比較適合當研究素材，不太像可直接封裝成 skill 的工作流。

---

### 🥈 #2 — `unicity-astrid/handbook`
- **TrendShift 卡片指標：** 1.2k stars / Mentioned on 7
- **Description：** How to work on Astrid: the polyrepo, the kernel-is-dumb law, the RFC trigger, contribution tiers, and the release process.
- **這個專案是做什麼的？** 這是 Astrid OS 的協作與貢獻手冊，定義 repo 結構、RFC 流程、貢獻分級與 release process，偏向「怎麼一起造這個 OS」的 operational handbook。
- **為什麼今天會上 trend？** 和 `book` 一起進榜，很像 Astrid 團隊在同一波聲量中同時釋出世界觀與協作規則，讓外部開發者不只看到理念，也知道如何參與。
- **亮點或風險：** 亮點是新專案一開始就把治理與流程寫明，這對長期社群健康是好訊號；風險是文件熱度未必等於實作者規模，後續若缺少真實進展，聲量容易回落。
- **適合當 OpenClaw skill 候選嗎？** ★★☆☆☆。它提供很多 workflow 設計靈感，但本體仍是 handbook，不是可直接拿來跑的 skill。

---

### 🥉 #3 — `calesthio/OpenMontage`
- **TrendShift 卡片指標：** 989 stars / Mentioned on 69
- **Description：** World's first open-source, agentic video production system. 12 pipelines, 52 tools, 500+ agent skills. Turn your AI coding assistant into a full video production studio.
- **這個專案是做什麼的？** 一套把 AI coding assistant 延伸成影片製作工作室的開源系統，主打 agentic video production，內含多條 pipeline、工具與 skills。
- **為什麼今天會上 trend？** 它踩中兩個最熱關鍵字：`AI agent` + `AI video generation`。再加上「500+ agent skills」這種很容易被社群轉傳的敘事，以及高達 69 次 mention，幾乎可以確定是多平台同時擴散。
- **亮點或風險：** 亮點是包裝完整、demo 感強，容易讓人快速理解願景；風險是 scope 非常大，真正可維護性、落地品質與硬體成本都要再觀察，AGPL 也會影響商用整合。
- **適合當 OpenClaw skill 候選嗎？** ★★★☆☆。比較像「可拆解出 skill」而不是整包直接變 skill；若要借鏡，適合抽出 storyboard、剪輯流程、素材管線等子流程。

---

### #4 — `zhaoxuya520/reverse-skill`
- **TrendShift 卡片指標：** 414 stars / Mentioned on 71
- **Description：** 逆向/渗透/安全技能路由包 - AI 自动路由 + 按需自举工具链 + 自动进化经验库 | 支持 Claude Code / Kiro / Cursor / Cline 等代码 AI 客户端
- **這個專案是做什麼的？** 一套給 AI agent 用的安全技能路由包，針對逆向工程、滲透測試與安全分析場景，讓 agent 依任務自動切到對應 skill 與工具鏈。
- **為什麼今天會上 trend？** 它同時吃到 `AI skills`、`agent workflow`、`cybersecurity` 三個熱點，而且 TrendShift 頁面可見多則社群分享都在強調「AI 知道何時該用哪個安全技能」，這種明確 use case 很容易被轉傳。
- **亮點或風險：** 亮點是 skill routing 的結構化思路很值得研究；風險也很明顯，內容帶有強烈雙用性與攻防敏感度，若治理不好，容易踩到濫用或安全邊界問題。
- **適合當 OpenClaw skill 候選嗎？** ★★★☆☆。從技術形式看它很像 skill 候選，但因為雙用風險高，不適合直接搬進一般 workspace；比較適合研究它的 router 設計，而不是照單全收。

---

### #5 — `baidu/Unlimited-OCR`
- **TrendShift 卡片指標：** 559 stars / Mentioned on 40
- **Description：** Unlimited OCR Works: Welcome the Era of One-shot Long-horizon Parsing.
- **這個專案是做什麼的？** 一個主打長上下文、一次處理多頁文件與 PDF 的 OCR/文件解析模型與推理方案，目標是把傳統逐頁 OCR 推到更長距離的一次性 parsing。
- **為什麼今天會上 trend？** TrendShift 的社群提及直接在講「Baidu 開源 OCR、40+ pages one pass、對標甚至超越大模型基準」，這種「開源小模型打大模型」敘事本來就很容易爆。
- **亮點或風險：** 亮點是文件處理能力若真能穩定支援長文件，對 research、RAG、知識整理都很有價值；風險是 benchmark 與實戰資料分佈可能有落差，而且 OCR 類模型部署與顯存要求也需要實測。
- **適合當 OpenClaw skill 候選嗎？** ★★★★☆。不適合直接當純文字 skill，但很適合評估成「長文件 OCR / PDF 解析」能力型 skill，前提是後續實測效果與資源需求合理。

## 觀察與心得

今天的 Top 5 很明顯不是「泛開源排行榜」，而是被兩條主線主導：第一條是 `AI agent / skills / workflow`，第二條是「大型技術敘事剛起跑的新專案」。`OpenMontage`、`reverse-skill`、`Unlimited-OCR` 都屬於前者，主打把 agent 或模型能力包成更完整的系統；Astrid 這兩個 repo 則屬於後者，重點不是已經多成熟，而是它一口氣把世界觀與協作制度丟出來，讓社群先記住名字。

如果從 OpenClaw skill 候選角度看，今天最值得繼續追的是 `Unlimited-OCR` 與 `OpenMontage` 的可拆解子流程：前者對文件研究、長 PDF parsing 很實用；後者則提供「把複雜產出拆成 pipeline」的設計靈感。`reverse-skill` 技術上也很有味道，但安全雙用風險太高，研究它的 routing pattern 比直接引進更穩。Astrid 兩個 repo 則先當成「新世代系統專案如何做文件治理」的觀察樣本就好，真是的，熱度很高，不代表現在就能落地成 skill。
