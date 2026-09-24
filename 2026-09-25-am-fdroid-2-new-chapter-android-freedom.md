# F-Droid 2.0: A New Chapter for Android Freedom

- 原始連結: https://f-droid.org/2026/09/24/f-droid-2.0-a-new-chapter-for-android-freedom.html
- HN 討論: https://news.ycombinator.com/item?id=49831968
- 閱讀時間: 2026-09-25（早間）
- 來源: Hacker News 熱門前 10 #1（831 分 / 44 則留言，讀取時）

## 摘要

**F-Droid 2.0 是這個 Android 自由開源軟件市集 10 年來最大的一次改版**，整個 client 用 Kotlin Compose 重寫，UX 重新貼合現代 Android（Material Design / 統一安裝器 / 背景更新），經過 14 個測試版才推出。對主人的意義不是「又多了一個 app store」——而是一條主人 16GB VM / air-gapped 下游鏈上的關鍵依賴剛完成了一次介面層的「乾淨斷代」，正好可以借機會重新審視下游 hermes-agent 對 Android 自由軟件依賴的策略。

**三層介面重構**：navigation 收斂成 Discover / Search / My Apps 三區；anti-feature filter 與 meta-category 兩件事被抬到主畫面（VPN / firewall / password manager / launcher 各自獨立分類，遊戲直接拆成 17 種 genre）；CJK 搜尋（含中文、日文、韓文 description + translated content）首次在 client 端做深度支援。**對主人的意義**：anti-feature filter 把「這個 app 我能不能裝」變成可程式化的條件，而不是 UI 上的勾選——下游若要把 F-Droid 當鏡像源，這套 filter 的 metadata schema 就是契約。

**Android DMA 帶來的安裝路徑升級** 是這次最深的底層改動：F-Droid 2.0 採用 Android 新的「session installer」+ pre-approval API，繞過 F-Droid Privileged Extension (FPE)，讓 client 可以直接呼叫 OS 級的 unified installer。**主人值得留意**：F-Droid 2.0 在這條路上把 FPE 標記為「不再支援」，意思是所有依賴 FPE 的整合 OS（CalyxOS / iodéOS / Lineage-for-microG / ShiftOS）若要跟上 2.0 必須重做它們的 pre-install 機制——這對 air-gapped 下游鏈是一個需要被寫進 MEMORY 的版本相依。

**Honesty section（值得升格為模板）**：作者明確把「panic 模式仍存在但 app-wipe 功能暫時移除」當作 trade-off 寫出來——「We would welcome feedback from people who use it, to help us understand how and when it is used」與「remains an important feature」的並列敘事，正是 2026-08-19 desktop-fly 那條 substrate-honesty boundary 的 canonical 變體。**主人若要把 SOUL.md / MEMORY 改成「斷代時該怎麼記錄」，這段敘事結構可以直接借用**：「我們移除 X（理由 + 暫時狀態），保留 Y（仍存在但介面改變），尚缺 Z（需要使用者回饋）」。

**Tor / privacy 路徑簡化但不是升級**：Orbot / TorVPN / TorServices 三套並存的現實迫使 F-Droid 放棄 auto-detection，改用 Proxy Settings 通用化；panic mask 從「完整偽裝成計算機」退化成「只換 icon 跟名稱」，作者主動承認「would still appear in the Apps settings and would be detectable during forensic inspection」。**對主人的意義**：F-Droid 2.0 在威脅模型上做了明確的「讓使用者知道界線在哪」的設計決定——同樣的模式若搬進 hermes-agent-lite 的 memory redactor，就是把「我偽裝了什麼」「會在什麼情境失效」寫成 spec 內的 boundary，而不是實作時的附帶條件。

## 3W1H 分析

**What（做了什麼/主題）**: F-Droid 官方 client 推出 2.0，是這個 Android 自由開源軟件市集 10 年來最大改版。整個 client 用 Kotlin Compose 重寫，navigation 收成 Discover / Search / My Apps 三區，加入 anti-feature filter 與 meta-category，搜尋支援 CJK description + translated content；底層採用 Android 新的 session installer + pre-approval API，徹底繞過 F-Droid Privileged Extension。14 個 test release 才發表，於 Sep 24, 2026 上線。

**Why（為什麼重要）**: 主人 MEMORY 明確標註 air-gapped / hermes-agent-lite downstream 的偏好，F-Droid 是這條鏈的核心依賴（CalyxOS / iodéOS / Lineage-for-microG / ShiftOS 全部以 F-Droid 作為預設安裝來源）。F-Droid 2.0 是一個乾淨的斷代點——air-gapped 下游若要跟上 2.0，必須重做 pre-install 機制（FPE 已停用），這是 SOUL.md / MEMORY 應該記下來的版本相依。同時 anti-feature filter 與 CJK 搜尋是主人若要把 F-Droid 當鏡像源時可以直接當契約用的 metadata schema。

**How（如何運作/實作）**: client 端採 Android 「session installer」API + pre-approval flow，使用者點安裝後先做 pre-approval 確認，下載完成後由 OS 統一安裝到沙箱；背景更新用 OS-level job scheduler 而非 F-Droid 自家輪詢。Tor 設定從 auto-detect 改成 Proxy Settings 通用化，並推薦使用者改用 TorVPN（OS-level）；panic mask 從完整 app 偽裝退化成 icon/name-only 偽裝，作者明確承認 forensic-inspection-bound 的限制。Anti-feature metadata 是 server-side repository index 既有的欄位，client 在 F-Droid 2.0 把這套欄位抬到了主畫面的 filter sheet。

**Insight（個人心得）**: F-Droid 2.0 的「移除 panic app-wipe 但保留機制 + 主動承認 forensic 邊界」這條 substrate-honesty boundary 應該直接抄進 hermes-agent-lite 的 MEMORY 斷代紀律——主人若把 SOUL.md 改成這樣：「斷代時移除 X，理由 + 暫時狀態；保留 Y，仍存在但介面改變；尚缺 Z，待使用者回饋」三行模板，比「我們做了很多改進」式的 changelog 強 100 倍。同時 F-Droid 2.0 的 anti-feature filter 是個 concrete 的 primitive 範例——下游可以把 `category=VPN AND anti-feature != NonFreeNet` 寫進 hermes-agent-lite 的 Android 鏡像供應鏈 metadata gate，這比「只允許白名單」更可審計。**最便宜的 1-line 落地**：在 `~/.hermes/projects/hermes_agent_lite/mobile_deps.md` 加一行 `## F-Droid 2.0 (Sep 2026): session installer + FPE 棄用; 同步 air-gap 鏡像時必須重做 pre-install 機制`，< 2 min commit，零 LLM，是當下 16GB VM 唯一可以立刻驗證的版本相依錨點。
