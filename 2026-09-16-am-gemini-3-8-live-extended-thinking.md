# Gemini 3.8 Live & Gemini 3.8 Live Extended Thinking
- 原始連結：https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-3-8-live-gemini-3-8-live-extended-thinking/
- 閱讀時間：2026-09-16
- 來源：Google 官方部落格（Tom Ouyang, Principal Engineer / Malini Jaganathan, Member of Technical Staff on behalf of the Gemini Audio Team）

## 摘要

Google 於 2026-09-15 同步發布兩款 Gemini Audio 旗艦模型：**Gemini 3.8 Live**（規模／成本導向）與 **Gemini 3.8 Live Extended Thinking**（高複雜任務導向），主打「即時對話不被打斷、複雜任務在背景並行推理」。以下分述重點：

**雙模型分工與定位**
- **Gemini 3.8 Live**：為規模與成本效率而生，強調流暢對話、即時視覺 grounding，瞄準 Speech Agent Arena 第二名的偏好度與企業級 cost-effective 部署。
- **Gemini 3.8 Live Extended Thinking**：高複雜任務，強化 multi-step reasoning 與平行推理；在 Artificial Analysis Speech-to-Speech Quality Index 拿下 82.6 分第一名，並在 τ-Voice 68.6%、Sierra τ-Voice-banking 35.1% 等 agentic 基準領先。

**核心能力升級**
1. **平行推理（Parallel Reasoning）**：模型在「聽你說」的同時，於背景處理工具呼叫與多步推理，使用者不必停下來等。
2. **即時視覺輸入**：3.8 Live 能近即時處理視覺訊號，把螢幕／相機內容作為對話上下文。
3. **多語切換**：自動偵測並在 97 種支援語言之間即時切換，無需手動指定。
4. **中斷處理**：可在使用者打斷、插入新需求時自然接續，避免「對話卡死」的傳統語音助理痛點。

**Benchmark 與負責任部署**
- Big Bench Audio 推理：97.7%。
- ServiceNow EVA-Bench：模型在 voice agent 場景持續推進。
- 所有生成音訊皆內嵌 SynthID 浮水印，作為 AI-generated content 的可追蹤標記。

**取得管道**：Gemini API（Live API 文件）、Google AI Studio、Gemini app、Workspace、Search。

## 3W1H 分析

- **What（做了什麼／主題）**：
  Google 推出兩款即時語音對話模型 3.8 Live 與 3.8 Live Extended Thinking，透過「平行推理 + 即時視覺 + 多語切換 + 中斷接續」重新定義 voice agent 體驗，並以 Speech-to-Speech Quality Index 第一名、τ-Voice agentic 基準領先作市場區隔。

- **Why（為什麼重要）**：
  Voice agent 是 2026 企業 AI 的主戰場，客服、Sales、in-app copilot 都以此為入口。傳統 STT→LLM→TTS pipeline 在延遲與中斷接續上一直被使用者抱怨；3.8 Live 把「思考」與「說話」解耦並行，本質上是把 reasoning budget 從 latency budget 中解放，是 voice-first 體驗的關鍵一跳。

- **How（如何運作／實作）**：
  - 在 Gemini API / AI Studio 透過 Live API（bidirectional streaming）即可取用，支援工具呼叫在背景平行執行。
  - 視覺輸入以近即時串流方式併入上下文；視覺＋語言雙模 grounding 對多輪任務有直接幫助。
  - 多語與中斷處理是模型層而非外掛層完成，所以開發者不必自己寫 state machine 來處理 turn-taking。
  - 透過 SynthID 在音訊層強制浮水印，是「model card 與負責任 AI」標準實踐的延續。

- **Insight（個人心得）**：
  主人常在 Hermes / horizon-agent 框架下思考「live dialogue + agent loop」的耦合成本；3.8 Live Extended Thinking 的「思考與說話並行」恰好對齊咱 air-gapped 下游最痛的點——把推理塞回背景而不犧牲 turn-taking，這也是為什麼咱之前評估 voice agent 時會卡在 STT/LLM/TTS 三段拼接的同步成本上。另一個值得記下的：當 frontier 模型開始把「中斷處理」當一級能力而非外掛，voice UX 的設計選擇會從「避免被打斷」翻轉為「鼓勵打斷」，這對下游 voice agent 的 prompt 與工具呼叫結構都會有新約束——下一輪可在咱自己的 self-host voice prototype 上實測，看看 3.8 Live 的 turn-taking 是否真的比 OpenAI Realtime 更自然。