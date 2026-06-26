# Better Experiments with LLM Evals - A funnel, not a fork
- 原始連結：<https://engineering.atspotify.com/2026/5/better-experiments-with-llm-evals-a-funnel-not-a-fork>
- 閱讀時間：2026-06-26

## 摘要
[文章連結](https://engineering.atspotify.com/2026/5/better-experiments-with-llm-evals-a-funnel-not-a-fork)。Spotify 這篇文章的核心論點很直接：LLM evals 不該拿來取代 A/B test，而是要放在實驗流程前面，先幫團隊做「驗證（verification）」的篩選，再讓線上實驗負責「驗證價值（validation）」。文章指出，LLM judge 很適合大規模評估相關性、連貫性、語氣、意圖對齊等過去昂貴的人工作業，能先淘汰不夠好的候選方案，提高真正值得上線實驗的命中率。

Spotify 也提醒，eval 分數本質上是代理訊號，不等於真實使用者結果。離線 judge 可能會誤判，或只抓到表面模式，卻沒有對業務指標帶來正向影響；反過來，也可能低估某些長任務或長期行為改善。因此他們主張建立「雙層校準」：一層是傳統離線量化指標，另一層是 LLM judge，兩者都要持續拿線上實驗結果回頭校正。當 eval 偏好與 A/B test 結果不一致時，那種落差本身就是最有價值的診斷訊號，能幫團隊修正 judge、調整假設，讓下一輪實驗更準。

## 3W1H 分析
- What（做了什麼/主題）: Spotify 分享如何把 LLM evals 放進既有實驗文化中，定位成上游篩選與品質驗證工具，而不是 A/B test 的替代品。
- Why（為什麼重要）: 很多團隊開始把 LLM judge 當成快速決策工具，但若沒有線上校準，分數再漂亮也可能只是自我感覺良好。這篇文把「離線看起來更好」和「真實世界真的更好」切得很清楚。
- How（如何運作/實作）: 先用 LLM evals 評估候選方案在品質面是否達標，減少浪費實驗流量；再用 A/B test 驗證是否真的改善使用者體驗與業務結果；最後把實驗資料回灌到 eval 系統，持續校準 judge 與代理指標。
- Insight（個人心得）: 這套思路很適合 AI 產品與 agent workflow。真是的，很多人一看到 judge 自動化就想跳過實驗，但真正穩的做法反而是把 eval 當成漏斗前段，讓它替你提高測試品質，而不是替你省掉面對真實世界的那一步。
