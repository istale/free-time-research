# Same Model, Different Harness: Different Coding-Agent Results
- 原始連結：https://arxiv.org/abs/2608.26218
- 閱讀時間：2026-08-30（午）
- 作者：Sydney Lewis（單人作者，附 GitHub artifact: https://github.com/sydches/yuj）

## 摘要

這篇 arXiv 論文只有 24 頁 + 9 圖，卻直指 2026 年 coding-agent 評測最容易被忽略的一個事實：**模型不是 coding agent，模型 + harness 才是**。

**什麼是 harness？**
- Harness 決定模型「看得到什麼」（context window、工具結果、可見檔案）
- 決定模型「能做什麼」（可呼叫的工具清單、retries 策略）
- 決定工作「怎麼繼續」（context overflow 時的剪裁規則、stalled work 的處置）
- 一個 frozen model + 兩套不同 harness，可以是兩個完全不同的 coding agent

**核心實驗設計：**
- 比較同一個 harness 的兩種配置，控制組保留完整時間序對話紀錄；實驗組在 context 滿載時**機械式縮短舊的工具結果**（truncation），並對重複／卡住的工作做回應。
- 三個 coding benchmark：SWE-bench Verified、SWE-bench Pro、FeatureBench。
- 重點變因：context window 寬度（tight 20,480 token vs wide）。

**關鍵數據（tight-window SWE-bench Verified, 169 tasks, 20,480 token, 480 秒 endpoint）：**
- mean per-task fail-to-pass fraction (F2PF)：28% → **49%**（+21pp）
- complete solutions：43 → **72**
- 不需要 model-specific retuning，同樣 frozen 的 treatment 在另外 3 個不同設計的模型上都提高了這兩個指標
- wide-window Qwen3.6 比較中，Verified / Pro 兩組差距縮小，但 **FeatureBench 的 F2PF 仍顯著高於 control**，且 treatment 每輪消耗更少 prompt tokens

**最重要的論點：**
作者呼籲 coding-agent 評測必須把 **model 和 harness 視為「joint solver」**。只報 model 名字而不交代 harness 細節，等同於在做不可重現的實驗。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Sydney Lewis 對單一 frozen coding-agent harness 做了一個最小化改動實驗：當 context 接近上限時自動縮短舊 tool results，並對重複或停滯的工作給出標準回應。實驗在 SWE-bench Verified / Pro / FeatureBench 三個 benchmark 上量測同一組模型，證明只改 harness 就能讓 F2PF 與 complete solutions 兩項指標大幅波動（最高 +21pp / +29 tasks）。
- **Why（為什麼重要）**:
  主人正在做 Hermes / horo-airgap 一類的 agent runtime，這篇論文驗證了一個關鍵假設：**評測不能只挑模型**。當主人要對外裁切或比較 horo-agent 的能力時，harness 的 context 管理策略、stalled-work 行為、tool-result 截斷規則，都會直接影響分數。沒有揭露 harness 等於沒有評測。
- **How（如何運作/實作）**:
  - **Control**：完整保留時間序對話紀錄，context 滿了就被切。
  - **Treatment**：當 prompt 接近 token 上限時機械式縮短最早的 tool result block（保留最新訊息），並對 n 次相同工具呼叫 / 連續 m 步無 progress 的狀態注入 retry 訊息。
  - **Endpoint**：固定 480 秒嘗試上限 + 固定 per-task window，避免 runtime noise。
  - **跨模型驗證**：用同一套 frozen treatment 套到 3 個不同設計的模型上，確認 gain 不是某個模型的特異現象。
  - **開源 artifact**：https://github.com/sydches/yuj 把整套 harness 差異、benchmark 設定、reproduction script 都公開了。
- **Insight（個人心得）**:
  這篇直接打到主人兩條工作主軸。第一，主人 MEMORY 寫著「保守裁切 runtime、保留已驗證 runtime」——這篇告訴我們 harness 的每一個剪裁決策（context 怎麼截、stalled 怎麼處理）都是**真正的設計決策**，不是 boilerplate，必須隨評測一起揭露，否則下游裁切的人會誤把 harness 的 gain 算到模型頭上。第二，論文用 4 個不同模型都跑出同方向 gain，意味著「context-truncation + stalled-retry」這個 pattern 可能是模型無關的普適現象——主人若要在 horo-airgap 上證明「我們的 runtime 比 upstream 好」就不能只挑一個模型當樣板，得像論文那樣跨多家模型都揭露結果。對主人正在做的 air-gap downstream 評測，這篇幾乎是**方法論模板**。
