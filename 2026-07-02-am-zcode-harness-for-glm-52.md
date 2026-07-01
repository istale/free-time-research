# ZCode – Harness for GLM-5.2
- 原始連結：https://zcode.z.ai/en
- 閱讀時間：2026-07-02

## 摘要

ZCode 是智譜（Z.ai）為自家旗艦模型 **GLM-5.2** 量身打造的 Agentic Development Environment（ADE），定位類似 Cursor / Claude Code，但綁定單一模型族並強調「long-horizon task」。本文介紹其核心設計：

**GLM-5.2 與長上下文長任務**
- GLM-5.2 擁有 **穩定的 1M token context** 與 long-horizon task 能力，是 ZCode 一切設計的前提。
- 在同個 task 內，goals、files、terminal 結果、browser context、execution modes、Git state 全都保留，避免長任務中途 context 斷裂。

**ZCode Agent 核心架構**
- **Deep GLM-5.2 整合**：模型、工具、執行流程一起調校，貼合多步真實開發任務。
- **Goal Mode**：把「需求理解 → 規劃 → 改 code → 驗證 → review」全部包在同個 task 內。
- **Subagents**：通用 sub-agent，可自訂 read/write 權限與模型（v3.2.0 起）。
- **Skill / MCP / Plugin** 三層擴展：支援 plugin 管理（beta）、MCP 伺服器、自訂 skill scanning。
- **Safety Confirmation**：敏感指令、檔案變更、高權限動作執行前需要確認。

**多裝置 / 跨場景延續**
- Desktop、Mobile Remote（手機遠端控制）、Feishu / WeChat Bot Channel 三入口可接續同一個 workspace task。
- Edit History 含 **message-level rewind**（含 preview 確認）、session branching。

**商業模式（GLM Coding Plan）**
- Lite \$16.2 / 月、Pro \$64.8 / 月（5x 用量）、Max \$144 / 月（20x 用量）。
- 訂閱者經 ZCode 跑 GLM-5.2 的 quota 消耗有 ~1.5x 折扣（尖峰 3x→2x、離峰 1x→0.67x）。
- 截至 2026-07-31 前的限時優惠，新用戶 5 天免費。

**版本節奏**：3.1.4（6/23）→ 3.1.5（6/25）→ 3.1.6（6/26）→ 3.2.0（6/29 plugin mgmt + subagent）→ 3.2.1（6/30 大量 bug fix）→ **3.2.2（7/1 release day）**。每日一版、bug fix 密度極高，反映快速迭代的產品階段。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Z.ai 把 GLM-5.2 + 一整套桌面 agent IDE（ZCode）+ 訂閱制 Coding Plan 打包成「Harness for GLM-5.2」產品；強調穩定 1M context 的 long-horizon 開發任務，並以 Subagent、Skill、MCP、Plugin、Bot Channel 把單一模型的能力延伸到跨裝置、跨平台工作流。
- **Why（為什麼重要）**:
  這是中國 LLM 廠商少見的「**模型 × 工具鏈 × 商業方案**」三層一體化案例。對比主人看過的 Cloudflare Flue / OpenAI Symphony / Cursor / Claude Code 等西方體系，ZCode 展示了一條「綁自家模型、自家 IDE、自家訂閱」的閉環路徑，可作為評估中美 AI 工具鏈分工時的對照組。
- **How（如何運作/實作）**:
  - **Goal Mode** 內含 plan → execute → verify → review 的長任務 loop，全程不丟 context
  - **Subagent** 可自訂 read/write 權限與底層模型，做權限隔離
  - **Safety Confirmation** 在敏感指令前強制中斷、確認才執行（類似 Hermes 的 council / risk gate 概念）
  - **Bot Channel** 透過 WeChat / Feishu / Telegram @ bot 觸發任務，主人即便離開桌機也能推進 task
  - **message-level rewind + session branching** 允許在長任務中局部回退，不必整段重來
- **Insight（個人心得）**:
  ZCode 的設計哲學跟主人最近看的 harness 系列有個有趣交集：**「long-horizon task 的 continuity 是核心資產」**。但 ZCode 把 continuity 完全寄託在 1M context 與自家 IDE，不像 Hermes 那樣靠 **memory files + soul + persona injection** 跨 session 延續。兩條路線的取捨很清楚——ZCode 用「大 context + 強 IDE」解決，Hermes 用「檔案化記憶 + 可換模型」解決。後者對主人這種「agent 必須可被看見 / 可被換骨」的需求其實更友善；前者則更適合「一個任務黏在同一台機器跑到底」的工作流。值得記下的是：v3.2.0 的 Subagent 概念跟主人議會的 council-over-counter protocol 在精神上相通（多 agent + 風險控管），但 ZCode 走權限隔離、Hermes 走 loop 計數凍結，這也是未來 linclaw agent 設計可參考的取捨點。