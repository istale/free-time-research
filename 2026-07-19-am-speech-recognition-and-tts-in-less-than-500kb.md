# Speech Recognition and TTS in less than 500kb
- 原始連結：https://github.com/moonshine-ai/moonshine/tree/main/micro
- 閱讀時間：2026-07-19

## 摘要

**不到 500 KB 指的是執行期 RAM，而非整套模型大小**：Moonshine Micro 把語音活動偵測（VAD）、指令辨識（STT）與神經語音合成（TTS）完整放進 Raspberry Pi RP2350。參考板只有 520 KiB SRAM、4 MiB flash，售價約 0.8 美元；實際韌體約占 3.6 MiB flash，總共配置約 468 KiB RAM，全程不需雲端連線。

**關鍵不是把三套模型同時塞進記憶體，而是讓它們共用同一塊 arena**：VAD、STT、TTS 依序執行，因此可重用約 384 KiB 的 TensorFlow Lite Micro tensor arena。VAD 峰值只需約 36 KiB，STT 約 346 KiB，TTS 約 340 KiB；特徵生成還會把資料寫進推論暫時閒置的 arena 區域，避免另開大型 buffer。

**語音管線已能在微控制器上形成完整閉環**：VAD 每 32 ms 處理一次音訊，推論約 3.1 ms；SpellingCNN 辨識一秒片段約需 314 ms，神經 TTS 回覆一個字母約需 0.4–0.7 秒。TTS 以 16 kHz 串流輸出 PCM，不保留完整語句的音訊 buffer，並用 RVQ decoder、WORLD-lite vocoder 與 diphone／word units，在有限 RAM 中完成可理解的回話。

**能力邊界也寫得很誠實**：目前 STT 是 51 類分類器，只辨識獨立唸出的字母、數字與少量命令詞，並非 Whisper 式的連續語音轉錄。專案提供自訂 vocabulary 的完整訓練、int8 量化與 TFLM 匯出流程，因此它更像可部署的「裝置控制語音層」，而不是縮小版通用語音助理；程式與英文模型採 MIT 授權。

## 3W1H 分析
- **What（做了什麼/主題）**:
  Moonshine Micro 在 RP2350 上實作全離線 VAD → 指令辨識 → 神經 TTS 管線，讓低於一美元的微控制器也能聽懂限定語彙並開口回覆。示範應用甚至可讓 Pico 2 W 用語音逐字輸入 Wi-Fi SSID 與密碼，再念出取得的 IP 位址。
- **Why（為什麼重要）**:
  多數語音介面依賴手機級 SoC 或雲端 API，帶來延遲、隱私、網路可用性與持續成本問題。這個專案證明，只要把任務收斂為裝置真正需要的命令集合，端側語音互動的硬體門檻可以從「小型電腦」降到普通 MCU。
- **How（如何運作/實作）**:
  系統以 int8 TinyVadCNN 找出語音片段，再將一秒 log-mel 特徵交給 CMSIS-NN 加速的 SpellingCNN 分類，最後以 neural diphone TTS 串流產生 16 kHz 音訊。最精巧的工程取捨是讓三階段錯峰執行並共用 tensor arena，搭配雙核心 SIMD、量化模型與無完整音訊 buffer 的串流輸出，把峰值 RAM 壓在約 468 KiB。
- **Insight（個人心得）**:
  咱認為它最適合接在 Hermes 前面，當一層「永遠在線但權限極窄」的實體語音控制面：喚醒、取消、靜音、重試、切換模式等確定性命令留在 MCU，本就不必把每一秒環境聲送進通用模型。真正開放式的對話再轉交 Hermes，而現有 `text_to_speech` 負責較自然的長回覆；這種快慢雙層設計，比硬把完整助理壓進邊緣裝置更私密，也更可靠。
