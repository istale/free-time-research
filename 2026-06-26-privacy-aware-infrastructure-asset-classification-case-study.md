# Privacy-Aware Infrastructure in the AI-Native Era: An Asset Classification Case Study
- 原始連結：<https://engineering.fb.com/2026/06/25/security/privacy-aware-infrastructure-in-the-ai-native-era-an-asset-classification-case-study/>
- 閱讀時間：2026-06-26

## 摘要
這篇 Meta 工程文：[Privacy-Aware Infrastructure in the AI-Native Era: An Asset Classification Case Study](https://engineering.fb.com/2026/06/25/security/privacy-aware-infrastructure-in-the-ai-native-era-an-asset-classification-case-study/) 談的是如何把「資料隱私分類」做成一個可落地的基礎設施服務，而不是只把欄位名稱丟給 LLM 猜答案。文章的核心觀點很強：真正決定分類品質的通常不是 prompt，而是上下文品質。Meta 先替每個 asset 組出 evidence brief，把 code resolution、lineage、ownership、semantic annotations 等訊號濃縮成支撐與反證，再交給模型處理模糊案例。

他們的架構是明確的 deterministic-first funnel。大約 85% 的請求由 versioned deterministic rules 在毫秒級處理，只有約 15% 的新型或模糊案例才 fallback 到 LLM；後者更慢，而且計算成本高出約 400 倍。這種分流讓 LLM 只負責 cold start、長尾與歧義判斷，而穩定模式則逐步蒸餾成可審計、可重放、可版本化的規則，讓生產決策越來越少依賴模型即時推論。

我覺得最值得記住的是它把「masking」提升成系統不變量，而不是 prompt 小技巧。凡是會造成 circular reasoning 的既有標籤，不只在 LLM 輸入要遮掉，也不能偷偷流回規則蒸餾流程。再加上獨立 evaluation loop、三裁判 judge panel、counterfactual masking 和 rule promotion gate，整套設計其實是在回答一個很現實的問題：怎麼讓帶不確定性的模型推理，最後輸出可以被治理、稽核與長期維運的隱私控制。

## 3W1H 分析
- What（做了什麼/主題）: Meta 分享一套隱私感知基礎設施中的 asset classification 模式，結合 evidence brief、LLM fallback、獨立評估迴圈與 deterministic rule distillation，讓資料分類可直接服務後續的 retention、access、purpose 與 sharing 控制。
- Why（為什麼重要）: AI-native 系統的資料型態更多、轉換更快、政策解釋也更常變，靠人工審查或靜態規則很難跟上；但若直接把分類決策交給 LLM，又會帶來延遲、成本、可重放性與治理風險。這篇文章提供的是一條介於純規則和純模型之間、比較能落地的中間道路。
- How（如何運作/實作）: 先建立穩定的分類契約與 context mesh，再把多來源訊號整理成 evidence brief；決策時先走 deterministic rules，未命中才交給 LLM。線下則用人審 reference labels、獨立 judge panel、校準後 confidence、counterfactual masking 與 shadow mode 驗證候選規則，最後只把通過品質門檻的模式升格為 versioned rules。
- Insight（個人心得）: 真是的，這篇最有價值的不是「又一個 LLM workflow」，而是它非常清楚地把模型能力放在該放的位置。對做 agent、分類器、合規系統的人來說，真正該優化的常常是 context assembly、評估隔離和 rule promotion discipline，不是一直重寫 prompt。
