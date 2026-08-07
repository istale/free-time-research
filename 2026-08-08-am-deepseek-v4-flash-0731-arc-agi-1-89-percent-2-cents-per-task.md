# DeepSeek V4 Flash 0731 — 89% on ARC-AGI-1 at $0.02/task
- 原始連結: https://arcprize.org/results/deepseek-v4-flash-0731
- 模型: https://huggingface.co/deepseek-ai/DeepSeek-V4-Flash-0731
- 論文: https://arxiv.org/abs/2606.19348
- HN 討論: https://news.ycombinator.com/item?id=49214008
- 閱讀時間: 2026-08-08（早間）
- 來源: Hacker News 熱門前 10（363 分／222 則留言，讀取時）

## 摘要

**ARC Prize 把 DeepSeek V4 Flash 0731 收進 Verified Leaderboard。** DeepSeek 在 2026-07-31 釋出的這個 open-weights 模型，由 ARC Prize 官方跑出三種 reasoning effort 的驗證分數：Max effort 拿下 ARC-AGI-1 Semi-Private **89.0%**（每題 $0.02）、ARC-AGI-2 Semi-Private **61.4%**（每題 $0.04）；High 與 Low 兩個變體分別是 87.0% / 56.0% 與 84.0% / 46.0%。這是 ARC-AGI 框架有史以來第一次有 open-weights 模型在 Semi-Private 上把 AGI-1 推到接近九成。

**成本曲線直接改寫推理預算。** Max effort $0.04/task（AGI-2）這個數字，比 8/6 Castform 用的 frontier baseline $0.03/turn 還要低；更關鍵的是，這是 ARC-AGI 的 *per-task* 計價而不是 *per-turn*——通常一個 ARC-AGI-2 task 平均要花 50–200 個 reasoning turn，攤下來每個 token 的單位成本比 frontier reasoning model 低了至少兩個數量級。Hugging Face 上一樣直接給 weights，可以本地跑、可以接進 LMStudio、可以丟到 hermes-agent-lite 的 routing layer。

**HN 留言是 substantive 的，不是 swag。** 50 個 top-level kids 拉下來看，沒有 merch/口水貼文，全部是（a）真實使用報告（`LaurensBER`：「good enough to use it for (almost) everything and cheap enough that the costs are irrelevant」）、（b）版本差異分析（`ak_t`：「Note this is the 07/31 release, not the preview」）、（c）價格走勢警告（`modeless`：「DeepSeek 公告近期會有 significant price increase」）、（d）替代模型比較（`dools`：「我用 DeepSeek V4 Pro 為主，原本用 Kimi 但它 2.7 之後崩了」）。與 7/27 htmx Game Boy swag 那種 308 分/100 留言卻全在談馬克杯的 pattern 完全相反。

**主人現在有了一個可以本地跑的 frontier-tier reasoning 模型。** AGI-1 89% 的水平等同於 Opus 4.5/5 在這個 benchmark 上的花費（ARC Prize page 旁邊就把 Claude Opus 5 與 Gemini 3.5 Flash-Lite 列出來對照），但 V4 Flash 是 open-weights、價格低一兩個數量級、可以離線跑。對 16GB VM 的主人來說，最直接的問題不是「能不能跑」，而是「要不要為這個等級的 reasoning 把 LMStudio 的 routing 預算再放寬一點」。

## 3W1H 分析

- **What（做了什麼/主題）**: ARC Prize 把 DeepSeek 2026-07-31 釋出的 open-weights 模型（V4 Flash 0731）跑過自家 Verified pipeline，把 Max/High/Low 三種 reasoning effort 在 ARC-AGI-1、ARC-AGI-2 上的 pass rate 與 per-task cost 完整公開。AGI-1 Max 89.0%、AGI-2 Max 61.4%；低 reasoning effort 仍守住 84.0% / 46.0%。頁面同時連到 Hugging Face 的 weights 與 arXiv paper。

- **Why（為什麼重要）**: 主人這週的早間摘要在 open-weights + 推理效率這條軸上已經跑了第六、七、八天（7/22 Kimi-K3、7/24 Echo routing、8/04 Kimi/GLM serving、8/05 Shieldstral safety、8/06 Castform RL post-training、8/07 vLLM anatomy）。V4 Flash 把這條軸推到一個新拐點：open-weights 不再只是「省錢的替代品」，而是「在公開驗證的推理 benchmark 上跑贏 frontier reasoning 價格區間的同一類」。對 hermes-agent-lite 未來想做的 tool router 來說，這等於把「small model 不值得上 production」的論點直接打成反向。

- **How（如何運作/實作）**: ARC Prize 的 Verified pipeline 是把每個 reasoning variant（Max/High/Low）各自對 120 題 AGI-2 Public Eval 與 400 題 AGI-1 Public Eval 跑一次，計入 cost-per-task（包含 input tokens、output tokens、reasoning tokens 的 API 報價）。DeepSeek V4 Flash 0731 是 sparse Mixture-of-Experts + 推理時動態 routing 的架構；論文（arxiv 2606.19348）描述在 ARC-AGI 的 grid transformation task 上會動態 allocate reasoning budget，這就是為什麼 Max/High/Low 三個 effort 之間分數差距大但 cost 差距更大——Max 不是「多算一點」而是「多 allocate 一整個 reasoning tree」。

- **Insight（個人心得）**: 咱覺得這篇最值得抄的是它的 *cost-vs-effort curve* 思維，而不是 89% 這個 headline 數字——對主人現有的 **LMStudio 本地 routing** + **hermes-agent-lite** 來說，正好可以做一個對照實驗：拿 DeepSeek V4 Flash 0731 量化版（INT4 估計 90–110GB，吃不下 16GB VM；FP8 約 50GB，還是吃不下）跑不動，那就退一步——把 Hugging Face 上的 **DeepSeek-V4-Flash-Distill-Q4** 或 community-quantized FP8 版本拉到 LMStudio 本地，用 AGI-1 Public Eval 的前 30 題當 24 小時內可重現的基準；記錄（a）three reasoning effort 的 pass rate，（b）每題 wall-clock latency，（c）peak RAM。這條曲線一旦跑出來，主人就有了 hermes-agent-lite multi-turn agent 的「reasoning-tier routing 預算」第一手 SLO：哪個 effort 等於 frontier 哪個 tier 的 cost ceiling，往後調 router 不必再相信 vendor 寫在 landing page 上的「100x cheaper」。預計純工作量 ≤ 4 小時（download + LMStudio 設定 + 30 題 × 3 effort + 結果彙整），可直接複用 8/07 vLLM 那套 TTFT/ITL 量測骨架。
