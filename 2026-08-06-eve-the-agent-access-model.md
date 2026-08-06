# The Agent Access Model
- 原始連結：https://blog.cloudflare.com/the-agent-access-model/
- 閱讀時間：2026-08-06（晚）
- 作者：Matt Silverlock（Cloudflare, Product / Identity）
- 發布時間：2026-08-05
- 來源：Cloudflare Blog（RSS tier-1，Agents Week 系列第二天主文）

## 摘要

**BeyondCorp 的下一步不是「再上一層 AI 判斷」，而是把信任邊界從網路縮到任務。** Matt Silverlock 把十年前的 BeyondCorp 重新拿來對照：當年 Google 解掉了「內網就信任」的盲點，把決策移到身分+裝置+請求層；今天同一套控制被拿來管「任務範圍內的 ephemeral agent」時卻安靜地失敗——因為它們「grant 太多、看太少、信太久」。Agent Access Model（AAM）只訂一句開場規則：「不要信任這次 run，每一次動作都要用 task + 累積 state 重審一次。」這條規則把 implicit trust 從網路搬到 task execution graph 上——任務啟動時發一張短效憑證，之後每個 tool call、每個 egress packet 都用同一個 capability ceiling 去比。

**Agent 跟人類在四個維度上根本不是同一種 principal——這才是問題的根。** 文章把差異寫得很硬：(1) 短效 vs 永久憑證：service account 的長效 key 配 ephemeral task 是 replay 漏洞溫床；(2) 機器速度 vs 人類速度：人類調的 DLP / rate-limit 還在取樣，agent 已經把表格 POST 出去了；(3) prompt 不是邊界：「不要連 prod」這種 prompt 指令可以被 prompt injection 直接繞過——「你能用嘴巴講通的牆不是牆」；(4) 跨 hop 授權會掉鏈：agent → tool → 另一個 agent → API 的呼叫鏈中「這次是誰、要幹嘛、可以做什麼」會在某個 hop 蒸發。Cloudflare 對應的解法是 sender-constrained token（DPoP / OAuth 2.0 Token Exchange, RFC 8693 / 9449）+ harness-mediated tool path + programmable network egress——把 enforcement 放在「動作真正發生」的位置而不是「prompt 文字」的位置。

**AAM 的五原則與四主動控制是文章真正的工程遺產。** 五原則很洗鍊：(1) 憑證短效且綁定，(2) enforcement 在 harness / network 而非 prompt，(3) 人工覆核保留給真正需要的高風險動作（不是每步 approve，否則會養出 reflexive-clicking 的 approval fatigue——作者直接拿 Windows UAC 當反例），(4) grant review 必須來自 captured evidence 而不是模型自報，(5) capability state 只能單向收窄（Trust Ratchet）。四個 active control 加上兩個 supporting system 構成 reference architecture：**Agent Identity Broker**（dispatch 時發任務範圍的短效憑證）、**Task-Scoped Access Engine**（每個 action 比對 template ∩ 當事人權限 ∩ 資源擁有者政策，三者交集才是 ceiling）、**Mediation Layer**（harness + 網路，兩者 fail independently）、**Trust Ratchet**（stateful 信任，只會變窄）；支援的 **Agent Activity Log** 是 append-only event contract，**Grant Review Loop** 用證據去問「這個 task template 是不是過寬／過窄」——approved 變更只套到未來 task，active task 保留原 ceiling。

**「Trust Ratchet 保護資料」是 AAM 最具體的工程創新。** 它把「資料敏感度分級」變成狀態機：當 task 收到一筆 classified-protected 的 response，harness 不直接把資料交給模型——它先把 response buffer 住，廣播「Baseline → Restricted」的 state 變更給 Access Engine、harness、network 三個 enforcement point；每個點用 CAS / single-writer 序列化地更新、清掉舊決策快取、關掉舊連線、ack 回去；全部 ack 到齊之前，response 不會被釋放到模型。這套 fail-closed 的協議解決了「人為 DLP 對機器速度不夠快」的舊問題——而且 state 只能變窄、不可逆，attacker 就算拿到一張 restricted-state 內的憑證也無法 reset 回 baseline。

**Multiplayer access control 是作者誠實標記的開放問題。** 當一個 agent 同時替 Alice（有 revenue 權限）和 Bob（沒有）做事，誰能讀到誰的資料？作者直接寫：「We are not comfortable saying that multiplayer access control can be built end to end today.」並引用 CI-Work benchmark 的數字：在模擬企業工作流中，多人 agent 的隱私違規率 15.8%–50.9%、洩漏可達 26.7%。現有的 actor chains / `may_act` claim / per-principal scope 都只是 building block，沒有一個能完整帶著 item-level authority 穿過 retrieval → shared context → generation → caching → delivery 整條鏈。結論是：AAM 目前只守住 single-principal task graph；多人 agent 必須 per-principal 隔離或退到保守 common grant——以 shared context 為代價。

## 3W1H 分析

### What（這篇文章到底在說什麼）
Cloudflare 提出 Agent Access Model（AAM）作為 agent 時代的存取控制 reference architecture。它把 BeyondCorp 的「去隱式信任網路」延伸成「去隱式信任 task execution graph」：短效 sender-constrained 憑證、Task-Scoped Access Engine、雙層 Mediation（harness + network）、Trust Ratchet、Agent Activity Log、Grant Review Loop 六個組件共同把「每一次 action 都用 task + 累積 state 重審」做成可在 machine speed 強制執行的協議。文章也誠實標出 multiplayer access control 是尚未關閉的系統問題，並用一段 nightly reconciliation 的場景把整套機制跑一遍。Agents Week 的定位讓它跟昨天讀過的 ADLC 是姊妹篇——ADLC 處理「agent 寫程式」的流程，AAAM 處理「agent 跑任務」的權限。

### Why（為什麼這對主人有意義）
主人記憶裡有三條線會在讀 AAM 時自動亮起來：(1) air-gap / enterprise-lite downstream 的硬規則要求「permissioned」是 ADLC 七屬性之一，而 AAM 直接把「permissioned」展開成可施工的 reference architecture；(2) SOUL.md 是 runtime-visible control surface、profile isolation 是 permissioned 最小版本——AAM 把這條工藝升格成業界術語，特別是 Task-Scoped Access Engine 的「template ∩ principal ∩ resource-owner 三交集 = ceiling」公式，跟主人現有的 stage gate / commit-per-stage 是同形結構；(3) 主人最近在做的 horo-agent / horo-webui 是「多個 hermes profile 同時活著」的多人環境——剛好踩在 AAM 自己標出的開放問題上（multiplayer access control, 26.7% leakage）。換句話說，AAM 給主人的不只是命名，是「驗證後可以怎麼修」：把 SOUL.md 的 skills/ 對應到 task template，把 profile isolation 對應到 Access Engine 的 ceiling 計算，把 stage gate 對應到 Trust Ratchet 的 state transition。

### How（主人可以怎麼用）
主人不該直接把 AAM 整套搬進 horo-agent——它會立刻撞上主人「複雜化會被打回」的紅線，且 AAM 自己承認 multiplayer 部分還沒有 end-to-end 方案。但有三個**保守增量**可以今晚就回填進 SOUL.md / 現有 skill：(a) 在 `hermes-runtime-operations` 的 memory / skill_manage action 上加一段「task-scoped」備註——任何跨 profile / 跨 cron 的存取都要在 dispatch 端發一張帶 lifetime 的 token，避免主人的 service key 留在 profile memory 裡被 replay；(b) 把 Trust Ratchet 的「state 只能單向收窄」原則寫進 `horo-vm-autonomy-workflow`：當一個 delegation 收到敏感輸入（主人的私人檔案、Tailscale key、cron jobs.json），harness-level 的 tool path 就必須先把 egress / write tool 收窄才能把 response 傳到下游 agent，這正是主人 background cron 派工時最可能踩到的 exfiltration 路徑；(c) 在 `daily-research-digest-*` playbook 開頭加一行「Agent Activity Log 風格的 evidence capture」——每次 cron 寫新檔前，順手在 commit message 留 `access: {sources: ..., ceilings: ..., ratchet_state: ...}`，未來要 audit 或 rollback 就用同一個格式而不是從散文裡撈。

### Insight（赫蘿的觀察）
主人讀完最值得帶走的一句：**「AAM 的中心論點其實是『讓 agent 的 capability 更小，這樣要判斷的就更少』——不是讓判斷更聰明」**。這跟過去三年整個業界（也包含主人記憶裡的 OCA / ADLC 類思維）的主流方向是相反的——主流會想把「聰明的 policy model」疊上去解所有 edge case，但 Silverlock 在這條線上拉出一個非常 Cloudflare-style 的工程立場：縮小問題、縮小決策面、把 enforcement 放到 action 真正發生的點，而不是 prompt 文字或 LLM 判斷裡。對主人最直接的影響是「重新校準」幾個正在做或打算做的方向：第一，`advisor-council` skill 如果想加 risk gate，**不該**寫一個 risk-judge LLM（會踩 prompt injection），而是該學 AAM 在 dispatch 端用「template ∩ 主人 authority ∩ resource-owner policy」三交集直接算 ceiling——讓模型看不到、不需要判斷。第二，**multiplayer 那段誠實標記的開放問題就是主人 horo-agent 的真實風險**：當預設 cron (`daily-research-digest-am-playbook` 與 `daily-research-digest-pm-playbook`) 同時跑、又同時透過 Agent Share 在 tailnet 上讀別的 agent 的訊息，誰的 authority 是 ceiling？Cloudflare 自己說「we do not know of a widely deployed end-to-end system that closes the whole chain」——主人不需要解決它，但應該在 SOUL.md 明確標出「horo-agent 多 profile 共存時，預設走 per-profile isolation + 保守 common grant」，並把這條寫進 `horo-vm-autonomy-workflow`。決策邊界：單一任務、單一 authority、有 captured event 的場景（AAM single-principal case）可以採用 task-scoped + trust ratchet 的設計；多人 / 跨 profile / 共享上下文的場景，**先不要試圖做聰明的解**，直接 per-profile 隔離——這跟 AAM 自己的結論對齊，而且符合主人「不要複雜化」的偏好。