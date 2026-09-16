# Training a 4B Model to Produce 81% Faster Query Plans than Postgres
- 原始連結: https://rohanbansal.com/qorl
- HN 討論: https://news.ycombinator.com/item?id=49731285
- 程式碼: https://github.com/polyphilz/qorl
- 閱讀時間: 2026-09-17（早間）
- 來源: Hacker News 熱門前 10 第 1 名（311 分 / 59 則留言，讀取時 2026-09-17 07:01 Asia/Taipei）

## 摘要

**主人，這一篇是「用 4B 小模型 + RL 在特定子領域擊敗 70 年歷史 optimizer」的工程級實錄——同時是 9/12 以來 agent-harness / human-bridge / ML-as-optimizer 軸線的最高強度連擊點。** 文章作者 Rohan Bansal 在 Recurse Center 沙發年期間，用自己的 2×RTX 3090 主機（FLOPper）+ 租來的 2×H100 + $400 OpenAI API，把一個 4B Qwen3.5 蒸餾模型從「看不懂 qo-agent harness」訓練到「在 113 條 JOB join-heavy 查詢上比 Postgres 預設計畫快 1.81× 幾何平均、總工作量降 44.7% 延遲」。HN 留言區 59 則裡 24 則是 substantive 機制討論（fool-rate 量測、shared_buffers 雜訊、agent-as-optimizer 範式），不是 vendor release cluster，也不是 swag thread。

**核心機制：verifiable-reward + agentic-RL + small-model distillation 三件套** 文章把 GRPO 改成 **anchored variant**（相對於 default plan 執行時間算 advantage，不相對於 group mean），把 RL rollout 的 reward 形塑成「能不能打敗 default Postgres plan」這個**單軸可驗證訊號**——這正是 R1-Zero 那條 verifiable-reward 路線在 query-optimizer 子領域的工程化實例。三階段：off-policy SFT（120 條 Astra traces + LoRA，1 epoch → 2 epoch → 3 epoch 退步）→ RL（anchored credit，1,200 updates）→ best-of-15 評估。每階段都有 measurable 邊界：SFT 1 epoch valid candidate 71/113，RL 後 113/113 valid + 1.41× speedup，best-of-15 拉到 1.81×。

**為什麼主人會想讀——這是 16GB VM + agent-harness + Hermes-stack 的「niche-task small-model」範式骨架** 三個 transferable primitive 直接落在主人手上：
1. **Measurement rig as substrate**——4 容器 × 4 core × 8GB RAM、128MB → 2GB shared_buffers 的 fool-rate 把 5% 降到 1.2%；no-op error rate 從 13–20% p90 降到 0–1.3%。對應到 hermes-agent-lite 的 verifier / measurement loop：**reward signal 是 RL 成敗的第一因**，不是模型大小。
2. **Worker lease strategy**——vLLM 推論時釋放 Postgres worker（避免 inference 空檔把 worker 鎖死），讓 20 個 rollout 並行跑在 4 個 worker 上。對應到主人 council / advisor 多 worker 架構：**idle resource 不該被持有**。
3. **Verifiable-reward shape**——把 GRPO 從 group-mean 改成 default-plan-anchored，避免「全組都沒打敗 baseline 還拿到 positive advantage」的 phantom reward。對應到 hermes-agent-lite 的 task-completion verification：**reward 必須 anchored 到 ground-truth，不是相對 ranking**。

**與 9/12 → 9/15 同軸連擊的關係** 9/12 Tao 連署警告「AI 自動化掏空 artifact 價值」、9/15 Litt 提出「rigorous defense」當 allocatable unit、今日 Bansal 給了**「AI 自動化下如何保住 artifact 價值」的可執行範例**——證明「在 niche 子領域上 small-model + verifiable-reward 反而比 frontier model 更有性價比」。frontier 模型的蒸餾（100 條 Astra trajectories → 4B）是「大型智慧給小型子任務」的具體案例，跟 Litt「研究計畫取代單篇 paper 當 allocatable unit」同構。同時，這是 8/04 Cloudflare → 8/07 vLLM → 8/11 Muse Glimmer/DFlash → 8/20 DFlash → 8/22 Nari TTS inference-optimization 軸線累積的第 6 個 pick——但跟前 5 個不同，今日不是「serving-engine optimization」，而是「**niche-task small-model optimization**」這個全新子軸線，substrate-identity 從「shared scheduling surface」升級到「verifiable-reward + agentic-RL + small-model distillation」。

## 3W1H 分析

**What（做了什麼/主題）:** Rohan Bansal（Recurse Center 沙發年、polyphilz 在 HN）9/16 發布完整工程實錄：用 Qwen3.5-4B 蒸餾（empero-ai/Qwen3.8-4B-Distill）做 supervised fine-tuning（120 條 GPT-6 Astra 軌跡 + LoRA，21M 參數）→ custom anchored GRPO RL（600 → 1,200 updates，reward = `clip(median(default)/median(candidate), 0.1, 10)`，anchored 到 default plan 執行時間而非 group mean）→ 三種 rollout 評估（model's own choice 1.40×、best within trajectory 1.44×、best across 3 trajectories 1.81×）。總成本 $1,200（$800 H100 + $400 OpenAI API + $9/day 電力），113 條 JOB 查詢 workload 總延遲降 44.7%。附 qo-agent harness（5 個 tool：get_plan / evaluate_candidate / finish + 兩個輔助）、pg_hint_plan 作為 plan 注入介面、Prime Intellect prime-rl 作為 RL 框架。

**Why（為什麼重要）:** 三個 transferable insight 直接掛上主人 stack——
(a) **小模型 + verifiable reward + agentic RL** 擊敗 70 年歷史的 optimizer（Postgres 採用 cost-based optimizer 自 1980s 起），證明「frontier intelligence 是 teacher，niche intelligence 是 student」這個範式骨架在 production 環境成立；
(b) **measurement rig 設計是 RL 成敗的第一因**——reward 雜訊從 5% 降到 1.2% 的不是模型，是 4 容器 + 2GB shared_buffers 的 calibration protocol；
(c) **$1,200 + 2×RTX 3090** 是可重現的 baseline——對應到主人 16GB Mac Mini VM，「niche 小模型 + verifiable reward」的算力門檻在 2026 年下半年已經降到個人消費級。
這跟 9/15 Litt「rigorous defense 才是 allocatable unit」、9/12 Tao「artifact 自動化掏空制度」、8/13 Tailscale SQLite 「pin substrate version」、9/8 Trusting-Trust「bootstrap toolchain 是 6th trust boundary」共同構成一條**「AI 自動化下如何保留人類制度價值」的軸線**——今天 Bansal 用 113 條查詢證明了 engineering-level 的可行性。

**How（如何運作/實作）:** 三階段 pipeline：(1) **SFT via off-policy distillation**——120 條 Astra trajectories 用 Prime Intellect renderers 轉 Qwen 格式、loss-masking（只 mask model 預測的 tokens，不 mask context）、LoRA 21M 參數在 3090 上跑 1 epoch；3 epoch 退步（驗證 loss 不動但測試集退步），所以選 2 epoch；(2) **anchored GRPO RL**——reward = `clip(median(default_time)/median(candidate_time), 0.1, 10)`，anchored 到 default plan 而非 group mean；worker lease strategy：rollout 結束時立刻釋放 Postgres worker，下一個 rollout 才能接上；(3) **best-of-N 評估**——每 query 跑 3 trajectories，每 trajectory 5 candidates，總共 best-of-15。三個 substrate-aware 細節：(a) `pg_hint_plan` extension 讓 EXPLAIN plan 可程式化注入（無需改 Postgres source）；(b) `EXPLAIN (ANALYZE, TIMING OFF, BUFFERS, FORMAT JSON)` 暴露 shared hit/read blocks 計數，用來判斷 warmup 是否真的熱機完成；(c) `shared_buffers = 2GB` 把 5% fool-rate 降到 1.2%，比 `work_mem` 從 4MB 改 32MB 還有效。

**Insight（個人心得）:** 主人，這一篇在 substrate-identity 上是 **8/04 → 8/13 → 8/21 → 9/8 軸線的「make invisible substrate behavior observable」家族**的**第 6 個 instance**——但跟前 5 個不同，這次驗證的不是 visibility，是 **verifiable reward**。文章最強的 portable primitive 是 anchored credit（reward 必須相對於 ground-truth 而非 group mean），對應到 hermes-agent-lite 的 kanban task-completion verification：今天主人的 kanban task reward 是「executor self-report」，未來可以加一層 **anchored verifier**——把 reward 從「executor 自己說完成」改為「anchored 到 spec.md 第 N 條」+ 「anchored 到 test exit code 0」+ 「anchored 到 live smoke evidence」。**1 小時 prototype 路徑**：在 `kanban_complete` 之前加一個 `verify_anchor(spec_path, claimed_result)` hook，regex 抓 spec.md 編號條目 → grep executor 提交的 handoff 裡是否每條都有 exit code / log evidence → 沒有的退回 request_changes；這跟 8/04 KV-cache 8-byte tag + 8/15 SOUL.md text-rule + 9/8 Trusting-Trust toolchain.lock 是同一族「Layer 0 text rule 優先於 Layer 1 code」，< 50 行 Python、< 1 hour commit、no LLM call。同時——4B Qwen3.5 蒸餾 + $1,200 + 2×RTX 3090 的算力 envelope，**直接映射到主人手邊的 Qwen3.8-27B (qwen38-code on 192.168.0.56:2234/v1, max_turns=20)**——下一個 hermes-agent-lite 內部 experiment 可以挑一條主人 kanban 軸線（例：query plan、test ordering、tool-routing）做同樣的 SFT + anchored-RL pipeline，預算 $1,200 / 4B / RTX 3090 envelope，預期能在主人 16GB VM 上跑出 niche-axis 的 1.5–2× speedup。
