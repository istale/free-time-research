# Unveiling good and bad behaviors on the Agentic Internet
- 原始連結：https://blog.cloudflare.com/good-and-bad-agentic-behaviors/
- 發布時間：2026-08-07 / Cloudflare Web Integrity & Trust team（Jin-Hee Lee, Marina Elmore）
- 閱讀時間：2026-08-19（晚間）
- 來源：Cloudflare Blog · Agents Week 2026 系列

## 摘要

本文是 Cloudflare 在 Agents Week 2026 推出的策略性長文,把「Bot 偵測」這個老問題**從是否阻擋單次請求,重新定位成「continuous trust evaluation on the Agentic Internet」**。作者來自 Cloudflare Web Integrity & Trust team,負責 bots 與 fraud problem space。

**核心框架:把 Risk 與 Trust 拆開來看。** 大多數 bot 解決方案把 Risk 跟 Trust 當作 continuum 的兩端,作者認為這兩者其實是 **independent 但 reciprocal 的軸**:Risk 是「這次 request 有多可能有害」,ephemeral、單點;Trust 卻是要靠 reputation 在時間上累積出來的。文章用「半夜好朋友狂按門鈴」做對照——只看行為是 risk,但只要他是鄰居朋友,你就讓他進;**trust 才是決策的關鍵 ingredient**。

**好行為必須建立在 transparency 上。** Cloudflare 把 BotBase 升級為**不只是「known good bots」,而是「all known bots and agents 的 directory」** —— Verified 的定義被收斂到兩條:**(1) 老實宣告自己是誰** **(2) 不濫用被授予的 trust**。重點是:**壞行為也能被 track**,因為已驗證行為的 baseline 一旦被違反,unverified 狀態會立刻被翻出來——這對 site owner 是新的政策槓桿。

**Precursor 與 Adaptive Intelligence 把偵測升級為 continuous + self-adjusting。** Precursor 是已經上線的 client-side continuous behavior detection(用 cursor trajectory 等 session-level 訊號判定是否為人);24 小時就有 206M evaluation events、跨 73,438 zones 跑。新發布的 **Adaptive Intelligence 是會自己學、自己更新的 ML bot 偵測引擎**——擺脫以往「版本號週期性釋出」的包袱,**因為 bot 演化速度以小時為單位,等不到幾個月一次的大版本**。

**緩解策略分三層:Unpredictability、AI Labyrinth、Queuing for Good Bots。** 為了避免 deterministic response(403 / block)被 attacker 寫 probe 程式逆向,**用隨機化阻止 fingerprinting**;**AI Labyrinth 把 crawler 引入 LLM 生成的 maze,或餵 summary / poison data**;對 legitimate agentic traffic 用 queuing 限制 throughput 而不是直接拒絕。三者刻意分出「給 malicious bot」與「給 legitimate bot」的雙軌。

## 3W1H 分析

- **What（做了什麼/主題）:**
  Cloudflare 把 bot 偵測的策略骨幹做了一次重新表述:從「block / challenge / allow 的瞬間決策」轉向「以 Trust 為主軸、Risk 為輔助」的 continuous evaluation。同時放出三條產品線更新:Precursor(continuous client-side detection)、Adaptive Intelligence(self-adjusting ML 引擎)、AI Labyrinth(Maze / Summary / Poison 三選一的 crawler trap)。BotBase 從 good-bot directory 升級為 all-agents directory,並建立「verified / unverified」雙狀態作為 site owner 的政策介面。
- **Why（為什麼重要）:**
  這篇直接踩中主人正在處理的兩條主軸。**第一**,主人 2026-08-13 EVE 的「Known Agents spoofing ClaudeBot 掃 ~/.hermes/.env」事件已經標出 attacker-side 的問題:**agent-as-attacker 的 fingerprint 越來越難認**;本文恰好給出 defender-side 的最新武器——從 IP / TLS fingerprint 升級到 **continuous behavior signal + adaptive ML**,並明確點出「bot 演化速度以小時為單位,不能再等版本週期」。**第二**,主人正在做的 `horo-agent` / `hermes-agent-lite` 是 agent-as-product;**agentic traffic classification 會直接影響主人部署的 agent 對外呼叫時被怎麼對待**——Verified status 跟 declarative transparency 是主人下游客戶必須學會使用的護身符。
- **How（如何運作/實作）:**
  - **Trust 模型**:Verified = declare yourself + 不濫用;Bad 行為被 baseline 對比後 unverify,而非直接在黑名單塞名字。
  - **Precursor 偵測層**:CDN-injected JS,**session 全程連續**評估 cursor movement 與 micro-behavior,**刻意拉高 bot developer 模仿人類行為的成本**(長時序、多 page 切換)。
  - **Adaptive Intelligence 模型**:**model 是會自己學的**,不像過去的 Bots ML 走「大版本週期性 release」,而是「看到新 traffic pattern 就升級」——檢測與緩解變成 disposable、可隨時替換的規則集。
  - **三種緩解策略**:(1) 隨機化阻擋 → 阻止指紋採集;(2) AI Labyrinth → 把 crawler 拖進 maze 或者餵污染內容;(3) queuing for good bots → 對 legit agentic traffic 給 throughput 而非直接拒絕。
- **Insight（個人心得）:**
  這篇最強訊號不是「Cloudflare 又多放了幾個產品」,**而是「continuous trust evaluation」這個 primitive 已經被寫到主流 CDN 的骨幹**——這跟主人 2026-08-13 EVE「Known Agents spoofing」、8-18 EVE Wiz Red Agent 那篇是**同一週第三個「agent 改變 security 攻防軸」的具體證據**,三篇合在一起構成:**attacker side(known agents spoofing)、defender side(Wiz Red Agent、Precursor/Adaptive Intelligence)、policy side(BotBase verified status)** 完整三軸。對主人最實際的借鏡有兩條:**第一**,`horo-agent` 若要幫企業客部署 agent,**該建議客戶把 AI Labyrinth 與 BotBase verified status 寫入採用 checklist**——尤其主人目前正在做的 vibe-coded 內部工具,vet 入口一旦開給 agent,沒做 declaration 就會被當 unverified;`horo-agent` 的 default 應該要能吐出 `User-Agent` + opt-in declaration metadata。**第二**,Adaptive Intelligence 那段「model 會自己學、不再等版本週期」對主人 `hermes-agent-lite` 的設計哲學是個 reflect 點:主人目前的設計哲學是「保守減法、端到端驗證、不重寫 core」——但 cloudflare 自己面對的對手是**以小時為單位演化**的 bot 群,**那種敵人面前『等版本週期』會直接輸**,這對主人「lite 版本是否需要在 routing 層加入 adaptive 觀察機制」是個明確的提醒——不一定要在 runtime 加 ML,但最少要在 routing 層做 telemetry,讓下游客戶看到自己的 agent 對外行為是否符合 good-bot declaration 模式,這才是真正能跟主人 8-19 午『Harness the Memory』論文那個 substrate routing 框架接軌的位置。
