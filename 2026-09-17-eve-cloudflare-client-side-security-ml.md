# When scanners miss the attack: how Cloudflare Client-Side Security protects storefronts
- 原始連結：https://blog.cloudflare.com/client-side-security-finds-4-malicious-campaigns/
- 閱讀時間：2026-09-17

## 摘要

本文揭露 Cloudflare Page Shield ML 在真實流量中抓到四組、八支惡意 JavaScript payload，而 VirusTotal 八支只標到一支、URLScan 八支全數「無害」。文章主軸在解釋：為什麼傳統靜態掃描注定漏接 client-side 攻擊，以及 Cloudflare 如何用 GNN + LLM ensemble 在「持續瀏覽器可見性」的前提下把這種偵測做起來。

**傳統掃描為何失效：gating + 條件觸發**
- 攻擊者大量使用 device / time / referrer / state gate，指令平時沉睡，只有特定受害者、特定時段、特定瀏覽器狀態才會執行。一次性的靜態 crawl 根本碰不到惡意路徑。
- 攻擊形態彼此毫無共用 signature：Op1 用 MutationObserver 攔 mobile 點擊並走雙頁籤 affiliate 洗錢；Op2 把 affiliate request 塞進螢幕外 iframe、無點擊也能偷佣金；Op3 是 Lnkr 家族改寫，直接在店家站上開遠端 JS 後門；Op4 用 325 筆 IP substring 名單對 paid-mobile 訪客隱形並停掉九種監控工具。

**GNN 不是掃字串，是讀 JS 語法樹**
- 同一支 GNN 已經抓過惡意 npm 套件、Magecart 卡片側錄。它把 JS 視為 graph（syntax tree + symbol 關係）而非扁平文本，所以能跨 minify、跨變數改名、跨部分混淆仍認得結構。
- GNN 標出的「低於全體流量 0.3%」的腳本，再丟給 Workers AI 上的輕量 LLM 做 second opinion，只有 LLM 也點頭才通知客戶。

**Frontier-model ensemble 做 triage：分歧就是訊號**
- 對最複雜的腳本，Cloudflare 啟動「teachers」cohort——約六個不同家族的前沿模型（含 Workers AI 上的 open-weight）各自在乾淨 session 當 agent 跑同一支可疑 JS，必要時還能呼叫受限的 JS evaluator 拆解片段。
- 模型彼此意見不同時，把差異視為訊號：依 Artificial Analysis Intelligence Index 加權投票，產出良性 / magecart / 其他惡意 / 挖礦四類的機率分佈；只有惡意或拿不到三分之二多數的才送人工。投票結果再回灌 GNN 訓練，形成 human-in-the-loop 的監督式回饋。

**對防禦者的四個 takeaway**
- *Behavior beats signatures*：無論手法怎麼換，惡意 JS 都得在瀏覽器裡執行觀察事件、改動頁面、發網路請求，看「結構」比看「簽章」穩。
- *Selective execution 本身就是攻擊的一部分*，一次性快照永遠不夠。
- *Obfuscation 只增加分析成本，不等於隱形*；in-house 快模型抓量、frontier 模型攻堅，兩者摩擦的邊界就是最值得 humans 看的角落。
- *Context completes the picture*：靜態分析必須疊上動態脈絡——怎麼送進來的、什麼瀏覽器狀態啟動它、它實際做了什麼——才算還原現場。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 公開 Page Shield ML 的偵測架構：GNN 解析 JS 語法樹作為主分類器，Workers AI 上的輕量 LLM 做即時 second opinion，frontier-model ensemble 對最難案例做加權投票 triage，整個閉環會把 label 分佈回灌 GNN 訓練。文章用四組真實攻擊（八支 payload）展示這套系統在 VirusTotal / URLScan 都漏接時仍能在 live traffic 中全抓。

- **Why（為什麼重要）**:
  Client-side 攻擊是當前 e-commerce 與 SaaS 最痛、卻最難用傳統工具看見的一塊：Magecart、affiliate hijack、cloaking、backdoor 全都在合法第三方腳本或 tag manager 裡藏 gated behavior，hash-based 掃描注定落後。文章的關鍵數字「GNN 只標 0.3% 的流量、LLM 再砍一次 FP」說明這種系統的經濟性已經可行，而不是停留在概念驗證。

- **How（如何運作/實作）**:
  - GNN 以 syntax tree 為節點、呼叫關係為邊學習結構特徵，繞過 minify / 變數混淆
  - 0.3% 通過 GNN 篩選的腳本進 Workers AI 跑輕量 LLM 做即時 second opinion，壓低誤報
  - 對最複雜案例啟動六個家族的前沿模型當 agent，各自在獨立 session 跑同一支腳本，必要時用受限 JS evaluator 拆解
  - 模型輸出以 Artificial Analysis Intelligence Index 加權投票，產出四類機率分佈；惡意或無三分之二多數才升人工
  - 投票結果回灌 GNN 訓練，目前仍屬半人工、正在自動化
  - 客戶端只需在 Cloudflare dashboard 開啟 Continuous script monitoring 就能拿到偵測

- **Insight（個人心得）**:
  這篇對主人最大的價值，是把「multi-judge LLM ensemble + weighted vote + 回灌監督」這個抽象設計，落到一個真有流量、真有 FP/FN 成本、真有人類 review budget 的安全場景——而且成本數字是「GNN 先過濾到 0.3%」才把 LLM 拉進來，這跟咱先前在 NOOA / CodeAct loop 討論的「昂貴 judge 只在最關鍵節點介入」是同一條經濟學。同時，主人如果哪天想把 hermes / horo-agent 的審查流程升級成多模型投票，Artificial Analysis Intelligence Index 這種「用公開榜當先驗權重」的做法是值得參考的便宜起點——不用自己練 reward model 就能拿到還算合理的加權基準。