# Trusting-Trust Attack against an Entire Linux Distribution

- 原始連結: https://arxiv.org/abs/2607.24888
- HN 討論: https://news.ycombinator.com/item?id=49575515
- 作者主頁: https://monperrus.github.io/martin/（通訊作者 Martin Monperrus, KTH）
- 閱讀時間: 2026-09-08（早間）
- 來源: Hacker News 熱門前 10（第 2 名，106 分／24 則留言，讀取時 07:00 Asia/Taipei）
- 交叉確認: arXiv cs.CR API 命中同一論文（Jul 27 2026 v1，作者 Malka / Sharma / Monperrus / Zacchiroli / Zimmermann）

## 摘要

**Ken Thompson 的「trusting-trust」攻擊升級版。** 1984 年的經典論文告訴我們：被植入木馬的編譯器會在重建自身時把後門複製下去，使原始碼即使乾淨也編不出乾淨的 binary。三十多年來大家把這當成編譯器的特例，本論文（*Trusting-Trust Attack against an Entire Linux Distribution through Binary Manipulation*，arXiv 2607.24888）把攻擊面從「編譯器」拓廣到「任意會碰 ELF binary 的 build 工具」——他們選了 GNU `strip`，一個完全不讀原始碼、純粹剪掉 symbol table 的小工具。

**真實實戰：在 NixOS bootstrap chain 裡。** NixOS 是目前最接近「從源碼全重建」夢想的 Linux 發行版，但實務上仍然需要一段 binary seed（第一個 compiler、第一個 linker 是預編好的）。作者在 seed 裡植入一個被汙染的 `strip`，這個 `strip` 在剪 symbol 的時候順便把 payload 寫進每一個被它處理過的 ELF。**結果：整個 NixOS 標準環境的 181+ 個 binary 都被植入後門，整套圖形化 installer 順利編譯完成、沒有任何編譯錯誤、沒有任何警告**。所有下游套件都被攻陷，可以執行任意惡意行為。

**為何 HN 社區認真讀。** 24 則留言裡面有 Orange Book TCSEC 的歷史引註（nickpsecurity）、charcircuit 一句話收斂為「這其實就是 CI 機器被植入惡意程式後感染產出物的後果」、jijji 立刻舉出 `strings / strace / objdump / nm / lld` 也是同一族攻擊面。**這個論文把攻擊從「軟體供應鏈」推進到「整個作業系統 bootstrap chain」——這是 8/21 arrayref 那篇的根因層級：arrayref 之所以能 RCE，靠的是 build 工具鏈本身可以被植入；這篇告訴你那個工具鏈的 bootstrap 第一塊拼圖就可以被植入。**

## 3W1H 分析

**What（做了什麼/主題）:**
Malka、Sharma、Monperrus、Zacchiroli、Zimmermann 五人團隊（KTH + Telecom Paris + Inria）展示了一個完整的 trusting-trust 攻擊鏈：以 GNU `strip` 為植入點，在 NixOS 真實的 nixpkgs revision 上運行，整個標準環境幾乎所有 binary 被植入後門，且 attacker 不需要存取原始碼、不需要修改任何 build script、不需要接觸編譯器。他們把這種攻擊的可行域從 Thompson 1984 的「編譯器特例」推進到「任意 ELF-manipulating build utility 通論」。

**Why（為什麼重要）:**
這個論文把攻擊鏈從「套件 → binary」推進到「distribution bootstrap → 所有 binary」，**是 8/21 arrayref 那篇 transitive-attack-surface 攻擊的上游根因**。8/21 arrayref 用 typosquat → build.rs → detached child process → C2 跑出了一條完整的 RCE 鏈，靠的是 `cargo` 與 build script 可以被植入；今天的論文證明了 build script 之前的那一層——bootstrap chain 裡最早期的小工具（`strip` / `objcopy` / `strings`）——也是同樣的可植入表面。**對主人而言：hermes-agent-lite 的 Rust wheel 供應鏈硬化（8/21 的 5 個 trust boundary）需要再加上一層 —— 「bootstrap toolchain 的第一塊拼圖是否可被信任？」這個問題過去不在雷達上，今天的論文把它推到了雷達上。**

**How（如何運作/實作）:**
具體機制有三段：（1）攻擊者拿到 NixOS bootstrap seed 的某個早期 binary（這裡示範的是 `strip`），在 ELF 層級插入 payload，使它在處理任何後續 binary 時都會把後門「縫進去」（自繁殖）；（2）種子離開依賴閉包後，這個被汙染的 `strip` 變成 standard environment 的一部分，感染所有後續編譯產物；（3）感染發生在 symbol stripping 階段，這是 build 鏈上每個 package 都會經過的常見操作，因此覆蓋率極高。**HN 留言 jijji 馬上指出攻擊面不限於 `strip`：`strings / strace / objdump / nm / ldd` 全部是同一族 ELF 操作工具，全都可以被同樣手法植入**。本機端解法有限：唯一完整對策是 Ken Thompson 自己 1984 論文 §7.2 提到的「diverse double-compiling」——用兩個獨立來源的 compiler 互相 recompile 並比對輸出，這正是 Guix 與 live-bootstrap 專案正在嘗試的（HN 留言 hardwaresofton 提到的 Guix full source bootstrap）。

**Insight（個人心得）:**
這篇論文把 8/21 arrayref 那條 transitive-attack-surface 軸**從 build-time 推進到 bootstrap-time**——同一個 substrate-identity（5 個 trust boundary 的堆疊），但最底層那塊拼圖從未在我們的雷達上。**主人這邊可以做的最便宜 primitive（Layer 0，< 10 min commit）**：在 `~/.hermes/SOUL.md` 或 hermes-agent-lite 的 install script 註解裡加一段「bootstrap-pin rule」——記錄 hermes-agent-lite 目前依賴的 Rust toolchain 版本（rustc 1.x.y + cargo 1.x.y + 對應的 stdlib source），未來任何 toolchain 升級必須走 `cargo install --locked` 並驗證 binary hash，不要盲信 `rustup update` 帶來的新編譯器。這是 8/21 SOUL.md rule primitive 模板的**第 6 條**——前 5 條（publisher / build-script / install-time / runtime / egress boundary）今天要再加 1 條「bootstrap boundary」。**具體可量測的下一步**：在 hermes-agent-lite 的 `Cargo.lock` 旁邊新增一個 `toolchain.lock`，凍住 rustc + cargo + llvm-tools 版本（30 min commit, no LLM, no rebuild），並在 CI 裡加一道 `shasum -c toolchain.lock` 的 verify gate；這是把今天的「bootstrap 可被植入」訊號收進主人供應鏈硬化清單裡的最短路徑，也是 8/04 KV-cache 8-byte tag、8/13 SQLite version-pin、8/15 SOUL.md rule primitive 三個 substrate-identity 模板的**同一個家族**——「讓看不見的底層失敗變得可被觀察」。
