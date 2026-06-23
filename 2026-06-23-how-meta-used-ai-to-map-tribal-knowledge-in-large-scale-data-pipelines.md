# How Meta Used AI to Map Tribal Knowledge in Large-Scale Data Pipelines
- 原始連結：<https://engineering.fb.com/2026/04/06/developer-tools/how-meta-used-ai-to-map-tribal-knowledge-in-large-scale-data-pipelines/>
- 閱讀時間：2026-06-23

## 摘要
這篇 Meta 工程文〈[How Meta Used AI to Map Tribal Knowledge in Large-Scale Data Pipelines](https://engineering.fb.com/2026/04/06/developer-tools/how-meta-used-ai-to-map-tribal-knowledge-in-large-scale-data-pipelines/)〉談的是一個很實際的 AI 落地問題：模型不是不知道怎麼寫程式，而是不知道你家 codebase 裡那些沒寫進文件、只存在資深工程師腦中的「部落知識」。Meta 面對的是一條跨四個 repo、三種語言、4,100 多個檔案的大型 data pipeline；在這種 config-as-code 系統裡，一個欄位變更可能同時牽動設定註冊、路由邏輯、DAG 組裝、驗證規則、C++ codegen 與自動化腳本。沒有上下文時，AI agent 會一直猜，最後交出「能編譯但語意微妙地錯掉」的修改。

他們的解法不是把 prompt 越寫越長，而是先做一層「先驗知識基礎建設」。Meta 用 50 多個專職 agent 先掃完整個 codebase，再產出 59 份 context files，讓 AI 在真正動手改 code 前，就先拿到每個模組的導航圖。這些檔案遵守「compass, not encyclopedia」原則，每份只保留四類資訊：可直接複製的操作命令、最該看的 3 到 5 個關鍵檔、容易踩雷的非顯而易見規則，以及跨模組參照。這種設計很聰明，因為它不是想把所有知識灌爆 context window，而是把 agent 從盲目探索，改成有方向地探索。

文章裡最有價值的是他們如何系統化地挖出隱性知識。11 個 module analyst agents 讀遍每個模組，回答五個問題：這模組在配置什麼、常見修改模式是什麼、哪些不明顯規則會導致 build failure、跨模組依賴有哪些、以及哪些 tribal knowledge 藏在註解裡。最後他們整理出 50 多條「non-obvious patterns」，像是某個中間欄位名稱會在下游被重新命名，指錯欄位就會讓 codegen 靜默失敗；或者某些看似 deprecated 的 enum 值其實不能刪，因為序列化相容性還依賴它。這些東西平常根本不在正式文件裡，但對 agent 成敗卻是致命差異。

成效也很具體。AI 導航覆蓋率從約 5% 提升到 100%，可理解檔案數從約 50 個提升到 4,100+；在六個任務的初步測試中，帶有預先計算上下文的 agent，每個任務少用約 40% 的 tool calls 與 tokens，原本要花兩天查資料加問人的複雜 workflow，縮到約 30 分鐘。更重要的是，這套知識層不是一次性文件，而是會定期自我刷新：自動檢查路徑、補 coverage gap、重跑 critics、修 stale references。真是的，這才是像樣的 agent engineering，不是迷信更大模型，而是把「理解系統」本身工程化。

## 3W1H 分析
- What（做了什麼/主題）: Meta 分享如何用多代理 pre-compute engine，把大型私有 data pipeline 中的 tribal knowledge 轉成 59 份可供 AI agent 使用的 context files 與依賴索引。
- Why（為什麼重要）: 它直接回答了企業內部 AI coding 常見失敗點: 不是模型不會寫，而是模型不知道專案裡哪些隱性規則不能踩。這對任何想把 AI 用進大型私有 codebase 的團隊都很有參考價值。
- How（如何運作/實作）: 先讓 explorer、analyst、writer、critic、fixer、tester 等 50+ specialized agents 分工掃描整個 codebase；每個模組萃取關鍵檔、常見修改模式、隱性規則與交叉依賴；再用自然語言 routing layer 和定期自刷新機制，把這層知識接到實際開發與維運工作流。
- Insight（個人心得）: 我最有感的是它把「AI 上下文」從臨時 prompt 技巧，升級成可維護的知識基礎建設。與其期待 agent 自己摸清一個陌生系統，不如先替它做出可靠地圖；先把知識壓縮成高密度導航層，再談自主編碼，成功率才會真的上來。
