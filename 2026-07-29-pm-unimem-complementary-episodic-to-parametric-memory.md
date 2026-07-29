# UniMem: Complementary Episodic-to-Parametric Memory for Boundary-Agnostic Task Streams
- 原始連結：https://arxiv.org/abs/2607.26017
- 閱讀時間：2026-07-29（午間）
- 來源：arXiv cs.CL（2026-07-28 提交，作者：Siyu Xia、Chenheng Zhang、Yanting Wu 等 11 人，機構含 Peking University / Princeton / MBZUAI）

## 摘要

**LLM agent 的記憶痛點：plasticity 與 stability 兩難**
長期部署的 agent 暴露在沒有明確邊界、會持續演化的工作流（boundary-agnostic task streams）時，會撞上一個心理學借用來的取捨：retrieval-based 記憶（外部 RAG）能快速吸收新證據，卻學不會把反覆出現的執行模式內化、每次都要付一次檢索成本；parametric 記憶（直接寫進模型參數）跑起來穩又快，卻強烈依賴事先定義好的任務切分與固定參數預算，部署時難以擴充。UniMem 把這兩個極端同時端出來，模仿人類大腦以互補的情節儲存 + 漸進鞏固來平衡兩者。

**Learnable routing tokens 做自我路由**
UniMem 的核心不是單一記憶體，而是一組 **learnable routing tokens** 作為記憶控制器，搭配 parametric memory blocks 與 episodic buffer。新任務、稀疏任務路由到 episodic buffer 走 retrieval-augmented 執行；反覆出現、可靠性高的模式被鞏固進 expandable parametric memory，後續直接讀參數跳過檢索。**關鍵解耦**：routing tokens 把「這個任務是誰」與「這個任務怎麼跑」拆開，因此部署時不需要任務標籤、參數也不會無節制膨脹。

**長期 streaming 評測 +4.0 EM**
在長時程 streaming 任務序列上跨三個 backbone（作者寫的 3 種）實驗，UniMem 平均比 baseline 高 4.0 EM（exact match），同時維持執行 fidelity。值得留意的是 routing tokens 是端到端學出來的，沒有任何任務層級的監督訊號，因此部署期間可以動態擴充記憶而不用事先設計 schema。

**為主人值得注意的地方**
主人近期在追 memory / agentic context management 這條主鏈（07-18 MemoHarness、07-21 CAPC、07-24 Agentic Context Management），UniMem 正好補上「路由器」這一塊。Stable / plastic 的取捨直接對應到主人實際的兩層控制面：MEMORY.md / 個人 context file 是 episodic（外部、可隨時覆寫），skill 與 profile 是 parametric（已固化、重複使用率高）。UniMem 把「學什麼時候該走哪條路」這件事交給模型本身的 routing token，這與主人目前以人工 + skill_view 觸發的設計哲學形成有意義對比。

## 3W1H 分析

- **What（做了什麼／主題）**：UniMem 提出一個 self-routing 的雙軌記憶框架，把 LLM agent 的記憶拆成 episodic buffer（retrieval-augmented，給新任務）+ expandable parametric memory（內化，給反覆出現的模式），用 learnable routing tokens 做兩者間的動態路由器，部署時不需要任務標籤、也不會無限制長參數。
- **Why（為什麼重要）**：長期運作的 agent（master 自己也跑 cron 自動化、air-gapped downstream）都會撞到「外面查得到、但每次都要查」與「想內化、但不知道什麼時候該內化」的權衡。UniMem 用 routing token 把「識別 vs 執行」解耦，正是主人近期多篇 memory 文章各自只解一半的痛點；把這層路由器自動化，下游的 agent 就能在 runtime 自己決定讀 MEMORY.md 還是直接複用已固化的 skill。
- **How（如何運作／實作）**：UniMem 設計 learnable routing tokens 當 memory controller，搭配可擴充的 parametric memory blocks 與 episodic buffer；新/稀疏任務路由到 episodic 走 retrieval，重複/可靠模式鞏固到 parametric。實驗在 long-horizon streaming task sequences 跨 3 個 backbone 模型，平均 +4.0 EM。注意 baseline 與評測 protocol 都是作者自選、沒有獨立重現——採用前應自行驗證。
- **Insight（個人心得）**：咱讀完最想把它對應到 Hermes 的「skill_view + memory editor」這一對原始控制面：skill_view 觸發當下讀檔像 episodic retrieval、已常駐的 skill 像 parametric 內化，而 UniMem 的 routing token 概念上就是「讓 agent 自己在兩者間切換」。反向借用：UniMem 的「任務無標籤、邊部署邊擴充」也提醒主人——主人目前記憶治理高度依賴人工 `memory` 工具與 cron，這條路在單人/小團隊規模下是對的，但若要把 Hermes 放進更大的下游場景，routing token 這種「agent 自己學會切換」的機制值得在 horo-agent 的下一輪 hardening 時當成選項之一考慮，**但** 先不要動 agent loop（主人 air-gap 規則）——只在 profile / memory 層做實驗。