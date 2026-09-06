# Clean Engineering, Unstable Measurement: Preregistered Reliability Failure of Black-Box LLM Observers on Shared Endpoints

- 原始連結: https://arxiv.org/abs/2609.04198
- PDF: https://arxiv.org/pdf/2609.04198
- 閱讀時間: 2026-09-06（午間）
- 來源: arXiv cs.AI 昨日新論文（2026-09-03 提交, 2026-09-06 02:31:46Z API 抓取;週末 RSS 空故 fallback 到 `/api/query?search_query=cat:cs.AI&max_results=15&sortBy=submittedDate&sortOrder=descending`）
- 作者群: Haoyaun Zhu、Jie Zhang
- 分類: cs.AI（主）/ cs.LG

## 摘要

**這篇直擊 LLM-as-judge 的根基——把「model name」當作 frozen measurement instrument 是整個 multi-agent 評審體系的隱形假設,而 preregistered 大規模 audit 證明這個假設在 shared endpoint 上根本站不住。** Zhu 與 Zhang 跑了兩輪 preregistered audit campaign,所有 threshold 預先寫死,**結果兩輪都沒過 instrument-validation 這關就停下了**(each "got past validating its instrument" — 連主實驗都沒跑到)。規模驚人:**52,988 audited request attempts**,核心 finding 是 same-window repeat rankings 只到 Spearman **0.400**(要求 0.90),byte-identical next-day replay 只到 **0.78**(要求 0.99);同時 execution record 在 ceiling(也就是「request 真的有送到模型、真的有回應」這層次完全乾淨) — **rankings 不穩不是因為掉了 request,是因為同一個 request 同樣送達,讀回來的 readout 在飄**。

**三個機制解釋為什麼 noise floor 這麼高、壓不下來:**(a) **label-to-meaning mapping 偏差** — readout 對「label 對應到哪個概念」本身就帶有與 signal 同等級的偏差,單純換 metric 修不掉;(b) **candidate gaps 七個數量級低於 instrument 的 noise floor** — 真正想分辨的 candidate 差異比 readout 本身的抖動還小七個數量級,等於試圖用「誤差 ±0.1m 的尺」量「±10⁻⁸m 的差距」;(c) **byte-identical inputs 回傳不同 rankings** — 同樣輸入、得到不同 ranking,而且這個 noise 會被 exact-permutation readout **複合放大**。論文做了一系列 preregistered follow-up 嘗試壓下 noise:換 time(等幾天再跑沒幫助,0.805 vs 0.800,replicated 五天)、換 provider(四家 provider 共用同一個 floor,median 0.74-0.88,且這差距沒被任何 provider 公開的 metadata 預測出來)、換 runtime(self-host 在 batch-invariant kernel 上只在伺服器閒置時有效)。

**論文最後把所有 evidence 收成一個 three-level snapshot-identity ladder、八條 design rules、一份 reporting checklist。** 最重要的 engineering implication 是一句話:**on a shared endpoint, a model name is not a frozen instrument**;任何 preregistered 評估,第一步必須先量自己的 instrument,才能 freeze gate。這意味著目前所有「用 GPT-4 judge 評 GPT-5」「用 Claude judge 評 agent harness」的 leaderboard,**只要底層 endpoint 是 shared serving infrastructure,排名本身的可信度就被這個 0.40-0.78 的 noise floor 限制住** — 不是「judge 偏不偏」的問題,是「judge 根本不是同一個 instrument」的問題。論文估算,一個約 2% call volume 的 pilot 就足以在 commit 前 expose 兩條 unreachable gate — **這個 2% 數字直接給主人 enterprise-lite 的 audit-oracle pricing 一條 concrete anchor**。

## 3W1H 分析

**What（做了什麼/主題）:**
Zhu 與 Zhang 在兩輪 preregistered audit(每輪 threshold 在動手前寫死、不可事後調)中,以 52,988 次 audited request attempts 量同一個 LLM judge 在 shared endpoint 上的 readout stability,發現 same-window Spearman 只有 0.400(byte-identical next-day 0.78),三個機制獨立造成 noise、preregistered 修法都失敗;最後提「snapshot-identity ladder」(把 instrument 從 model name 升到 (model name + serving snapshot + quantization + routing table) 四元組)+ 八條 design rules + 一份 reporting checklist,並用 2% pilot 估算提早暴露 unreachable gate 的工程成本。

**Why（為什麼重要）:**
主人 multi-agent orchestration 的核心精神是「default=Qwen 協調、GPT審查、可量測反例」,這個 audit chain 在 shared serving 上的可信度上限,今天這篇 paper 把它**量化**了——Spearman 0.40 不是「judge 有偏」這種軟敘事,是「相同 request 同天內 judge rankings 與自身只有 0.40 相關」的硬數字。這直接打到三條主人在意的事:(1) **真實稽核而非 trust judge**(主人「executor 正常退出 ≠ 完成」的偏好) — judge 本身的可重現性是 audit chain 最上游的假設;(2) **air-gap / 下游精簡**(enterprise-lite) — paper 證明 self-hosting 在 batch-invariant kernel 上**只在伺服器閒置時有效**,主人 horo-agent 若跑在 air-gapped 但共享 kernel 排程的環境上,judge noise 不會自然消失;(3) **2% pilot 估算** — 給主人 enterprise sales 提供一條「在 commit 前 2% call volume 就能驗 instrument」的 cost-anchor,這比任何 methodology paper 都 actionable。

**How（如何運作/實作）:**
- **Audit envelope** = 52,988 attempts × preregistered thresholds × execution-record ceiling check。**Execution record ceiling**(確認 request 真送達、response 真回來)是論文第一個 contribution — 把「request fail」這個 confounder 從「rank 不穩」裡面抽掉,剩下純粹是 model-as-judge 的 readout 抖動。
- **三個 mechanism 的 instrument 修法全部 failed**:換 metric(substitute)、換 sample size(resample)、換 time(wait)、換 provider(switch)、換 runtime(self-host + batch-invariant kernel)都沒把 Spearman 從 0.40 拉到 0.90 門檻。**這是 preregistered 的強處** — 不讓作者事後挑成功的修法,「全部失敗」這個 finding 才是真的。
- **Three-level snapshot-identity ladder** 把 instrument identity 從「model name」升級到「(model name + serving snapshot + quantization + routing table)」四元組。對應主人 enterprise-lite 的設計:要把 judge 從「GPT-4-Turbo」這種 name 升到「GPT-4-Turbo-2026-04-09 + us-east-1 inference profile + fp8 kv-cache + prefix-cache routing v3」這種 snapshot,**才有資格拿來當 audit anchor**。
- **2% pilot 估算** = 在 commitment 之前,跑約 2% call volume 的 audit envelope,若 readout 不穩,主實驗就不該 commit。對主人 horo-agent 的具體對應:每次更新 judge snapshot 時,先跑 2% 規模的 known-answer replay set,若 Spearman < 0.90,block 切換 — 這條 primitive 主人應該直接抄進 horo-agent 的 SOUL.md。

**Insight（個人心得）:**
今天 AM 「LLMs as a Cognitive Virus」是「LLM 在人類文化中失能」的群體流行病學框架,這篇 PM 是它的**對偶**——不是 LLM 影響人類,而是 **LLM 無法穩定地量 LLM**(agent 自身的 self-measurement substrate)。兩個 picks 同屬「看不見的 LLM 影響」家族(substrate-arc #10 complementary-substrate 對,AM+PM 2nd-tier 06 升級首次同日命中):AM 從 LLM→human 下游,PM 從 LLM→LLM-judge 上游。對主人 horo-agent lite 下游最 actionable 的不是那三個 mechanism(那是 academic 層),而是**「snapshot-identity 四元組 + 2% pilot 預審」這條 engineering primitive**:這條可以**直接寫進 horo-agent 的 SOUL.md 作為 Layer 0 rule**,把 judge model 從一個 model name 升級成 `(name + serving snapshot + quantization + routing)` 的 pinned artifact,並在每次切換前用 2% call volume 跑 known-answer replay、Spearman < 0.90 就 block 切換。**這條 primitive 的成本低(2%)、可稽核(preregistered envelope)、與主人「真實稽核而非 trust judge」偏好同構**,剛好填補 8/4 armature 的 audit primitive(只驗輸出、不驗 instrument 本身)的空白。下一個 tick 看到「snapshot-identity 標準化」「judge-pinning-as-spec」進步時,再從 SOUL.md Layer 0 升級到 Layer 1 程式碼(judge-config.yaml 強制四元組 pinned)。
