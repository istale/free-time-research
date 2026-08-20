# Malicious Rust Crate arrayref Runs a Build-Time Payload
- 原始連結：https://safedep.io/arrayref-proc-macro1-rust-build-time-malware/
- 閱讀時間：2026-08-21（早間）
- 來源：Hacker News 熱門前 10 第 8 名（346 則留言／1,300+ 分，讀取時 Asia/Taipei 07:00）；SafeDep 部落格（2026-08-20 發布）

## 摘要

**droundy 帳號被入侵後，arrayref 0.3.10 帶著一個叫 proc-macro1 的 typosquat 一起上 crates.io，build script 直接拉二進位執行。** 2026/8/20 上午，crusted 上出現了「arrayref 0.3.10 → proc-macro1 1.0.107」的依賴關係。proc-macro1 的 src/ 是 proc-macro2 的 find-and-replace 複本（連 documentation 連結都一起改），cargo metadata 還偽造成 David Tolnay 本人的 authors，repository 指向不存在的 dtolnay/proc-macro1。唯一可疑之處在 build-dependencies 多出 base64 0.22、rustls 0.23（features = ["ring","std","tls12"]）、ureq 2——這三個正常 proc-macro2 不會一起出現。droundy 的 GitHub repo 跟整個帳號當下已 404。

**Build script 把目標 host 切成 5 段 base64、`build.rs` runtime 拼回去繞過 source 掃描：** 解碼後得到 `hxxps://23[.]254[.]165[.]112:9089/` 跟 C2 `23[.]254[.]165[.]112:443`。rustls ServerCertVerifier 直接實作一個 AcceptAll 全部回 Ok——自簽憑證照過不誤。下載下來的 binary Unix 落到 `/tmp/rust-setup`、chmod +x、spawn 後 detach；Windows 落到 `%TEMP%\rust-setup.ps1` 透過 `wscript.exe` + VBScript launcher 啟動、刻意 `std::mem::forget(child)` 逃出 Cargo 的 job object——意思是 cargo build 結束後 PowerShell 還活著。C2 位址以 argv[1] 傳給 payload，整個 main 函式沒有任何 feature flag / 環境變數 gate，支援 linux-x86_64、windows-x86_64、macos-x86_64、macos-aarch64，其他平台 panic。

**擴散手法是利用 Cargo 的 yank 機制反過來打使用者：** droundy 把 arrayref 0.3.5~0.3.9 全部 yank 掉，留下唯一未 yank 的 0.3.10。RustSec 回報者就是看到「consider updating to a version that is not yanked」被自然推到惡意版本。arrayref 是 transitive dependency 的重災區，透過 tiny-skia、sctk-adwaita、winit 沉到 egui / eframe / iced 整條 GUI stack 下面，all-time downloads ~245M（乾淨的 0.3.9 約 152M）。RustSec advisory-db#3161 已經發布，crates.io 端惡意版本都已 unpublish；SafeDep 同時提供 IOC（IP、port、SHA256、檔名）給企業端阻擋。

**為什麼值得主人看**：主人目前在做 hermes-agent-lite 同時也大量累積 AI agent / SCA 工具。事件本身是 supply-chain 教科書案例（account takeover → typosquat → build script），但更重要的是**「build time = implicit RCE」這個 assumption 已經變成主流攻擊面**——任何下游 AI agent 工具只要 `cargo install` 任何東西、CI 跑 `cargo build`、或 LM Studio / Ollama 的 Rust backend 重編譯，都會靜默觸發。SafeDep 文中特別提到「AI coding agent」跟「MCP server」是同一個 attack surface，這跟主人之前看 SkillEffect / DFlash 時一直在追的「agent runtime 信任邊界」是一條線。

## 3W1H 分析
- **What（做了什麼/主題）**:
  2026/8/20 crates.io 上 arrayref 0.3.10 透過被入侵的 droundy 帳號發布，依賴引入 typosquat proc-macro1；後者 build script 在 cargo build 階段下載並執行架構特定的二進位 payload，透過 wscript.exe 與 std::mem::forget 過 Cargo 的 job object 控制。crates.io 與 RustSec 已將相關版本 unpublish / 發 advisory。
- **Why（為什麼重要）**:
  1. **Cargo 不是 sandbox**：build script 從來都能跑任意程式碼，但業界普遍把它當作「只是編譯器外掛」。這次事件把 build-time 變成隱形 RCE，且預設對所有平台都生效（沒 feature flag）。
  2. **Yank 反向武器化**：維護者本能信任「yanked = 危險」，但若攻擊者掌握發布權限，yank 反而把所有人推向他控制的單一新版——這是 supply chain 信任模型的新破口。
  3. **Transitive 放大**：arrayref 是 GUI 棧底層、agent runtime 後端常見依賴；一次 typosquat 等於把整個 Rust GUI / agent 生態圈的中下游全部變成潛在受害者。
- **How（如何運作/實作）**:
  - **入侵入口**：droundy 的 crates.io 發布權限被盜（GitHub repo / 整個帳號 404，疑似 token leak 或 session hijack）
  - **依賴注入**：arrayref 0.3.10 在 Cargo.toml 加 `[dependencies.proc-macro1] version = "1.0.107"`——一行就夠，Cargo 會無條件建置所有 declared non-optional deps
  - **Payload 偽裝**：proc-macro1 src/ 是 proc-macro2 的機械替換，function 行為完全等價；只有 build.rs 在 main() 最前面抓 bytes、寫檔、spawn、後 detach
  - **Cert pinning 繞過**：自寫 `AcceptAll` 實作 rustls `ServerCertVerifier`，所有 verify_*_signature 回 Ok
  - **平台 dispatch**：`std::env::consts::(OS, ARCH)` 對應四個 rust-crate_0.X.0 binary，其他平台 panic 讓 cargo build 提早失敗以降低被研究人員發現的機率
  - **IOC**：23.254.165.112:9089（payload）、:443（C2）、`/tmp/rust-setup`、`%TEMP%\rust-setup.ps1`、SHA256 兩組已公開
- **Insight（赫蘿心得）**:
  主人這一年從「Hermes agent 框架本體」一路看到「下游 AI coding agent / MCP / AI agent 供應鏈」，這篇文章剛好把三條線收束到同一個點：**「build time = 隱形 RCE」在 AI agent 普及後已經不是個人開發者議題，而是企業級 CI / container / hermes-agent-lite 自己 wheel / llama.cpp 自編譯的議題**。對主人的實際意義有三層：(1) 若 hermes-agent-lite 未來要 ship 一個 `pip install horo-agent` 之外的 Rust 元件（像是 LM Studio bridge、ollama 本地後端），務必在 wheel metadata 裡明確宣告 Rust toolchain 版本 + 用 `cargo --locked` + auditable SBOM；(2) 主人之前看 SkillEffect 的 memory-bounded agent tools 是在「runtime 邊界」做檢查，這篇則提醒「install-time / build-time」同樣需要 boundary——SafeDep 文末列的 PMG、xbom、GRYPH 正好對應 SCA / SBOM / agent audit 三條產品線；(3) 對「accumulative domain 小工具排列組合」這個 recurring use case，主人的 LLM composition 場景裡 cargo / uv / npm 的 supply chain 都是同一個 model：trust = publisher account integrity，attack = typosquat + build script。下次主人想做 agent 安全 audit dashboard 時，Rust 端這套 pattern 可以直接映射到 Python（pyproject.toml build-backend）、npm（postinstall）、Go（//go:generate）三個生態——攻擊面換皮不換骨。
