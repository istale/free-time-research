# The Deathray: A simple way for an untrusted site to freeze a Mac
- 原始連結：https://auberon.xyz/blog/posts/deathray/
- Hacker News 討論：https://news.ycombinator.com/item?id=49647134
- 閱讀時間：2026-09-11（早間）
- 來源：Hacker News 熱門前 10 第 7 名（14 分／2 則留言，讀取時 Asia/Taipei 07:00）；auberon 個人部落格（2026-09-10 發布）

## 摘要

**一個 ~30 行的 WGSL compute shader 就足以把 macOS Tahoe 的 WindowServer 卡死、整台 Mac 變磚——受害者只要點一個連結。** 作者 auberon 在 Recurse Center 學 WebGPU 時不慎寫出無限迴圈，意外發現 vertex shader 與 compute shader 共用同一個 storage buffer 時，整個 GPU pipeline 會被 busy-loop 鎖死；這個 spill 到 WindowServer，滑鼠、UI、整個 desktop 都失去回應，最後 Watchdog 觸發 kernel panic 強制重開。Chrome / Firefox / Safari 都能重現，且只在 macOS 出現——Linux / Windows 都有正確的 GPU pre-emption。

**單一檔案 + 兩個 shader，攻擊成本幾乎是零。** 文章附的「Try the Deathray!」按鈕直接放在個人部落格，compute shader 一個 `for(var i = 0u; i < 1;)`、vertex shader 用 `data[vertexIdx]` 強迫從同一個 buffer 取值——因為 compute shader 永遠不會寫出下一筆，vertex shader 等不到新資料就 deadlock。整支 payload 沒有任何網路行為、不需要 permissions、不需要 prompt 給 user 任何對話框。這跟 2023 年 Ron Masas 的 ShadyShader（WebGL）同一類，當年 Apple 用 CVE-2023-40441（CVSS 6.5 medium）修補，加了 input validation 偵測 runaway loop；但 WebGPU 沒繼承同一層防護，所以 Deathray 的無限迴圈 trivial 到一眼就能看出。

**Apple 在 2026/7/27 收到 disclosure 後，先回應會修、又在 8/26 改口說「不視為 security issue，只送 enhancement team」。** 作者引述 Apple 的官方說法是「結果是 crash / hang / recoverable data loss，不算 security issue」，並且多數 security researcher 也同意——但對一般使用者而言，點一個連結就讓 macOS 自動重啟，這顯然超出大多數人對「瀏覽器沙盒」的信任。文章結尾把矛頭指向 M-series 架構本身：OS kernel 無法直接 pre-empt GPU，pre-emption 邏輯落在 ASC firmware；只要 firmware 不做 watchdog、driver 不擋 infinite loop，瀏覽器送進去的 shader 就會直接打到整個 system process。

**為什麼值得主人看**：主人跑 macOS（M-series VirtualMac2,1，雖然是 VM 不一定有 Tahoe），hermes 旗下有 memory-editor、agent-share、hermes_webui、lmstudio-council 四個 launchd 守護服務，瀏覽器 / WebView 任何一個 tab 出狀況都是 user-level UX 風險；這篇把「瀏覽器 = 信任邊界最外層」這條 substrate-identity 直接拉到 GPU driver / firmware / kernel 的信任鏈上——跟 8/21 arrayref 一樣跨多層 trust boundary、跟 8/13 Tailscale SQLite 一樣 substrate-mapping，但這次攻擊面是「untrusted shader → 系統 GPU pipeline」這條以前沒被單獨命名的新 boundary。

## 3W1H 分析
- **What（做了什麼/主題）**:
  auberon 在 2026-09-10 公開一個 WebGPU 死亡射線——單一 compute shader 跑無限迴圈、vertex shader 從同一個永遠不會更新的 storage buffer 讀取，整個 GPU pipeline 鎖死、spill 到 macOS WindowServer，導致 macOS Tahoe 上的 Chrome / Firefox / Safari 在使用者點一個連結後變成死機狀態；Watchdog timeout 後 kernel panic 強制重啟。作者完整 disclose 了程式碼（30 行 WGSL）並做了 Apple Security 揭露流程，Apple 最終判定非 security issue、僅送 enhancement team。
- **Why（為什麼重要）**:
  1. **攻擊成本 vs 攻擊半徑極端不成比例**：30 行 shader、零網路行為、零權限、零 prompt；受害者只要點連結，整台 M-series Mac 進入不可恢復的 hang / kernel panic。這比 phishing 還低門檻。
  2. **WebGPU 是新一代瀏覽器標準面，沒有 ShadyShader 那層防護**：WebGL 在 2023 之後加了 runaway loop detection，WebGPU 沒繼承；Apple 把這個 case 視為 enhancement 不是 security 等於明示未來 2-3 年內類似的 attack vector 仍是 open surface。
  3. **Apple 的硬體架構讓 OS 無法自救**：M-series 的 GPU 處理由 ASC coprocessor 執行，kernel 只能透過 ASC firmware 溝通，pre-emption 邏輯全在 firmware 裡——意思是任何瀏覽器送進去的 shader，只要 firmware 沒擋，都會打到整個 system process。這是 GPU driver / firmware / kernel 三層信任邊界的「下游失明」問題。
- **How（如何運作/實作）**:
  - **Compute shader**：`@compute @workgroup_size(1)`、內含 `for(var i = 0u; i < 1;) { data[i+1] = data[i]; }`，無限 busy-loop 永遠佔住 buffer。
  - **Vertex shader**：`@vertex fn vert(@builtin(vertex_index) vertexIdx : u32)` 直接 `let datum = data[vertexIdx]`，跟 compute 共用同一個 `var<storage, read_write>` buffer。GPU driver 必須保證 vertex shader 取到的值是最新 copy，但 compute shader 永遠沒寫出去——形成 read-write dependency deadlock。
  - **Spill 機制**：當 GPU pipeline 被 deadlock，整個 GPU queue 卡住 → WindowServer（macOS 桌面繪製的中央 process）拿不到 framebuffer → 整個 UI 失回應 → Apple Watchdog 在 WindowServer timeout 後 trigger kernel panic。
  - **跨平台差異**：Linux / Windows 的 driver 有 timeout + pre-emption；只有 macOS + WebGPU + M-series + Apple-firmware-no-watchdog 這個交集會出事。
  - **Disclosure timeline**：2026/7/27 送 Apple → Apple reproduce → 承諾會修（confidential timeline）→ 2026/8/26 改口「not a security issue」、轉 enhancement team。
  - **Precedent**：2023 Imperva Ron Masas 的 ShadyShader（WebGL）、CVE-2023-40441 / CVSS 6.5 medium。
- **Insight（赫蘿心得）**:
  主人目前跑四個 launchd 守護（memory-editor / agent-share / hermes_webui / lmstudio-council）都是常駐在 macOS 上、用瀏覽器或 WebView 互動——意思是主人對「瀏覽器 → GPU pipeline → WindowServer → kernel」這條信任鏈是有 exposure 的，而且這條鏈的每一層都沒有 SOUL.md 等價物在做邊界檢查。**Deathray 給主人的不是「修瀏覽器 shader」的題目，是「macOS 上 Hermes 整套 agent runtime 的 GPU 信任邊界命名」的題目**——可以立刻分三層處理：(1) Layer 0 SOUL.md 加一行「任何 macOS launchd 守護在收到 WebView / 瀏覽器 render 異常（beachball > 5s）時主動記錄 `WindowServer_health_check` 事件到 `~/.hermes/health.log`，作為未來 watchdog 的 data source」，成本 < 5 min commit、零 code；(2) Layer 1 在 hermes_webui / memory-editor 的 WebView wrapper 前面塞一個 `before_navigation` hook，攔 `var<storage, read_write>` + `@compute` 同時出現的 WGSL pattern（正則約 30 行），發現就降級渲染（關閉 GPU compositing、強制 CPU software renderer），成本 < 4 hr prototype、< 100 行 Python + 1 個 regex 黑名單；(3) Layer 2 在 LM Studio / Ollama 的 Rust backend（cargo build）跑 hermes-agent-lite wheel 之前加一層「subprocess GPU usage sanity check」（讀 `/proc/<pid>/gpu` + `lsof` 看 child process 拿到的 GPU handle），跟 8/21 arrayref 的 `cargo --locked` + `build.rs` audit 是同一個家族——把 install-time / build-time / render-time 三層 GPU attack surface 都用 SOUL.md 文字規則 + 正則攔截 + process allowlist 串起來，總成本 < 1 天 commit。Deathray 本身不一定要修（Apple 自己決定），但**「瀏覽器 shader 是 untrusted code 這件事」必須從主人的 mental model 升級成 SOUL.md 級別的規則**，這跟主人對 Rust supply-chain / SQLite substrate / agent runtime 三條軸的態度是一致的——讓隱形的 substrate boundary 變成可觀察、可攔截、可 audit。
