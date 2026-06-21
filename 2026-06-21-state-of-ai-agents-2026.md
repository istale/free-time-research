# The State of AI Agents in 2026
- 原始連結：https://meditations.metavert.io/p/the-state-of-ai-agents-in-2026
- 閱讀時間：2026-06-21

## 摘要

Jon Radoff 統合了 2025 年財報、benchmark 數據、學術文獻與自身建構 agentic 系統的經驗，製作出 200+ 頁的研究報告。本文是他在回顧全貌後的核心觀察。

**核心數據：**
- 2025 年 AI 創投達 $2110 億，佔全球 VC 總額一半；AI 總支出達 $1.5 兆
- 推理成本三年內下降 92%（每百萬 token 從 $30 → $0.10–$2.50）
- Claude Opus 4.6 在 SWE-Bench Verified 達 80.9%，GPQA Diamond 達 91.3%（超越人類專家 21 分）
- 自主任務持續時間：2024 年初約 4 分鐘 → 2026 年 2 月達 14.5 小時（每 123 天翻倍）
- GitHub 已有 4% commit 來自 Claude Code，預計年底破 20%
- Big Tech 2026 年資本支出：Amazon $2000 億、Google $1800 億、Meta $1250 億、Microsoft $800 億

**關鍵洞察：**
1. **瓶頸已從工程轉向想像力**：模型 autonomous task horizon 已超過人類每日工時，瓶頸不再是「能不能做到」而是「應不應該做」
2. **SaaS 結構性崩解**：2026 年初單月蒸發 $2 兆 SaaS 市值，per-seat 定價模型被 agent 取代
3. **GPU 物理約束浮現**：資料中心用電 2026 年達 96 GW（相當於九個紐約市），摩爾定律已被「黃氏定律」取代（AI 算力 2012 年起成長 30 萬倍）

## 3W1H 分析
- What（做了什麼/主題）:
  - Radoff 回顧並整合 2025-2026 年 AI agent 發展全景，包含財報數據、benchmark 演進、硬體擴展軌跡與組織影響
- Why（為什麼重要）:
  - 揭示「只有 6% 組織從 AI 獲得 >5% EBIT impact」與「頂尖用家 6x 產出差距」之間的鴻溝；多數人尚未找到 capture value 的方法，而先行者已看到曲線的走向
  - 推理成本下降 92% 是 phase transition，讓 agentic workflow 從奢侈品變成標配
- How（如何運作/實作）:
  - 分析框架：從 Pioneer Era → Engineering Era → Creator Era 的創意產業演進路徑；現在進入 Creator Era，瓶頸是 imagination 而非 engineering capacity
  - 硬體加速：「黃氏定律」使 AI 算力每代翻 5x， Rubin Ultra（2027）將比 Blackwell 快 10x
  - 應用現狀：Cursor 24 個月破 $1B ARR、每日 10 萬產品在 AI-native 平台被建立、80% Neon 資料庫由 AI 建立
- Insight（個人心得）:
  - 最衝擊的數字不是 $690 億 capex，而是 autonomous task horizon 14.5 小時——這代表 agent 已經可以在沒有人介入的情況下完成一整個工作日。當系統能工作得比多數人類更久，「人力」作為瓶頸的時代正在結束
  - SaaSpocalypse 不是泡沫破裂，是結構性替換：當一個 agent 可以取代數十個人類軟體授權，per-seat 商業模式就再也撐不住了。這對基礎設施供應商意味著新的採購邏輯