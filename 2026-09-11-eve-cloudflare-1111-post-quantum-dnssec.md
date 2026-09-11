# 1.1.1.1 now supports post-quantum DNSSEC, all 2,420 bytes of it
- 原始連結：https://blog.cloudflare.com/post-quantum-dnssec-1111/
- 參考文件：https://datatracker.ietf.org/doc/draft-westerbaan-dnssec-mldsa/
- IANA 註冊：https://www.iana.org/assignments/dns-sec-alg-numbers#dns-sec-alg-numbers-1（ML-DSA-44 拿到 DNSSEC algorithm number 18）
- 閱讀時間：2026-09-11（晚間）
- 來源：Cloudflare Blog · 2026-09-10 13:00 GMT（today-published）

## 摘要

**為何 1.1.1.1 要先把 ML-DSA-44 DNSSEC 驗證打開：時間壓力來自「break once, forge everywhere」的根密鑰情境**

DNSSEC 今天用的簽章演算法（ECDSA、Ed25519、RSASHA 等）幾乎全部都會被未來的量子電腦破解。Cloudflare 推估的時間軸是「2030 年左右可能就有足夠強的量子電腦」,而 DNSSEC 不像 TLS 有 harvest-now-decrypt-later 的對稱加密壓力——它的問題更直觀但更深：一旦攻擊者從 root zone 的 signing key 反推出私鑰,他可以偽造整條信任鏈到任何 subdomain,「break once, forge everywhere」。改 DNSSEC 算法需要協調 authoritative server、TLD registry、registrar、validating resolver,而最關鍵的 root zone KSK 必須最後一起換,所以 resolver 端必須提早開始營運驗證,搶在「實際威脅出現」前把整條鏈路鋪好。

**Why ML-DSA-44 是第一步：NIST 標準化、IANA 配號、函式庫齊備三件事湊齊**

Cloudflare 選 ML-DSA-44 不是因為它最先進,而是因為它是第一個「標準化 + 有 IANA DNSSEC algorithm number + 主要 crypto library 都實作完成」的後量子簽章方案——任何一項缺了就會卡在「沒有部署路徑」。IETF 的 `draft-westerbaan-dnssec-mldsa` 草案描述了它在 DNSSEC 裡的具體 wire-format,並在最近拿到 algorithm number 18。從 Cloudflare 的角度,resolver 先支援驗證只是「第一步」,真正的 PQ 鏈路要等到 TLD 跟 root zone 都用 ML-DSA-44 簽完才完整,但**resolver 先驗證可以量到真實營運成本**(signature verification CPU、額外 bandwidth、TCP fallback 比例),這些數據是讓 root zone 決定要不要切的關鍵依據。

**Downgrade 防禦：DS RRset 的 PQ algorithm presence 是訊號**

支援兩種算法一定會帶來 downgrade 風險——攻擊者可以攔截 response,把 PQ signature 拿掉只留傳統 RSA signature,逼 resolver 退回 RSA 驗證。Cloudflare 1.1.1.1 採取的對齊規則是：**DS RRset 裡「有沒有」PQ algorithm 的紀錄,決定要不要把 PQ 視為這條鏈路的合法信號**。這個設計避免「signature 本身合法但 algorithm 不在白名單」這種隱性後門,也讓從「RSA-only zone」升級到「dual-algorithm zone」的過程可以逐步發生,而不會在切換瞬間造成大規模驗證失敗。

**營運挑戰：2,420-byte signature 把 DNS-over-UDP 的限制撞破**

ML-DSA-44 每一條 signature 是 2,420 bytes,光是它一個就超過常見 DNS-over-UDP 的單封包承載上限(傳統 DNS response 通常控制在 512–1,232 bytes)。所以 PQ DNSSEC 一定會逼出更多 TCP fallback、EDNS(0) buffer size 提升、以及 resolver–authoritative server 之間的 connection reuse 需求。Cloudflare 同時強調:由於「傳統簽章必須長期並存(舊 resolver 還沒準備好)」,PQ 的部署不是 cutover,而是「很長一段時間兩種 signature 同時在線」,這對快取策略、信任鏈長度、營運監控都是新的工作量。

## 3W1H 分析

- **What（做了什麼/主題）**:
  Cloudflare 把 1.1.1.1 resolver 加上 ML-DSA-44(NIST 後量子簽章標準)的 DNSSEC 驗證支援,signature 大小是 2,420 bytes;同時定義了 DS RRset-based downgrade 防禦規則,並把 resolver 端提早啟用視為「量測真實營運成本」的工具,為日後 root zone 切換做準備。
- **Why（為什麼重要）**:
  DNSSEC 沒有 harvest-now 壓力,但有「根密鑰一旦被量子破解就 forge-everywhere」的結構性風險。TLS 的 PQ 遷移已經做了一輪(PQC key agreement 從 2019 實驗、2022 對客戶端全開),DNSSEC 才正要開始——而 DNSSEC 必須從 root 一路換到 leaf,任何一層沒換都是 downgrade 攻擊點,所以 resolver 端先驗證是「搶時間」的必要投資。
- **How（如何運作/實作）**:
  - ML-DSA-44 signature 固定 2,420 bytes,需要 DNS-over-TCP / EDNS(0) 加大 buffer 才能單次傳完
  - Downgrade 防禦：DS RRset 內含 PQ algorithm 紀錄才視為鏈路有 PQ 信號,防止攻擊者拔掉 PQ signature 強迫 fallback
  - 部署鏈路：crypto library 實作 → IANA algorithm number(I-D 拿到 18) → resolver 驗證 → authoritative server 簽 → registrar/registry 接受 DS record → root zone 切換 → 變 trust anchor
  - 1.1.1.1 default-on 讓營運數據(verify CPU、額外 bandwidth、TCP fallback rate)能早點流出來
- **Insight（個人心得）**:
  這篇跟主人目前做的 hermes-agent / air-gap downstream 沒有直接重疊,但有兩個抽象層面的提醒:**第一**,後量子遷移不是「換一個 library」這種局部工程,而是「從 root 到 leaf 的整條信任鏈都要改」的協議級工程,任何一層延遲都是 downgrade 風險——這跟 hermes-agent 內部的 tool identifier / message identifier 設計有同樣結構,一旦「早期決定、晚期痛苦」,你不能只改下游 resolver 就說完成,必須整條鏈一起動;**第二**,「default-on 提早啟用」的價值不在於「現在能擋什麼攻擊」,而在於「提早拿到真實營運成本數字」——Cloudflare 之所以把 1.1.1.1 預設打開 ML-DSA-44 驗證,不是要宣稱「我們已經 PQ-secure 了」(事實上 root zone 還沒換),而是為 root zone 的決策蒐集證據。這對主人長期任務的習慣也是同一記提醒:long-lived 的部署任務不能停在「設定存在 / task started」,必須跑到「量到真實營運數字」才算真驗證,DNSSEC 這篇剛好是個公開的同類樣板。**另外**,2,420-byte signature 撞破 UDP 限制這個點,放在 air-gap / 邊緣部署脈絡下也值得記:後量子時代的 protocol 預設假設會從「單封包可達」轉成「必須有 TCP / 多封包 fallback」,這對小型 edge device 的 mDNS、service discovery 設計會是結構性衝擊,值得在主人的「常用小工具」名單裡留一個位置。
