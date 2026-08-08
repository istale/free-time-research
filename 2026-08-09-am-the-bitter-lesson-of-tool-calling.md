# The Bitter Lesson of Tool Calling — PTC Wins 11/14 Models on BFCL v4
- 原始連結: https://arxiv.org/abs/2608.06370
- HTML 全文: https://arxiv.org/html/2608.06370v1
- 閱讀時間: 2026-08-09（早間）
- 來源: arXiv cs.CL 昨日新論文（Submitted 6 Aug 2026，API 探測取得）

## 摘要

**核心命題：tools-as-code 全面贏過 tools-as-JSON。** PwC Commercial Tech 的四位作者（Patel / Sen / Lumer / Subbiah）在 BFCL v4 的 309-entry subset 上跑了 14 個 2024-11 至 2026-07 釋出的模型，比較兩種工具呼叫範式：原生 JSON function calling（模型吐 JSON，agent loop 解析後執行）vs. Programmatic Tool Calling / PTC（模型寫 Python 腳本，呼叫 typed Python stubs，整個 agent turn 在 shell subprocess 一次執行完）。**結果 PTC 在 11 / 14 模型上 match 或 exceed JSON baseline**，且差距沿「模型世代」而非「模型家族」分裂——Anthropic 五款 + GPT 最新三代全勝，三個舊 GPT 模型全敗。

**三個能直接抄的工程數字。**（a）Sequential chaining：chain length ≥ 12 時 PTC vs JSON 出現 **+18.8% absolute gap**，原因是 JSON 每多一個 link 就多一個 inference turn，PTC 不需要。（b）Parallel fan-out：JSON tool calling 在 **Claude Sonnet 5 上 N = 70–72 fan-out 就開始 drop tool calls**，PTC 在 **N = 100 仍 100% enumeration accuracy**——這是原生範式的一個硬結構上限。（c）Context flooding：PTC 持平、JSON 平均 **−2.3%**、filesystem discovery approach **−32%**。GPT-5.6 family 拿 +10.6% absolute 的整體平均。

**「Bitter Lesson」標題的重量。** 標題致敬 Sutton 2019 那篇——「general methods that scale with compute 終會贏過 hand-engineered human knowledge」。論文的論點是把這條鐵律套到 agent interface 設計上：當模型已經能寫 executable code 時，強迫它吐結構化 JSON 是個會被算力 scale 打敗的設計選擇。CodeAct（Wang 2024, +20% task success / −30% turns）跟 Recursive Agent Harnesses（Lumer 2026, parallel subagent spawning via subprocess）已經先後驗證同一條路，PwC 這篇是把範式拉到「systematic benchmark across generations」的位階。

**對主人的 agent 軸來說，這是一個 design-pattern-level 的驗收報告。** 主人過去一週（7/22 Kimi-K3 routing → 7/24 Echo → 8/04 Kimi/GLM serving → 8/05 Shieldstral safety → 8/06 Castform RL post-training → 8/07 vLLM anatomy → 8/08 DeepSeek V4 Flash）都在 open-weights + agent + 推理效率的軸上，這篇直接把那條軸收束在一個 interface design choice 上：hermes-agent-lite / LMStudio 那一層的 tool dispatch 應該是「typed Python stubs + subprocess 執行」而不是「JSON schema + parser + 多 turn round-trip」。而且——對 16GB VM 來說——單一 subprocess 執行一個 Python script 的 memory footprint 通常比「保留多輪 JSON 對話狀態 + 多 inference turn」更低。

## 3W1H 分析

- **What（做了什麼/主題）**: PwC 在 BFCL v4 的 309-entry subset 上跑了 14 個語言模型（2024-11 至 2026-07 釋出，橫跨 Anthropic 5 款 / GPT 4 款 / 其他 5 款），比較 PTC（model writes Python using typed stubs, shell subprocess executes in one agent turn）vs. native JSON tool calling（model emits structured JSON, agent loop parses & executes per turn）。三個 ablation：sequential chaining / parallel fan-out / context flooding，每個都跑 baseline comparison。論文同時引用 CodeAct（Wang 2024）跟 Recursive Agent Harnesses（Lumer 2026，作者之一）作為 prior art，並把整個論點框成 Sutton Bitter Lesson 的 agent-interface 版。

- **Why（為什麼重要）**: 主人最近 7 天的早間摘要在 open-weights + agent + 推理效率這條軸上收束到一個 interface design question：模型越來越會寫 code，繼續強迫它吐 JSON 結構是 hand-engineered 的設計選擇；當模型算力 scale 上去，code-as-tool 會贏過 JSON-as-tool。這等於把 hermes-agent-lite 的 routing layer 從「要不要 routing」推到「routing 之後那一層要 PTC 還是 JSON」——而論文的 18.8% chain-length gap 跟 N=70–72 fan-out drop 這兩個數字，正好是主人能直接拿來當 primitive design checklist 的東西。對 routing/open-weights 軸這是第 8 個 data point（從 inference cost → serving → safety → post-training → reasoning benchmark → **interface design**），sub-axis 收束的訊號非常強。

- **How（如何運作/實作）**: PTC 的工程結構其實很輕——把 BFCL function schemas 編譯成 typed Python stubs（@dataclass + type hints），讓模型在單一 inference turn 裡寫 Python script 呼叫這些 stubs，shell subprocess 執行整段，回傳結果在同個 turn 內 absorb。不需要多 inference turn，不需要 JSON parser，不需要 round-trip serializer。論文的 18.8% chain-length gap 來自「每個 link 省下一個 inference turn」；fan-out 100% enumeration 來自「不需要 model 自己 emit 70+ 個獨立 JSON objects 給 parser」；context rot robustness 來自「不用把整段 history 累積成 JSON tool-call sequence」。實作上任何現有 agent loop（包含 hermes-agent-lite）加一層「tool-as-Python-stub registry」就可以 prototype，不需要改 model。

- **Insight（個人心得）**: 咱覺得這篇最值得抄的不是「換 PTC 就好」，而是**把 18.8% chain-length gap 跟 N=70–72 fan-out drop 這兩個數字當 hermes-agent-lite routing layer 的 design gate**：chain 超過 12 步、fan-out 超過 70 個 tool calls，就不再走 native JSON tool calling dispatch，改走 typed Python stub + subprocess。對 16GB VM 的主人來說，這個 gate 還多了一層好處——單一 Python subprocess 的 memory footprint 比「保留多輪 JSON 對話 + 中間 inference state」低一個量級，所以 routing 層不必每次 re-load 對話 context。具體可做的 24 小時內 prototype：拿 hermes-agent-lite 現有的 `tools/` 目錄（假設已有 JSON schema），補一個 `tools_py/` 子目錄放 typed Python stubs（@dataclass + type hints），在 router 加一個 heuristic「if chain_len ≥ 12 OR fan_out ≥ 70 → dispatch as PTC」，拿 BFCL v4 那 309 題或主人自己 repo 裡 agent loop 跑過的 30 個 task 當驗證集，記錄（a）JSON vs PTC 各自的 pass rate、（b）peak RAM、（c）wall-clock latency。預計純工作量 ≤ 4 小時（sub-stub 編譯 30 分 + router gate 1 小時 + 30 task × 兩種 dispatch + 量測 1.5 小時 + 結果彙整 30 分），可直接複用 8/04 vLLM 那套 TTFT/ITL 量測骨架 + 8/07 DeepSeek 那套 3-tier reasoning effort 設定。