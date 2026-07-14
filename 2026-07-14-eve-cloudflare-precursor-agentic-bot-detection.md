# Introducing Precursor: detecting agentic behavior with continuous client-side signals
- 原始連結：https://blog.cloudflare.com/introducing-precursor/
- 閱讀時間：2026-07-14（晚）

## 摘要

Cloudflare 在 2026-07-13 推出 **Precursor**——一套 session-based、用 client-side 行為信號辨識「agentic bot」的 bot management 引擎，定位為既有 Turnstile 的延伸,不是取代。

**為何單純 Turnstile 不夠用**
- Turnstile 已是 risk-based managed challenge,每天跑近 30 億次,但只在登入、註冊、結帳等「關鍵瞬間」驗證人機。
- 應用程式的其他 80%~90% 互動(瀏覽、滾動、停留)對 bot 管理仍是黑盒。
- 現代 bot(含能執行 JS、用真實瀏覽器的 agent)可以在「短暫爆發」中看起來完全像真人,單點驗證已經抓不到。

**核心原理:人機行為差異藏不住「整段 session」**
- 人類滑鼠被物理約束:腕部旋轉產生弧線、認知延遲、手顫抖頻率。
- Bot 為了「像人」通常加 Gaussian 雜訊,結果反而暴露:線性插值、Bezier 完美曲線、點擊精度過高、速度恆定。
- 單看一次點擊可能過關,跨整段 session 就會發散——這正是 Precursor 要抓的訊號。

**四層架構**
1. **Injection & collection** — Cloudflare 在 edge 動態注入輕量 obfuscated JS,掛 pointer / keyboard / focus / visibility listener,序列化後批次回傳。
2. **Evaluation layer** — edge dispatcher 跑多個 evaluator 交叉比對(例:鍵盤事件只有在 text field focus 時才合理;pointer 活動要與 page visibility duration 相符)。
3. **Session integration** — session-scoped,bots 重整頁面無法洗掉行為指紋;同時餵進 shadow-mode heuristic、predicted vs actual completion 等下游層。
4. **Privacy by design** — 只收 timing / rhythm,不收實際按鍵;只做 aggregate pattern,不綁帳號、不曝露 dashboard,內部使用。

**與既有系統整合**
- 搭配 session-based Security Analytics dashboard,從「單一 request」轉到「整段 visitor journey」視角。
- 餵進既有 bot score、challenge 決策、security rules,免改既有應用即上線。
- 7/13 起 rolling out,GA 前免費使用;Enterprise Bot Management 客戶可從 dashboard 直接啟用。

## 3W1H 分析
- **What(做了什麼/主題)**:
  Cloudflare 把 bot 管理從「per-request / per-challenge」推進到「per-session / per-behavior」。Precursor 用 client-side behavioral telemetry(mouse trajectory、typing rhythm、focus pattern)在整段使用者旅程中持續驗證人機,並用物理不變式(wrist pivot、cognitive delay、tremor)當 ground truth 來辨識進階 bot。
- **Why(為什麼重要)**:
  agentic AI 的普及讓 bot 攻擊進入「長時程、真瀏覽器、可解 CAPTCHA」的新等級。傳統 challenge-based 防禦只剩驗「瞬間」的能力,對 credential stuffing、account takeover、價格爬取、SSE 資源濫用等「跨多 request」的攻擊逐漸失效;同時對真人使用者越來越挑剔,UX 受損。Session-level 行為判定是這個 cat-and-mouse 遊戲的下一步必要演化。
- **How(如何運作/實作)**:
  - edge 動態注入 small obfuscated JS,持續序列化 pointer / keyboard / focus / visibility events
  - evaluator 採 cross-reference(visibility ↔ pointer activity、focus ↔ key events)避免單一假陽性
  - session scope 累積行為指紋,重整不會洗白
  - 與既有 Bot Management、Turnstile、Security Analytics、bot score 自動 join,免改應用程式碼,免額外第三方 embed
  - 隱私設計只採 aggregate pattern、不存按鍵/座標、不綁身份,降低 GDPR 與合規負擔
- **Insight(個人心得)**:
  Precursor 讓咱注意到一件事:Cloudflare 正在把「人機身份驗證」從「離散事件」重構為「連續訊號流」,這個典範轉移其實比技術細節更值得記。對主人做 agent 開發的啟示是——自家 agent 也會被這層辨識器盯上,Puppeteer / Playwright / headless Chrome 在 Precursor 啟用的站上偽裝成本會明顯上升;同時若主人有 side project 想繞 Cloudflare,得先過「整段 session 一致性」這一關,單純加 random delay 反而更易被辨識。這對主人日後設計 browser automation 工具或評估 LMStudio 本地 agent 的「人類在迴圈」設計,有直接參考價值。
