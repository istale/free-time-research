# How OpenClaw Works: Understanding AI Agents Through a Real Architecture

- 原始連結：https://bibek-poudel.medium.com/how-openclaw-works-understanding-ai-agents-through-a-real-architecture-5d59cc7a4764
- 閱讀時間：2026-07-01

## 摘要

本文以 OpenClaw 為教學範例，拆解現代 AI Agent 的核心架構。OpenClaw 是一款開源個人 AI Agent 框架，2026 年初 GitHub stars 迅速突破 10 萬，被譽為「Claude with hands」。作者希望透過其清晰、易讀的架構，讓讀者理解所有主流 AI Agent 共享的核心模式：agentic loop、tool use、context injection 和 persistent memory。

文章從 Gateway 層開始介紹——這是整個系統的神經中樞，負責訊息路由、連線管理、認證和 session 追蹤。所有訊息（無論來自 WhatsApp、Telegram 或 Discord）都先經過 Channel Adapter 標準化，轉成統一格式後再進入處理流程。

核心的 Agentic Loop 包含四個階段：Context Assembly → Model Inference → Tool Execution → Streaming Reply。其中 Context Assembly 被強調為最重要的一環——模型沒有眼睛，只能依靠傳入的 context window 工作，因此如何組裝乾淨、一致的上下文，直接決定輸出品質。

Tool Execution 是讓 Agent 從「聊天機器人」進化為「能動 agent」的關鍵：當 LLM 輸出工具呼叫請求（如讀取檔案、搜尋網頁、發送郵件），agent runtime 攔截請求、執行工具、將結果回傳給模型，模型再決定下一步——這就是 ReAct loop (Reason + Act)。

Skills 系統則展示了一種優雅的 prompt 工程技術：每個 Skill 是一個資料夾，內含 SKILL.md 檔案，用自然語言描述 agent 如何處理特定領域的任務，agent 可在需要時動態載入這些指令。

## 3W1H 分析

- **What（做了什麼/主題）**：
  以 OpenClaw 開源框架為個案，系統性拆解 AI Agent 的工程架構，包括 Gateway、Channel Adapter、Session Management、Agentic Loop (ReAct)、Tool Execution、Skills 系統和 Memory 機制。

- **Why（為什麼重要）**：
  AI Agent 是 2026 年最關鍵的 AI 應用範式之一，但多數介紹僅停留在「如何使用」，缺乏對底層架構的理解。本文填補這個缺口——一旦理解 OpenClaw 的架構，就能理解幾乎所有現代 AI Agent 的共同模式，對開發、選型和除錯都有直接幫助。

- **How（如何運作/實作）**：
  Gateway 作為單一長期程序，接收各平台訊息後由 Channel Adapter 標準化，再經 Command Queue 序列化（每 session 依序處理以避免狀態衝突）。Agent Runtime 在每次處理中先組裝四層上下文（base prompt + skills + bootstrap files + per-run overrides），再呼叫 LLM。LLM 可能回文字回覆或發出工具請求，工具執行結果會被寫回對話歷史並餵回模型，直到完成回覆。Skills 以 SKILL.md 形式存在，agent 透過動態載入自然語言指令來擴展能力。

- **Insight（個人心得）**：
  文章最讓我印象深刻的不是任何單一技術亮點，而是「concurrency 危險性」這個工程教訓——同一 session 的訊息必須序列化處理，否則會產生狀態衝突與工具輸出衝突。這提醒我，即使在 LLMs 看似「智慧」的表層下，底層系統依然是需要嚴謹並發控制的軟體。對於想投入 Agent 開發的人來說，這是比學會呼叫工具更根本的認知。
