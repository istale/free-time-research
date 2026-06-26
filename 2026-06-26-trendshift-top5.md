# TrendShift 每日 Top 5 分析

- 檢查時間：2026-06-26
- 來源：https://trendshift.io/

## 排名摘要

> 備註：以下 `TrendShift 當日 mention 數` 取自 2026-06-26 的 Daily 排名頁卡片；`GitHub 總 stars` 取自各專案在 TrendShift 的 repository 詳情頁。至於「為何上榜」屬快速推測，不是官方說明。

---

### 🥇 1. google-labs-code/design.md
- **描述：** A format specification for describing a visual identity to coding agents. DESIGN.md gives agents a persistent, structured understanding of a design system.
- **GitHub 總 stars：** 19.8k
- **TrendShift 當日 mention 數：** 30
- **TrendShift 卡片動能值：** 785
- **是做什麼的？** 提供給 AI coding agent 使用的設計規格格式，讓 agent 能讀懂品牌色、字體、元件語言與視覺約束，等於把設計系統寫成機器可理解的結構。
- **為何今天會上 trend？** 很可能是因為 AI coding agent 正在從「能寫功能」往「能做完整產品」演進，而 `DESIGN.md` 剛好補上設計語意層。再加上它在 2026-06-25 曾登上 GitHub Trending 第 1，熱度延續到今天很合理。
- **亮點與風險：**
  - ✅ 標準化敘事很強，容易被其他 agent 工具鏈引用
  - ✅ Google Labs 背景讓它更容易成為生態共識
  - ⚠️ 若後續缺少主流工具原生支援，可能停在「概念被稱讚、實作沒跟上」
- **適合當作 OpenClaw skill 候選嗎？** ✅ 很適合。可研究做成「讀取 / 套用 DESIGN.md 規範」的 skill，幫 OpenClaw 在前端或品牌輸出時更穩定。

---

### 🥈 2. xbtlin/ai-berkshire
- **描述：** AI-era Berkshire: a value investing research framework built on Claude Code. 4 masters' methodologies + multi-agent adversarial analysis.
- **GitHub 總 stars：** 2.2k
- **TrendShift 當日 mention 數：** 62
- **TrendShift 卡片動能值：** 592
- **是做什麼的？** 這是一套把價值投資研究流程 agent 化的框架，試圖把 Buffett、Munger 等投資方法論轉成可由 Claude Code / 多 agent 協作執行的研究管線。
- **為何今天會上 trend？** 研判是「AI agent + 金融決策框架」這個題材很吸睛，加上它的敘事很完整，不是單一 prompt，而是一整套研究方法學封裝。
- **亮點與風險：**
  - ✅ 題材鮮明，容易吸引想做 agentic research 的開發者
  - ✅ 多 agent 對抗式分析很有觀摩價值
  - ⚠️ 金融研究結果若被誤當投資建議，風險很高
  - ⚠️ 方法論很依賴 prompt 品質與資料來源，落地時可能不穩
- **適合當作 OpenClaw skill 候選嗎？** ⚠️ 可參考，但不建議直接照搬。比較適合拆出「多 agent 研究報告 workflow」的 skill，而不是做投資建議 skill。

---

### 🥉 3. mauriceboe/TREK
- **描述：** A self-hosted travel/trip planner with real-time collaboration, interactive maps, PWA support, SSO, budgets, packing lists, and more.
- **GitHub 總 stars：** 6.9k
- **TrendShift 當日 mention 數：** 16
- **TrendShift 卡片動能值：** 307
- **是做什麼的？** 自架型旅行規劃平台，整合地圖、預算、行李清單、協作編輯等功能，定位是「旅行版的 Notion + 地圖 + 行程排程」。
- **為何今天會上 trend？** 一方面旅遊工具本來就容易被分享，另一方面它功能面完整、demo 明確，對想自架生活工具的人很有吸引力。
- **亮點與風險：**
  - ✅ 問題定義清楚，產品完成度看起來高
  - ✅ 自架、PWA、協作這幾個關鍵字很容易吃到社群流量
  - ⚠️ 功能面寬，後續維護成本高
  - ⚠️ 若沒有行動端體驗優勢，實際使用頻率可能不如想像
- **適合當作 OpenClaw skill 候選嗎？** ⚠️ 偏中低。比較像完整產品，不是 skill 本體；但其「旅行規劃資料結構」值得參考，對 Master 的大阪行程類任務有啟發。

---

### 4. baidu/Unlimited-OCR
- **描述：** Unlimited OCR Works: Welcome the Era of One-shot Long-horizon Parsing.
- **GitHub 總 stars：** 9.2k
- **TrendShift 當日 mention 數：** 62
- **TrendShift 卡片動能值：** 940
- **是做什麼的？** OCR / 文件解析專案，主打長文件、長上下文的一次式解析能力，目標是把複雜文件更完整地轉成可被模型使用的結構化內容。
- **為何今天會上 trend？** 很可能是因為文件理解、RAG 前處理、企業知識庫建設仍是 2026 的熱門剛需，而 `One-shot Long-horizon Parsing` 這個定位很有傳播力。
- **亮點與風險：**
  - ✅ 對 agent workflow 很實用，文件 ingestion 是高頻場景
  - ✅ 背靠百度，技術敘事與資源可信度較高
  - ⚠️ 若 benchmark 與實際資料差距大，口碑容易反噬
  - ⚠️ OCR 類專案常受語言、版面、PDF 品質影響，泛化能力要實測
- **適合當作 OpenClaw skill 候選嗎？** ✅ 值得關注。可延伸成「長文件解析 / OCR 清洗 / 結構化輸出」相關 skill。

---

### 5. inkeep/open-knowledge
- **描述：** Beautiful, AI-native markdown editor and LLM Wiki
- **GitHub 總 stars：** 451
- **TrendShift 當日 mention 數：** 9
- **TrendShift 卡片動能值：** 233
- **是做什麼的？** 一個面向 LLM 工作流的 markdown 編輯器與 Wiki，強調 AI-native 的知識整理體驗，而不是傳統文件編輯器再硬塞 AI。
- **為何今天會上 trend？** 應該是因為「AI 原生知識管理」這個定位很討喜，而且編輯器 / Wiki 類工具天然適合在 X 上展示介面與工作流。
- **亮點與風險：**
  - ✅ 切中「知識庫要不要重新為 LLM 重做一遍」這個題目
  - ✅ 若 UX 真好，容易形成忠誠社群
  - ⚠️ 目前 GitHub 規模還小，是否能持續活躍要觀察
  - ⚠️ 編輯器賽道很擠，和 Obsidian / Notion / 各種 AI notes 工具競爭激烈
- **適合當作 OpenClaw skill 候選嗎？** ⚠️ 本體不太像 skill，但其知識編排思路很值得研究，尤其是「給 agent 用的 Wiki 結構」。

---

## 觀察與心得

**🎀 琴里觀察：**

今天的 TrendShift Top 5 很明顯分成兩條線：

1. **AI agent 基礎設施 / 工作流層**：`design.md`、`ai-berkshire`、`Unlimited-OCR`、`open-knowledge` 都不是單點玩具，而是在補 agent 真正落地時缺的那幾塊：設計規格、研究流程、文件解析、知識編排。
2. **生活型垂直產品仍然有市場**：`TREK` 不是 agent 工具，但它證明「完成度高、問題清楚」的產品，照樣能在一堆 AI 專案裡殺進前排。

如果從 OpenClaw skill 候選角度看，今天最值得追的是兩個方向：

- **`design.md`**：非常適合轉成讓 OpenClaw 理解視覺規範的 skill。
- **`Unlimited-OCR`**：適合延伸成長文件解析 / 知識清洗 skill。

真是的，今天榜單表面上很花，底層其實都在講同一件事：**大家開始不是只想要 AI 會聊天，而是要它真的能接手工作。**
