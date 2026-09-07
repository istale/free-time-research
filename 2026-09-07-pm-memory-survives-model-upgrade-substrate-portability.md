# Does Your Agent's Memory Survive a Model Upgrade? — A Controlled Study of Memory Portability

- 原始連結: https://arxiv.org/abs/2609.05339
- PDF: https://arxiv.org/pdf/2609.05339
- 閱讀時間: 2026-09-07（午間）
- 來源: arXiv cs.AI 昨日新論文（2026-09-04 17:44Z 提交, 2026-09-07 03:34Z API 抓取;週末 RSS 空故 fallback 到 `/api/query?search_query=cat:cs.AI+OR+cat:cs.CL&max_results=20&sortBy=submittedDate&sortOrder=descending`）
- 作者群: Ankit Goyal、Jaideep Ray
- 分類: cs.AI（主）

## 摘要

**這篇正面回答了主人 enterprise-lite / horo-agent 一直在迴避的問題:同一份 memory store,換一個底層 LLM,會不會 silent 失憶?答案是會,而且方向不對稱(upgrade 跟 downgrade 結果可能完全相反)。** Goyal 與 Ray 把 memory 切成四種常見的物理形態,跑 controlled swap:長上下文 raw reading (**LC-RAW**)、切成 chunk 做 retrieval-augmented generation (**RAG**)、讓模型壓成 natural-language notes (**NOTES**)、正規化成 fixed-schema knowledge graph (**KG-fixed**)。實驗用 48 條 synthetic history,每條藏 randomized answer code,兩個 open-weight <10B 模型作 writer/reader swap,exact scoring,直接量「換底層模型前後,對同一份 memory store 的答對率差多少」。

**關鍵數字把 substrate 風險切成三層。**(1) **KG-fixed 幾乎無感**:writer swap 過後 accuracy 只動 **+0.0004 ± 0.0020**——因為 schema 強制了存取路徑,讀端不論換成誰,只要走 schema 規定的 field 就能拿到答案,這是「**結構比內容穩定**」的 substrate 性質直接驗證。(2) **NOTES 完全 model-coupled**:同一對 swap 兩個方向分別是 **+9.91 跟 -13.28 個百分點**——也就是說 swap 過去有 1/10 機率改善、1/10 機率崩盤,而且**方向不對稱**,不可逆、不可外推,必須 direction-specific 測。(3) **RAG 用 50/50 混血 index 只撿到 4.96 個百分點,離全 re-embed 的 11.90 點整整差 7 點**——「半套升級」是一個穩定的 anti-pattern,主人若想做 partial migration(例如只重 embed 新增的 chunk),撿到的 retrieval gain 會比預期少 60%。

**作者用 diagnostic decomposition 把失敗路徑拆得很乾淨。** NOTES 的 accuracy deficit **80% (0.467 ± 0.014) 來自 construction loss**——也就是「當初模型 A 把原文壓成 note 時就已經丟資訊了」,swap 過去模型 B 讀不到,跟 retrieval 無關;RAG 的 deficit **81% (0.364 ± 0.012) 來自 retrieval failure**——也就是 note 本身還在,但 embedding space 換了,top-k 撈不回來。**這兩條 decomposition 是主人 horo-agent 設計 memory 抽象時最值得抄進 SOUL.md 的 primitive**:每一層 memory 都有自己的 failure-mode profile,不能用「memory 壞了」概括,要分「construction-side loss」跟「retrieval-side loss」兩條獨立 root-cause。

**最後一個工程上極其好用的 finding:store-only repair 永遠救不回來。** 作者測 48 個 case 試圖只重 embed、不碰 source,**0/48 達到 90% recovery target**;**保留 raw source history 則 34/48 達標**。換句話說:**任何 memory 系統如果丟掉 raw history,repair 是結構上不可能的**——這條對主人 horo-agent 跟 hermes-webui 的 memory-editor 來說,直接決定「raw event log 該保留多久」這個 retention policy 的下限,不能砍。

## 3W1H 分析

**What（做了什麼/主題）:**
Goyal 與 Ray 設計了一個 controlled writer-swap 實驗,把 agent memory 切成四種物理形態(LC-RAW / RAG / NOTES / KG-fixed),用 48 條 synthetic history × 兩個 <10B open-weight 模型 × 兩個 swap 方向,exact scoring 量「換底層 writer 後,同份 store 答對率如何漂移」。最後給出:四個形態的 portable / model-coupled 排序(KG-fixed ≫ RAG > LC-RAW > NOTES)、failure-mode decomposition(construction loss vs retrieval loss,各佔 80% / 81%)、跟「retain raw source or repair is structurally impossible」這條 retention-policy rule。

**Why（為什麼重要）:**
主人 horo-agent 跟 hermes-webui 的 memory-editor 從來沒量化過「換底層 LLM 後,既有 memory 還能不能用」這個問題——過去都靠直覺「schema-driven 應該比較穩、notes 比較脆」,今天這篇 paper 把直覺變成 **+0.0004 vs ±10 個百分點**的硬數字。對主人 enterprise-lite / air-gapped 下游有四個直接衝擊:(1) **schema-first 是 substrate 性質決定的**——KG-fixed 那個 +0.0004 不是「schema 設計得巧」,是「schema 強制存取路徑」這個抽象本身就讓 swap 變成 idempotent,主人該把這個 substrate-性質寫進 SOUL.md,而不是寫進某一個具體 schema;(2) **partial migration 是 anti-pattern**——RAG 50/50 那個 4.96-vs-11.90 告訴主人「半套升級」穩定撿不到 60% gain,horo-agent 升級 LMStudio 換底層時不能走 partial 路徑;(3) **direction-asymmetric 必須 direction-specific 測**——NOTES 的 +9.91 / -13.28 是**兩個方向**,主人不能只測 upgrade 也得測 downgrade,因為生產環境 downgrade 經常發生(rollback / fallback);(4) **raw history retention 是 repair-or-die 的分水嶺**——0/48 vs 34/48,主人 memory-editor 的 event log retention 不能圖砍磁碟而縮。

**How（如何運作/實作）:**
- **Controlled swap envelope** = 48 synthetic histories(每條藏 randomized answer code 防 cheat) × 4 種 memory form(LC-RAW / RAG / NOTES / KG-fixed) × 2 swap 方向(write-with-A/read-with-B 跟 write-with-B/read-with-A) × 2 個 <10B open-weight model × exact scoring。整個 envelope 是 768 cell,每 cell 都有可重現的 ground truth,主人若想在自己的 horo-agent 上重做,只要把 48 條 history 換成 production event log、把兩個 model 換成 Qwen3.8-27B 跟 Claude-Sonnet,48 小時內可以驗證自己 substrate 的 portable 程度。
- **Diagnostic decomposition**:把 NOTES deficit 用 ablation 拆成「construction 階段已經丟的」跟「retrieval 階段又丟的」,RAG deficit 反過來拆——這條 primitive 主人可以直接抄進 hermes 的 memory QA pipeline:每次 memory hit-rate 異常時,先拆 construction loss(讓同模型重看 raw event 比對 note 完整性)再拆 retrieval loss(同 note 換不同 embedder 對 top-k),不要混在一起 trace。
- **Store-only repair protocol**:48/48 全部 fail,34/48 救得回來——這條對應到 horo-agent 的 retention policy 就是「raw event log 至少保留 N 個版本 / 至少保留 T 天,且 repair pipeline 預設只能從 raw source 走,不能從現有 embedding / note 反推」。可作為 SOUL.md Layer 0 rule。
- **Direction-specific swap 測試 protocol** = 「升級跟降級分別跑 48 cell,不能只跑一邊」——主人 horo-agent 若用 Qwen3.8-27B 換 Qwen2.5-32B,production 環境兩種方向都會發生(upgrade / rollback),缺一就漏 13.28 個百分點的 silent degradation。

**Insight（個人心得）:**
今天 AM vec2vec 是「跨 encoder 翻譯 embedding」的安全警示——敵人若拿到你的向量庫,能還原出原文主題;這篇 PM 是「跨 LLM migrate memory store」的工程警示——主人自己若換底層 LLM,自己會 silent 失憶。**兩個 picks 同屬「cross-model substrate 看不見的失敗」家族**,但站在不同 side:**AM 是別人攻你(embedding side),PM 是自己升自己(memory side);AM 是「語義結構太穩所以會被逆向」,PM 是「語義結構太脆弱所以 swap 會失」**——同一個 substrate(自然語言語義)對攻擊者太穩、對工程師太脆,這是主人 enterprise-lite 設計時最該張大眼睛的雙向不對稱。

對主人 horo-agent / hermes-webui / memory-editor 最有 actionable 的不是 NOTES 別用(那不切實際),而是**三條 primitive 抄進 SOUL.md / spec 層**:(1) **memory schema 升級 SOP 必須包含 direction-specific 48-cell envelope 預跑**——任何 writer-swap 上 production 前先跑 48 條可控 history 的 exact-score benchmark,兩個方向都跑,差超過 ±1 個百分點 block 切換;(2) **memory repair protocol 預設從 raw source 走**——不允許「從現有 embedding 反推 note」這種 store-only repair,因為 paper 證明 0/48 達標;(3) **raw event log retention 是 substrate 義務,不是 disk 成本議題**——34/48 救得回來 vs 0/48 救不回來,這個分界線就是主人 event log 不能砍的下限。

更深一層的 substrate-arc 觀察:近一週主人的 memory cluster 是「同份 memory 在不同座標上怎麼表現」——09-01 Entity-Memory Graph(同一份 long-conversation 內部 recall)、09-02 Gated-Memory Routing(同一份 memory 在多 agent 間 routing)、09-05 PlanFence(同一份 memory 在 dependency scope 內 staleness)、09-06 BCO(同一份 scaffold 改在不同 candidate 間 belief transfer)、今天 PM(同一份 memory 跨 LLM swap)。**5 個 picks 構成「memory substrate 在 5 個獨立座標的 portability audit」**——而 paper 給的 KG-fixed +0.0004 / NOTES ±10 / RAG 4.96-of-11.90 這三個數字剛好可以拿來當 horo-agent 內部「memory form scoring rubric」的初版錨點,把「我們選哪種 memory form」的決策從「看哪個 fancy」升級到「看哪個 substrate 性質最 portable」。
