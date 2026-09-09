# The Audit Decides the Verdict: Instrument Effects Rival Demographic Bias in LLM Decision Audits

- 原始連結: https://arxiv.org/abs/2609.09048
- PDF: https://arxiv.org/pdf/2609.09048
- 閱讀時間: 2026-09-09（午間）
- 來源: arXiv cs.AI 昨日新論文（2026-09-08 17:08:53Z API 抓取，今日 weekday 故 `/api/query?search_query=cat:cs.AI+OR+cat:cs.CL&max_results=20&sortBy=submittedDate&sortOrder=descending` 拿到 20 篇，本篇從 12-candidate 評比中勝出）
- 作者群: Siddharth Vohra、Manikandan Ravikiran
- 分類: cs.AI（主）/ cs.LG
- 與同日 AM 關係: AM = Quesma Qwen3.8 27B 量化基準（inference / cost-of-token substrate）；本篇 = audit-methodology reliability（verify / verdict 來源 substrate）—— substrate-arc playbook 2nd-tier complementary-substrate 對（pair 升級第 2 個同日命中）
- 與近 5 日 PM 同族 reference: 9/02 CrossAudit / 9/06 Clean Engineering Unstable Measurement / 9/08 Trusting-Trust — 三篇都是「audit / verifier / measurement substrate」家族；本篇是「audit 提問方式本身就是 dominance factor」的 negative-ROI finding，補充 9/06 的「single-endpoint reliability 0.40-0.78」一階 + 9/02 CrossAudit 的「cross-vendor ledger」三階 = family 第三個獨立 evidence layer

## 摘要

**這篇是「audit 提問方式本身就是 verdict 的主要變因」——LLM 是否看起來有 demographic bias，取決於 audit 怎麼問，而不是模型怎麼回答。** Vohra 與 Ravikiran 預註冊（preregistered）一次大規模 LLM decision audit:同一組 hiring / lending / medical triage 場景，**40,726 個 requests 到 5 個模型，唯一變因只有申請者的「名字」**（其餘 demographic 結構凍結）。**預先寫死的 36 組 contrasts，沒有一組通過多重校正**（none of 36 planned contrasts survives correction）——「rating 順序保持方向但只有原報告的一半大小，ranking 反向在三個 domain 中，hiring 是排他性被 bound 出來、但 lending 與 medical triage 的 ranking floor 都還在 bound 之上」。Plant 進去的真實 disparity 會被正確跟到（**planted disparity tracking**：他們注入的 signal 大小，模型 readout 完整 track 進來），代表性 replication 在原始 aid 數據上把「audit 提問方式 vs 信號方向」的 null 都 bound 起來。

**真正的凶手不是 demographic，是 audit 本身的構造：audit 比 demographics 還「活」**——三個獨立的 audit-instrument effect：(a) **模型辨識透明 audit 幾乎永遠命中**（models recognize transparent audits nearly always），(b) **identical content 的兩份申請，無論差異是 race 還是 hobby 都被 tied 起來**（tie identical-content comparisons regardless of which detail varies），(c) **first-listed candidates 拿到與任何 demographic effect 一樣大的加分**（reward first-listed candidates as much as any demographic effect we measure）。論文結論一字一句：**「Audit verdicts reflect audit construction more than demographic bias.」**

**對主人的 engineering 警示：今天已有的 LLM-as-reviewer / LLM-as-judge pipeline 是不是「verdict 來源」還是「auditor 構造的反射」**——尤其當主人 kanban `chatgpt-reviewer` profile 開始用同一個 GPT 系列評多個不同形狀的 task (executor 完成確認 / spec 驗收 / PR diff 評分) 時，audit 構造的任何變化（評審 prompt 多一句「請嚴格」/ model snapshot 換行 / temperature 從 0 變 0.3）都會被讀成「bias 改善／惡化」的假訊號。**「36 planned contrasts 全部不顯著」這個 preregistered null，應該被當成 master kanban reviewer profile 的 audit-pipeline 預警**：在 commit verdict 之前，必須先把 instrument 本身的 construction dependency 量出來——這跟 9/06「snapshot-identity 四元組 + 2% pilot 預審」是同一條 primitive，**今天這篇再加一條：在 audit 提問方式本身的可重現性上，加一個「construction-pinned envelope」**。

## 3W1H 分析

**What（做了什麼/主題）:**
Vohra 與 Ravikiran 預註冊一組 LLM decision audit：40,726 requests 送到 5 個模型、跨 hiring / lending / medical triage 三個 domain，唯一變因只有申請者「名字」，主實驗的 36 組 contrasts 全部不顯著——既證明 charitable-aid benchmark 那篇 famous 報導的反向效應不能 generalize、又用 planted disparity tracking 證明信號注入路徑是通的、還用 directional replication 把 null 都 bound 起來；最後歸納出三個 audit-instrument effects (transparent-audit recognition / identical-content tie / first-position-bonus) 證明「**audit 比 demographics 還活（the audit is livelier than the demographics）**」。

**Why（為什麼重要）:**
主人目前「`chatgpt-reviewer` profile」與「executor → 完成確認」的 audit chain，**正處在這篇 audit-paper 直接打臉的高風險區**——因為主人的 review prompt 結構跨任務（executor agreement / spec conformance / diff score）有顯著差異，這些 audit construction 的差異，比 executor 本身的行為還更顯著地決定 verdict。作者群 showcase 的「first-position-bonus raval demographic bias」直接中槍主人 kanban 把 PR diff 從第一行列到最後一行的所有 review prompt 格式——**結論前置 / bullets 順序 / 「看看有沒有錯」開頭 vs 「嚴格驗證」開頭，這些 prompt 結構的 delta 跟任何 demographic effect 一樣大**。更重要：本文是 preregistered 主實驗全部 null 的 paper——**主人「executor 正常退出 ≠ 完成」「真實稽核而非 trust judge」的偏好，需要的不只是「snapshot-identity 四元組」（9/06 那條）+ 「2% pilot 預審」（同 9/06），還要加一條「audit construction envelope」——就是「今天 reviewer 的 prompt 結構、欄位順序、開頭句」要被 pin 進 audit log，跟 model snapshot + serving snapshot 同層級**。

**How（如何運作/實作）:**
- **Audit envelope** = 40,726 requests × 5 models × frozen applicant names × pre-registered 36 contrasts × preregistered primary test fixed before collection。**Planted disparity tracking** 是論文的關鍵 sanity check——故意注入一段「實際存在」的 demographic disparity，readout 必須完整 track 進來才算 instrument 可信；今天這層通關，才允許「null result」這個結論被嚴肅看待（planted disparity 不通的話，null 就是無能檢測，不是真的沒有 bias）。
- **三個 audit-instrument effect 的 mechanism 拆解**：transparent-audit recognition（模型聽到「audit」這個字就在打招呼） / identical-content tie（無論差異是 race 還是 hobby，相同 payload 就被 tie 起來——這個 tie 機制把 audit 的 falsifiability 直接拿走） / first-position-bonus（**首列答案加分——這條直接與主人 kanban review prompt 結構同構**）。
- **Preregistered null 的工程價值**：今天的主人 enterprise-lite + air-gapped 下游只要 review pipeline 還沒 pre-publish construction envelope，**那整個 reviewer verdict 就是「37 行 construction 字串」的反射，不是 model behavior 的測量**——這個訊號比 9/06 的「endpoint 抖動 0.40-0.78」更上層，因為 endpoint 抖動是 **substrate noise**，audit-construction 是 **substrate signal**。9/06 + 9/09 兩篇一起讀才能完整看到：audit 上的不可重現性同時來自「endpoint 抖」(substrate noise) + 「construction 變」(substrate signal)。
- **三層 audit-pin 工法的進化 roadmap**（與 9/06 那層 primitive 互補而合併）：(1) snapshot-identity 四元組（model name + serving snapshot + quantization + routing table，9/06 那條）；(2) 2% pilot known-answer replay（commit 前先用 2% call volume 驗 readout 0.90，9/06 那條）；(3) **construction-pinned envelope（本篇新條）——把 review prompt 的 structure hash / 欄位順序 / 開頭句 / temperature，pin 在 audit log 裡跟 model snapshot 同位**。

**Insight（個人心得）:**
今天這篇是 **9/06 PM 「on a shared endpoint, a model name is not a frozen instrument」 的二階姊妹**——9/06 證明「同一個 LLM judge 在不同 snapshot 下讀不回同一份東西」，本篇證明「同一個 LLM judge 在不同 audit construction 下讀回完全相反的 verdict」。**這兩篇要一起讀，才能完整理解主人 kanban reviewer profile 的 audit substrate** —— 一個是 endpoint 抖（substrate noise floor），一個是 audit construction 變（substrate signal source）。**對主人 horo-agent lite 下游 + air-gapped 最 actionable 的 primitive 就是 audit-construction pin envelope**：把 reviewer prompt 的 structure hash / 欄位順序 / 開頭句 / temperature，全部 pin 在 audit log 跟 model snapshot (9/06 那條) 同層 —— 這條 Layer 0 rule 應該直接寫進 horo-agent 的 SOUL.md，**讓「reviewer verdict」從「model + serving snapshot」二元組升到「model + serving snapshot + audit-construction snapshot」三元組**，理由是本篇 36 contrasts 全部不顯著證明：**二元組還不夠，第三個 construction-pinned snapshot 才是 audit verdict 的真實錨點**。具體可量測的下一步：在 `~/.hermes/agents/` 下加一個 reviewer-config YAML（< 100 行），`audit_construction_hash` 跟 `model_snapshot` 同位存放，每次 review 完成時把這個 hash 寫進 Kanban comment 與 Discord 回報，任何 hash 漂移都自動拒絕 merge reviewer verdict 到 main —— 這條 primitive 在跟 8/4 armature 的 audit primitive（只驗輸出、不驗 instrument 本身）、9/06 snapshot-identity（驗 instrument 卻不驗 construction）合成 **完整 audit-anchor 三元組**：output + instrument + construction，主人 kanban reviewer profile 這條 pipeline 要從 primitive-3 元化升級才算追上 9 月初這兩篇的 headline finding。
