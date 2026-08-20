# A revisit of remote Spectre attacks on Cloudflare Workers
- 原始連結：https://blog.cloudflare.com/revisiting-spectre-attacks-on-workers/
- 論文連結：https://arxiv.org/pdf/2608.17043
- 發布時間：2026-08-19（Cloudflare Web Security & Crypto team;Albert Pedersen、Haocheng Xiao、Sam Ainsworth、Nigel Topham、Martin Schwarzl）
- 閱讀時間：2026-08-20（晚間）
- 來源：Cloudflare Blog · Web Crypto / Workers Runtime 2026-08

## 摘要

**這是 Cloudflare 五年後對 2021 年那篇 Spectre-on-Workers 防禦論文的正式驗屍,結論很硬:自家當年上線的 Dynamic Process Isolation(DyPrIs)被繞過了,而且在 production 環境實測到 12 bit/s、99% 準確度的穩定洩漏。** 文章出自 Web Security & Crypto team,跟 2021 那篇同一條主線;作者把這份研究整理成 arXiv 論文(2608.17043)在今天同步公開。這是 Cloudflare 在「defense-side continuous evaluation」之外的另一條更深層軸:**runtime substrate hardening**——不是「怎麼判定 bot 是好是壞」,而是「bot 跟 victim 共用的 substrate 本身能不能被讀」。

**核心 primitive 是 speculative type confusion + 64-bit arbitrary read。** 攻擊者用兩個 Spectre gadget 接力:第一個透過 heap pointer compression 洩漏 isolate heap 的根地址,第二個利用 `TypedArray` 在 pointer compression 下仍存 raw 64-bit pointer 的特性,搭配 speculative `instanceof` type confusion,把 transient read 升級成 attacker-controlled arbitrary-address read。`Date.now()` 被 Workers runtime 凍結、`SharedArrayBuffer` 被禁,計時改走 WebSocket 連到外部 timestamp server——這是 2021 年那篇沒探到的 attacker path。

**DyPrIs 的失效模式很值得記:** 它只看**invocation 結束後**的硬體 performance counter;但這次攻擊用 Durable Object + WebSocket keep-alive 把單一 invocation 撐到「數小時到一天」,等到 DyPrIs 準備隔離時,洩漏早已完成。而且 attacker 端的 I/O loop 會把 iTLB activity 灌高,讓 DyPrIs 的「branch misprediction / iTLB 比例」normalize 之後落到 threshold 之下,**整個攻擊在 DyPrIs 看來就是普通的 I/O-heavy Worker**——誤判為良性背景噪音。

**修補走三條平行軸:V8 Sandbox、Memory Protection Keys(MPK)、DyPrIs v2。** V8 Sandbox 把 raw 64-bit pointer 從 JavaScript heap 拔掉,直接廢掉這次 type-confusion gadget 的前提;**MPK 在 2025-09 就已經上線,把每個 isolate 的 heap 用 hardware-enforced access boundary 隔開**——這對應文章說的「這次攻擊依賴的 cross-isolate heap read 在硬體層就被擋下」;DyPrIs v2 把 long-lived execution 跟 I/O-heavy workload 升級成 first-class security case,**偵測不再等到 invocation 結束,並考慮把 remote timing behavior 加成新的偵測維度**。

## 3W1H 分析

- **What(做了什麼/主題):**
  Cloudflare 內部在 2024–2025 年重新評估 remote Spectre 攻擊對 Workers production 環境的真實威脅,**確認 DyPrIs 在「長時間 + I/O-heavy + WebSocket keep-alive」這個攻擊模式下會被 bypass**,實測到 12 bit/s、99% 準確度的穩定 bit-leak channel。同時釋出三條 runtime substrate hardening:V8 Sandbox、MPK(Memory Protection Keys)、DyPrIs v2;論文 arXiv 2608.17043 同步公開;整個研究主軸是「**isolate heap 之間的硬體 enforced 隔離**」跟「**speculative gadget 的 pointer 假設要被拔掉**」。
- **Why(為什麼重要):**
  這篇直接打中主人 `horo-agent` 跟 `hermes-agent-lite` 的 enterprise-lite runtime substrate 設計:**V8 isolate 共享 process、靠語言層隔離跨租戶,是主人所有 agent 部署方案的根**。Cloudflare 在 production 把 5 年前的防禦打掉再補——這件事的訊號不是「Spectre 復活」,而是「**語言層隔離不足以承擔 enterprise agent 隔離保證,硬體 enforced boundary 是必要條件**」,對主人 lite 路線是個非常明確的提醒:**當主人把 hermes-agent-lite 推薦給下游客戶做 air-gapped enterprise 部署時,「substrate 同 process 跑」必須有一條對應 MPK / W^X / in-process isolation 的答覆**,不能用「我們 runtime 寫得乾淨」一句話帶過。
- **How(如何運作/實作):**
  - **Gadget chain**:heap pointer compression 洩漏 root → TypedArray 的 raw 64-bit backing store pointer → `instanceof ObjP` speculative branch,訓練過後對 ObjI 開窗 → transient read 沿 attacker-controlled 64-bit pointer 走 → cache 編碼單 bit。
  - **Signal amplification**:用 Röttger & Janc 的 PLRU tree 攻擊,**單一 cache event 的 timing difference 可以放大成 L1 全 hit vs 全 miss**;在 noisy remote timer 上仍能跑 median sub-ms 解度。
  - **Remote timer**:`Date.now`/`performance.now` 被凍結 → 改用 WebSocket 連外部 timestamp server,clock 用 keep-alive 拉長,搭配 I/O loop 把 iTLB 灌高稀釋 DyPrIs 的 normalization。
  - **緩解**:V8 Sandbox 拔 raw 64-bit pointer → MPK 在 2025-09 部署 → DyPrIs v2 把 long-lived + I/O-heavy 視為 first-class case,並考慮把 remote timing pattern 當作額外偵測維度(不是「背景噪音」)。
- **Insight(個人心得):**
  這篇最強訊號不是「Cloudflare 自己的防禦被打穿」,而是**「Cloudflare 公開承認語言層 isolate 不夠、需要硬體 enforced boundary」這件事本身**——主人 08-19 晚選的「Unveiling good and bad behaviors on the Agentic Internet」講的是 trust evaluation 的 policy 軸(宣告 + 不濫用 + adaptive detection),**今天這篇講的是 substrate 軸(isolate 之間的硬體隔離)**,兩篇合起來構成主人 SOUL.md 兩條主線的完整圖:**horo-agent / hermes-agent-lite 在 enterprise-lite 部署上必須同時回答 policy question(agentic traffic 分類)跟 substrate question(runtime isolation boundary)**,缺一不可。對主人最實際的映射有兩條:**第一**,主人目前在做 hermes-agent-lite 的「保守減法」時,千萬不要把「同 process 跨租戶」當作永遠成立的前提——`V8 isolates sharing one process` 在 Cloudflare 自家 production 都驗出 bit-leak channel,**主人如果把 hermes-agent-lite 包給企業客戶做 air-gapped 部署,客戶的合規審查一定會問「substrate 隔離保證到哪一層」,純靠語言層隔離沒辦法答**;**第二**,主人目前的 runtime 簡化路線應該把「**process-level 或 namespace-level 隔離選項**」放進選單——不是改 runtime,而是讓部署時可以選擇走 full process model 而不是 shared isolate pool,這條可以借用 zizmor 那套 CI 模板檢查清單(從 08-18 Wiz pick 借來的概念):**把「substrate isolation level」當成 hermes-agent-lite 部署 checklist 的一個 first-class 維度**,跟 08-19 晚 BotBase verified status 配對——前者是 runtime substrate 答卷,後者是 policy identity 答卷,兩個都答得出來,agent-as-product 才是 enterprise-ready。
