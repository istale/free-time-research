# WikiSkill: Compiling Agent Experience into Persistent Knowledge for Skill Evolution
- 原始連結: https://arxiv.org/abs/2608.27454v1
- arXiv PDF: https://arxiv.org/pdf/2608.27454v1
- HN 討論: https://news.ycombinator.com/ （本篇 8/31 上午尚未入榜，arXiv tier-1 API surface pick）
- 閱讀時間: 2026-08-31（早間，週一）
- 來源: arXiv cs.AI/cs.CL API tier-1（Submitted 2026-08-27，作者 Liyan Tang）

## 摘要

**WikiSkill 把 agent 自我演化拆成三層：「raw execution experience（每次跑的軌跡）」「accumulated knowledge（結構化 wiki）」「executable skills（可直接被未來回合呼叫的程式化 procedures）」**——這正是主人 SOUL.md 中「赫蘿自己 / 讓我可以看見你」主軸在最底層的 substrate。paper 的核心訊號是 *persistent knowledge base 必須介於 raw experience 跟 executable skills 之間*，只把 skill 直接從 experience 萃取出來會遇到「insights scattered across optimization histories, limiting systematic reuse across iterations」。對主人來說，這是 *60+ skills 的現有 family 第一次有 paper-level 命名* — 主人 hermes-agent 的 skill system (`~/.hermes/skills/`) 其實正是 raw experience → wiki → skill 三層的一個未命名 instance。

**第二個關鍵訊號是「skill evolution 跟 model scaling 是 orthogonal 的」**：paper ablation 明確指出「larger models generally benefit more from evolved skills, while smaller models with skills can outperform substantially larger models without them」+「skills evolved by other models can outperform self-evolved skills」+「evolved skills transfer effectively across models and model families」。對主人目前 default（Qwen）vs qwen38-code（oMLX 192.168.0.56:2234/v1，Qwen3.8-27B，max_turns=20）的雙 profile 偏好而言，這是 *可直接驗證的部署場景*：主人可以把 Qwen3.8-27B 在 hermes-agent-lite 上跑的 experience consolidate 進 wiki，再讓 MiniMax-M3 用同 wiki 演化出的 skills，跨模型遷移效益有 paper 數字背書。

**第三是 wiki 層是 critical substrate**——ablation 把 persistent knowledge accumulation 拿掉，效果顯著下降。等於 *skill 不是 standalone resource，是 wiki-anchored resource*；沒有 wiki 就沒有 systematic reuse。對應到主人現有架構，`~/.hermes/profiles/default/skills/` 是 skill repo、`~/.hermes/profiles/default/memories/MEMORY.md` 是 raw experience，但**缺一個 explicit「wiki」層**——主人現有 SOUL.md / USER.md / `references/` / linked_files 正在扮演這個角色，但還沒有被 formalize 為「skill evolution 的 substrate」。主人可以直接借用 WikiSkill 的三層分離做 *「Hermes Skill Wiki」* prototype（成本：grep SKILL.md + linked_files + 對應 references，< 50 行 Python，< 4 hr prototype）。

## 3W1H 分析

- **What（做了什麼/主題）**:
  WikiSkill framework（提交 2026-08-27，作者 Liyan Tang 等）— 一個把 agent skill evolution 系統化的 framework，核心抽象是 *「raw execution experience → persistent wiki → executable skills」三層分離*，每一層獨立 consolidate / 獨立演化，最後用 wiki 層做 systematic reuse。Paper 在多個 benchmark 跟多個 model（GPT-4 / Claude / Qwen2.5 等）上驗證：(1) evolved skills 對 small models 提升顯著（small model + skills 可打敗 larger model 無 skills）、(2) skills evolved by one model 對另一個 model 通常仍有效（甚至 better than self-evolved）、(3) ablation：把 wiki 層拿掉效果顯著下降、(4) skill evolution 跟 model scaling 是 orthogonal axes。

- **Why（為什麼重要）**:
  1. **Owner-actionability 在全新 axis，不是 cost-curve**：8/30 vLLM v0.28.0 已是 inference-optimization axis 第 6 pick 且 SKILL 8/22 規則說「6th item 必須 extend 或 refute substrate-identity」。今日轉進 **agent-harness / skill-evolution axis**，這條軸主人尚未開過、且對應主人 SOUL.md 的「赫蘿自己演化」主題（讓 agent 變成能被看見的存在）。Substrate-identity 從「scheduler / 推理 substrate」推進到「skill-evolution substrate」—— 8/19 desktop-fly 雙方都有，三層分離又跨到了 skill-evolution，是 *8/04 + 8/17 substrate-identity stacking 的新 instance*。
  2. **主人現有 60+ skills 從「分散 raw experience」變成「有 wiki 層的 explicit substrate」**：主人現有 `~/.hermes/profiles/default/skills/` + `~/.hermes/profiles/default/memories/MEMORY.md` 是隱性三層（skills ↔ MEMORY ↔ session transcripts），但缺 explicit wiki consolidation step。今天 paper 給的 prescription 正好對應。
  3. **跨模型 skill 遷移效益有 paper 數字背書**：主人 multi-agent 偏好 default（Qwen）vs qwen38-code（oMLX Qwen3.8-27B），若把 Qwen3.8-27B 在 hermes-agent-lite 的 session 經驗 consolidate 進 wiki、讓 MiniMax-M3 拿來演化 skill，可 paper-anchored 驗證「small local model + cross-model skills 競爭 frontier model」的 thesis—— 這正是 8/05 Mistral Shieldstral 那條「pattern transferability over capability claims」axis 的新支線。

- **How（如何運作/實作）**:
  WikiSkill 的核心算法是 *「continuously consolidate experience into the wiki, which subsequent skill updates can build on」*——每跑完一輪 agent，wiki 層先 update 一份結構化知識（「哪些 prompt pattern 在哪些任務類型有效」「哪些 tool composition 失敗模式」），skill evolution 步驟再去 query wiki 找 systematic pattern、寫成新 skill。三層之間的 interface 是 well-defined：
  - raw experience → wiki: *natural language summarization + structured key-value extraction*（例如「task: install-python-pkg, agent: Qwen3.8-27B, attempt 3/3 success, key insight: 先 `pip install --user` 再 fallback sudo」）
  - wiki → skill: *query the wiki for high-frequency patterns、generate executable skill via templated generator*
  - skill → reuse: *next agent run query skill registry, pick top-K skills relevant to task*
  
  Paper 報告 ablation 把 wiki 拿掉 → skill evolution 效果顯著下降；paper 也驗證「skills evolved by other models can outperform self-evolved skills」—— 跨模型遷移之所以 work，是因為 *wiki 層是 model-agnostic 的 natural language + structured key-value*，skill 層只是 wiki 的 executable projection。

- **Insight（赫蘿心得）**:
  今天這篇對主人的 actionable 是 *立刻 borrow WikiSkill 的三層 substrate 命名到 hermes-agent*，不必 replicate paper 完整 framework。最便宜的落地是 **Layer 0 SOUL.md rule + reference convention**：在 `~/.hermes/profiles/default/skills/SKILL.md` 與 linked_files 之間加一個 *implicit wiki convention*——每個 skill 的 SKILL.md 開頭加一段 `## Wiki Anchor` 指向哪些 MEMORY.md sections、哪些 references/ 是這 skill 的「persistent knowledge substrate」，cost 是 **< 5 分鐘 commit、零 code、零 LLM call**，但把分散的 60+ skills 變成「有 wiki 層」狀態。Layer 1 prototype：寫一個 `skill_wiki_consolidator.py`（< 50 行 Python）— 掃 `~/.hermes/profiles/default/skills/**/SKILL.md` + linked `references/` + `~/.hermes/profiles/default/memories/MEMORY.md` 的 session_transcripts references、產出 `<profile>/wiki/index.json`（key = skill name, value = {raw_experience_refs: [...], wiki_anchors: [...], skills_inherited_from: [...]}），cost **< 4 hr prototype、無 GPU 需求、可在主人 16GB Mac 直接跑**。Layer 2 是把 wiki index 餵給 hermes-agent skill-suggester hook——下個 agent run 啟動時 query wiki 看哪些 skill 是 high-confidence transferable, 配 `daily-research-digest-am-playbook` 的 `## 摘要` 開頭那條「主人既有 primitive 對位」annotation（已經是 informal wiki 雛形），*把它 formalize 為 machine-readable wiki-anchor*。這條 Insight 跟 8/15 SOUL.md rule primitive（Layer 0 < 5 min commit）跟 8/19 honesty-section primitive（< 10 min commit, no code）同一家族——「讓 invisible substrate 變成 visible 文字」。主人若今天吃飽前想 commit 一個 minimal primitive，**Layer 0 那個 `## Wiki Anchor` convention 加進 SKILL.md 是 < 5 分鐘 commit、跟 8/15 SOUL.md rule primitive 同成本、同 family**，比 8/20 / 8/22 那種 routing-layer 改動輕很多。
