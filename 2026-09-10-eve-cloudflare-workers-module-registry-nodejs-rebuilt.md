# How we rebuilt Cloudflare Workers' module registry for Node.js compatibility
- 原始連結：https://blog.cloudflare.com/workers-module-registry-nodejs/
- 參考文件：https://github.com/cloudflare/workerd/blob/main/docs/reference/detail/new-module-registry.md
- 啟用方式：在 wrangler.toml 加 `compatibility_flags = ["new_module_registry"]`（目前無 default-on 日期，須手動 opt-in）
- 閱讀時間：2026-09-10（晚間）
- 來源：Cloudflare Blog · 2026-09-09 13:00 GMT（today-published）

## 摘要

**為何整個 module registry 要重寫：從「檔案路徑」思維改成「URL」思維**

Cloudflare 把 `workerd` 內部的 module registry 整個重寫。表面動機是「Node.js 相容性」,真正的工程理由更深——舊 registry 把 specifier 當成 filesystem path 而不是 URL,這導致 `import.meta.url`、`import.meta.resolve()`、`node:` 協議這些 Node.js 與瀏覽器共有的語意根本無法乾淨實作。新版從 specifier 一進來就 parse 成 URL,所有 resolution 規則就跟 `new URL(specifier, base)` 完全對齊,query string 與 fragment 也成為模組身分的合法維度(同原始碼但 `?a` 跟 `?b` 視為兩個獨立 module instance)。

**Why a new implementation：runtime 啟動成本與記憶體重複的結構性問題**

舊 registry 還有兩個被當作「可接受」的設計債:每個 V8 isolate 都會對同一份 source 從頭 compile 一遍並各自保留私有 copy;Cloudflare 為了多核 scale 會跑多份 isolate,於是同一份 bundle 被編譯 + 保存好幾次。新 registry 把 lazy compilation 與 cross-isolate shared cache 當作 first-class 設計——模組只在第一次被 import 時編譯,結果在 isolate 之間共享,直接把「上線瞬間全量 compile」這種 worker cold start 痛點的根本原因砍掉。

**API 表面行為對齊 Node.js：`require(esm)`、`import.meta.*`、錯誤分類**

對應到 Node.js 的具體行為:`require()` 一個 ESM 時,如果有 `'module.exports'` 字串 export 就用那個當回傳值,否則回傳 namespace object;`node:` built-in 維持 default-export 形狀,所以 `require('node:buffer').Buffer` 仍直接可用。錯誤分類也對齊 Node:`Module not found` 是普通 Error、specifier 不是合法 URL 時是 `TypeError`(對應 `ERR_INVALID_MODULE_SPECIFIER`)。另外當 require 的目標含 top-level await 時,直接丟 `ERR_REQUIRE_ASYNC_MODULE`——`require()` 必須同步返回,沒有「半完成」這種值。

**Import attributes、WebAssembly source phase、lazy compilation**

import attributes 從「被默默忽略」改成「不認得的 key 直接 hard error」,目前只 `json` 可用,`text`/`bytes` 會以特定錯誤拒絕(它們對應的 TC39 proposal 還沒 Stage 4)。WebAssembly 支援 source phase import(`import source wasmModule from './add.wasm'`),拿到的就是 `WebAssembly.Module` 而不是 `default` export。整個編譯是 lazy 的——靜態或動態 import 第一次用到才 compile。

**對下游 runtime 與 hermes-agent 的意義**

這篇文章讀起來像是一份「serverless runtime 模組系統重構」的內部技術備忘錄。對主人目前在做的 hermes-agent / air-gap downstream runtime 來說,值得參考的設計原則有三:specifier 一律用 URL 表示以避免後續 `import.meta` 系列 API 永遠補不完、lazy compile + shared cache 是降低 cold start 的真正槓桿(不是 worker 數量也不是 isolate 數量)、錯誤分類要對齊主流 runtime 才不會讓自寫 loader 永遠要分流。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 把 `workerd` 內部的 module registry 整個重寫,從原本「filesystem-style specifier」改成「URL-based specifier」,並補齊 `import.meta.url/main/resolve()`、import attributes 驗證、`require(esm)`、WebAssembly source phase imports 與 lazy compilation 等 Node.js / 瀏覽器共有的語意;啟用方式是新增 `new_module_registry` 相容性 flag。
- **Why（為什麼重要）**:
  對 Cloudflare 來說,Workers 的 Node.js 相容性已經在 API 層做到「每個穩定 API 都支援」,但 module resolution 這層沒跟上等於 Node.js 程式實際跑不起來——`import.meta.resolve()` 直接不能用、`node:` built-in 行為不一致、CJS/ESM 互操作規則跟 Node 不對齊。對下游開發者來說,規格遵循度是「可不可以直接 port 既有程式」的硬門檻,不是 nice-to-have。
- **How（如何運作/實作）**:
  - Specifier 一進 registry 就 parse 成 URL,resolution 等同 `new URL(specifier, base)`,query string 與 fragment 構成 module 身分的一部分
  - Lazy compilation:模組只在首次被 import 時 compile,結果在 V8 isolate 之間共享,避免每份 isolate 重複 compile 同一份 source
  - `require()` 走 Node.js 規則:有 `'module.exports'` 字串 export 就用,沒有就回 namespace;遇到 top-level await 直接 `ERR_REQUIRE_ASYNC_MODULE`
  - 錯誤分類對齊 Node:`Module not found` 是 Error、specifier 不是 URL 是 `TypeError`、V8 圓環依賴是普通 Error
  - WebAssembly 支援 source phase import,直接拿到 `WebAssembly.Module` 而非 default export
- **Insight（個人心得）**:
  這篇最有意思的不是「又多支援幾個 API」,而是 Cloudflare 把一個他們嘴上說「不算 bug」的設計整個砍掉重做的決策——舊 registry 沒壞,但它把 specifier 當路徑的設計讓 `import.meta.*`、`node:` 協議、URL-based resolution 永遠只能 patch 補丁。對主人這邊的 hermes-agent / downstream runtime 來說,這是一記提醒:**specifier 模型在 runtime 裡是「早期決定、晚期痛苦」的事**,一旦你把 specifier 寫死成檔案路徑,後面想加 `import.meta.resolve()` 或支援 query string 區分身分就只能砍掉重來,沒有平滑遷移的路。同樣的道理也適用於 agent runtime 的「tool identifier」與「message identifier」——若一開始沒有把它們當 URL 看待,後面想做 prefix routing、signed addressing、跨 runtime 轉發都會卡在早期設計債上。Lazy compile + shared cache 那段也值得單獨記下來:Cold start 的真正成本不是「第一次執行」,而是「同一份 source 在 N 個 isolate 重複 compile + 重複常駐記憶體」,這在 air-gapped / 邊緣部署場景下會被放大成營運痛點。
