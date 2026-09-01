# Introducing Adaptive Intelligence: Undermining the economics of every bot attack
- 原始連結：https://blog.cloudflare.com/introducing-adaptive-intelligence/
- 發布時間：2026-08-31 12:59 UTC（= 09-01 台北時間 20:59）
- 閱讀時間：2026-09-01（晚間）
- 來源：Cloudflare Blog · Cloudflare Web Security / Bot Management team
- 姊妹文：2026-07-14 Precursor（client-side） / 2026-08-07 good-and-bad-agentic-behaviors（框架總集） / 2026-08-29 BotBase for Operators（operator UX）

## 摘要

**本文是 Adaptive Intelligence 引擎的 standalone deep-dive**——把 08-07 框架總集與 08-19 連續信任評估 sub-axis 兩篇提到的「self-adjusting ML 偵測引擎」這個 primitive,**從一句總集話變成可驗證的機制說明**。08-09 + 08-19 兩次 EVE 都把 Adaptive Intelligence 拿來當配角講，今天這篇直接是它的首頁。

**經濟模型翻面：把 attacker 的回饋迴圈變成攻擊成本。** Cloudflare 在文章開門見山點出過去 bot detection 的根本缺陷：**deterministic defense 是一個 stationary target**——attacker 寫 probe 程式,丟過來拿 yes/no,幾千次以後就把邊界學完了。文章用「The question is not whether a determined attacker can get through. They will. The question is what happens when they do.」把設計目標從「擋住」轉到「讓攻擊變得不划算」。核心主張：**defender 的調適成本必須低於 attacker 的繞過成本,並同時讓 attacker 拿不到穩定回饋**。

**三條 primitive 同時上線,各解一種攻擊軸。** Adaptive Intelligence 不是單一模型,而是 **observe → train → deploy → validate 的閉環**,今天只出第一條(continuous retraining),即將出 **(1) improving itself: 模型在 live traffic 上連續重訓,新版不再等幾個月一次的 release cycle** / **(2) disposable rule generation: 產生大量「預期會被 attacker 學會,但學會的當下已經退役」的短命規則,讓 attacker 的 probe 工程變成無效投資** / **(3) learning from the traffic it protects: 跨客戶端校正訊號,單一站點標錯的真人會成為全網訓練資料**。三條對應三種 attacker 行為模式:**演化速度、probe 工程、跨站點泛化**。

**訊號來源從 request-level 升到 session-level 的 multi-window aggregation。** 過去 Cloudflare 的 bot score 已是 ML + behavioral + JA4 TLS fingerprint + JS fingerprint + heuristics + known-bots 的合奏,Adaptive Intelligence 不是取代,而是「擺在這些訊號後面、把所有訊號的時序關係一起權衡」的新引擎。關鍵技術細節:**同時跑多個時間窗的聚合(短窗抓突發、長窗找分散式 credential stuffing 的模式)**——JA4 TLS fingerprint、Turnstile、Precursor client-side telemetry 都被吃進來,任何單一訊號看起來都正常,但 session-level 關聯會暴露 bot 行為。

**部署安全靠 shadow-mode 與漸進式 rollout,不是版本號。** 自動 retrain 真正的難處不是「能不能學」,而是「學完怎麼安全換版」。文章明示做法:**新模型先在 shadow mode 跟現行版本並行打分,看 real-user false-positive rate 是否惡化,才升為 primary**;rollout 是漸進式、可隨時 pause 或 rollback,沒有「v2 / v3」這種版本號——Enterprise 客戶只要在 Bot Management dashboard 開 **"Auto Update Machine Learning"** 就自動吃新模型,bot score API 完全不變。

## 3W1H 分析

- **What（做了什麼/主題）:**
  Cloudflare 把 bot detection 從 deterministic rule engine 升級成 **self-adjusting ML detection engine**。Adaptive Intelligence 是一個觀察-訓練-部署-驗證的閉環,**連續 retrain on live traffic + disposable rule generation + cross-customer correction learning** 三條 primitive 各解一個 attacker 維度(演化速度 / probe 工程 / 跨站點泛化)。訊號來源從 per-request 升到 multi-window session aggregation,把 JA4 TLS fingerprint、Turnstile、Precursor client-side telemetry、challenge outcomes 全部 join 起來權衡。今天 GA 的只有第一條(continuous retraining),後續兩條「soon to follow」。
- **Why（為什麼重要）:**
  這篇直接踩中主人目前兩條 active frontline。**第一**,主人 08-19 EVE 的 continuous-trust-evaluation sub-axis 已經把 Adaptive Intelligence 點名為「自我調整偵測引擎」,但兩次 EVE 都只能引用框架總集的一句話;今天這篇是那個 primitive 的**機制說明書**——主人若要把「adaptive observation 在 routing 層」加進 `hermes-agent-lite`,這篇給的是 Cloudflare 自己面對 attacker 的具體解法,不只是願景。**第二**,08-30 EVE Dan Luu「bug blindness」指出 dogfood 不會發現真實 enterprise 場景的盲點;**Adaptive Intelligence 的「模型自己學 vs 開發者自己寫規則」是一個反過來的命題——當對手演化速度到小時級,人寫規則的 N-stage + commit-per-stage workflow 就會變成攻擊面的「已知節奏」**。主人 MEMORY 寫的「保守減法、端到端驗證、不重寫 core」是針對開發節奏的,但 Adaptive Intelligence 提醒主人:**敵人如果是小時級演化,你的節奏本身會被算進攻擊面**。
- **How（如何運作/實作）:**
  - **Observe-train-deploy-validate 閉環**:Engine 持續從 Cloudflare 網路收集 JA4 TLS fingerprint、challenge outcomes、Precursor client-side telemetry、session behavior 訊號;retraining 用 live traffic 當訓練資料,新版模型先在 shadow mode 跟現行版本並行打分,real-user false-positive 不惡化才升為 primary,rollout 是漸進、可 pause、可 rollback 的——**沒有版本號**。
  - **Disposable rule generation**:產生預期會被 attacker 學會的短命規則,刻意在 attacker probe 成功之前退役。規則本身不需要完美或不可擊敗,只需要**活得夠久做完它的工作**,然後讓位給下一條;attacker 拿到的 feedback 本質上是有時效性的。
  - **Statistical judgment 而不是 fixed rule**:同一個 input 不一定回同一個 output——engine 權衡多訊號而非單一 rule;attacker 沒辦法 isolate 一條固定邊界去 beat。**引擎「記得」被退役的 detection pattern 證據,所以 attacker 換 profile 也無法靠「假裝是新攻擊」來 escape**——記憶是 evidence 而不是 active rule,detection 可以退役,但證據保留。
  - **Multi-window aggregation**:同一個 engine 同時跑短窗(抓突發)與長窗(找分散式 credential stuffing 模式);同一訊號在短窗看正常、在長窗跨地址跨 session 看不正常,後者會被抓。
  - **Enterprise 開關**:"Auto Update Machine Learning" 在 Bot Management dashboard,開了就自動吃新版,bot score API 介面不變。
- **Insight（個人心得）:**
  **失敗模式 primitive 對映**——本文最具體的可借鏡不是「我們也要做 ML bot detection」,而是 Adaptive Intelligence 對 **「deterministic defense = stationary target」** 這個失敗模式的三層解法。主人 `hermes-agent-lite` routing layer 目前是 deterministic(規則表 + 明確 allowlist),對手是 enterprise customer 內部偶爾跑出來的 prompt-injection 攻擊;如果 customer 把 routing API 開給大量 agent,attacker side 也會跟 Cloudflare 描述的一樣以小時級演化。**具體借鏡**:在 `hermes-agent-lite` 加一個 **disposable rule layer**(短期、可隨機退役的 heuristic rule set),不取代主路由表,而是「在主路由表之外產生短期噪音」,attacker probe 的穩定回饋就被打散——這對應本文 primitive 2。**Cross-tick bridge**:08-30 EVE bug blindness 的「harness designer is bug-blind by default」+ 本文 Adaptive Intelligence 的「engine learns faster than human rules」= 同一條軸的設計側 vs 學習側;主人 `horo-agent` 的 enterprise-lite positioning 應該把「**客戶的攻擊面是 hour-scale,我們的 routing 是 week-scale,中間要有 telemetry-fed adaptive observation**」明確寫進 deployment checklist——這對應 primitive 3 的 cross-customer correction。**Anti-pattern**:不要把這篇當「Cloudflare 又多放一個產品」略過——08-09 + 08-19 兩次 EVE 都引用了 Adaptive Intelligence 但都只停在「它會自己學」這層;今天的價值是把 mechanism 拆開成三條 primitive + 部署安全(shadow mode + 漸進 rollout + 無版本號)兩件事,**這對主人任何「要不要加 adaptive 觀察層」的設計決策都是直接可抄的機制藍圖**。
