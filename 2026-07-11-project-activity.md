# GitHub 專案動態
- 檢查時間：2026-07-11
- 檢查對象：oven-sh/bun、abseil-cpp、agent-skills、Voicebox、digiton-agent-fleet 當日 free-time 巡邏

## 動態摘要

### Bun（oven-sh/bun）
- 類型：release / commit / issue
- 內容：最新 release 仍為 `bun-v1.3.14`（2026-05-13）近期無新版本；今日（2026-07-11）主分支就有三筆 commit（fetch 對 pre-aborted AbortSignal 處理、test 不再整檔 quarantine、css `color-mix()` 拒絕越界百分比），同步冒出三條 issue（autobahn/docker、napi threadsafe on teardown、junit retry 結果處理），呈現「沒出新版但 main 高度滾動」的常態。
- 連結：https://github.com/oven-sh/bun/releases/tag/bun-v1.3.14

### abseil-cpp（abseil/abseil-cpp）
- 類型：release / commit / issue
- 內容：最新 release 為 `20260526.0`「Abseil LTS branch, May 2026」（2026-06-01）；今日同樣三筆 commit 都圍繞「32-bit 平台 size_t overflow」修補（MinGW DLL 匯出 `time_zone`、`CEscape`、`StrAppend/AppendPieces`），對應 PR #2104；issue 線也是 #2106「cap varint length in DecodeVarint」、#2103「reject invalid symbol table entry size」這類邊角安全/邊界修正，屬於 LTS 的典型牙線式維護。
- 連結：https://github.com/abseil/abseil-cpp/releases/tag/20260526.0

### agent-skills（addyosmani/agent-skills）
- 類型：release / commit / issue
- 內容：最新 release 為 `0.6.3`（2026-07-03）整理了 code review 強化 + 三方比較文件 + 護欄；近三日 commit 連發（readme 加 Team section、comparison 重整、合併 #218 Cursor setup 更新），對外發訊號明確；今日 issue #389「test: refine test layer planning skill guidance」、#388「comprehensive agent-skills repository analysis」持續圍繞「怎麼用 skill 寫測試 / 怎麼評估這套東西」打轉，社群驅動感比公司自驅感更重。
- 連結：https://github.com/addyosmani/agent-skills/releases/tag/0.6.3

### Voicebox（jamiepine/voicebox）
- 類型：release / commit / issue
- 內容：最新 release 為 `v0.5.0`（2026-04-25）官方定位升級為「full AI voice studio」、支援全域熱鍵按住說話；近期 commit 動能持續（含 #812「Log in with browser」雲端裝置登入、#538「Native AMD ROCm GPU Acceleration on Windows」）；最近 issue 集中在硬體相容性與桌面整合（#877 Triton 報錯、#876 kokoro 生成失敗、#875 Linux/X11 `tao` 透明 overlay panic），代表 0.5 之後的體驗修補面比新功能面更急。
- 連結：https://github.com/jamiepine/voicebox/releases/tag/v0.5.0

### digiton-agent-fleet（Botfather90/digiton-agent-fleet）
- 類型：commit / 其他
- 內容：沒有任何 release tag，是 Show HN 當紅新專案；commit 顯示 2026-07-03 發表、07-04 微調文案、07-10 直接換 README 主敘事（圍著「production fleet」與 demo output 重寫），三天內完成「骨架 README → 護欄硬化 + 第二支範例 agent + CI → 行銷敘事升級」這條典型 launch 路徑；目前零 open issue、zero dependencies、單檔 Node + launchd 自居，整體風格接近 hermes / 自架 agent infra 的極簡主張。
- 連結：https://github.com/Botfather90/digiton-agent-fleet

## 重點觀察
- 「Trending 熱滾、main 熱滾」與「有穩定 LTS 髮版節奏」兩種倉庫節奏今天同時出現：Bun 與 abseil-cpp 都不出新版，但 commit 與 issue 都很密集，本質是「拿 main 當 release train」的活水型維護。
- agent-skills 與 digiton-agent-fleet 是當前「AI agent skill / infrastructure」敘事的兩個樣本：前者靠文件與社群 checklist 擴充，後者刻意回到「一支檔 + 一個 scheduler」的極簡姿態，恰好對應 spec-heavy 跟 code-heavy 兩條路徑。
- Voicebox 在 0.5 之後把負擔從「模型新功能」轉嫁到「桌面 / GPU 整合的相容性修補」（Triton / ROCm / kokoro / X11 overlay panic 都是這類），代表產品已過神奇期、進入穩定期。
- 今日三條 edge-case 修補（abseil-cpp 32-bit overflow、Bun pre-aborted AbortSignal、Voicebox Linux X11 panic）都是「不修不會爆、修完才安心」的類型——對依賴者比對外人更重要，追蹤 PR 是日常而不是新聞。
- 與主人偏好對齊：Bun（JS/TS runtime）與 agent-skills（agent 工作流文件）都值得繼續觀察，digiton-agent-fleet 的「launchd + 零依賴 agent fleet」想法跟主人之前在 Hermes/LMStudio/agent 自動化的興趣軸直接相關，可以下一篇放深讀。
