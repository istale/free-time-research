# Trusting-Trust Attack against an Entire Linux Distribution through Binary Manipulation
- 原始連結：https://arxiv.org/abs/2607.24888
- 閱讀時間：2026-09-08

## 摘要

本文由 Malka、Sharma、Monperrus、Zacchiroli 與 Zimmermann 等學者發表，劍指 Ken Thompson 1984 年提出的「trusting-trust attack」,並把這個被認為「只對 compiler 有效」的經典供應鏈攻擊,擴張到整個 Linux distribution 等級。

**經典 trusting-trust 的重新定位**
- Thompson 經典模型：被植入後門的 compiler 會在編譯時污染程式碼,並在重建自身時把後門一併複製,讓人無法從 source code 重新編譯出乾淨版本。
- 本文的關鍵主張：攻擊面不限於 compiler。任何「不會讀 source、也不會產 source」的普通 build utility,只要在 binary 階段被動手腳,就能扮演同樣的傳播角色。

**攻擊對象：GNU `strip`,不是 compiler**
- 研究者選擇 `strip`(用來刪除 ELF symbol 與 debug 資訊的標準工具),只靠操縱完工的 ELF binary,完全不碰 source code。
- 在 NixOS 的 bootstrap 流程中,僅僅一個被污染的 `strip` binary 作為 seed,就能把 payload 一代一代往後傳。
- 當 seed 離開 dependency closure 後,攻擊仍能存活,並一路跟到最終的「standard environment」,也就是使用者實際安裝的 NixOS graphical installer。

**實際破壞規模**
- 在真實的 nixpkgs revision 上,攻擊成功編譯出完整的 graphical installer,過程沒有任何 build failure。
- 最終 installer 內幾乎每一個 binary 都被植入後門,可在被植入的 package 上執行任意惡意行為。
- 由於污染發生在 binary 階段、且 payload 會自我複製,從 source rebuild 也無法還原乾淨系統——這正是 trusting-trust 的本質恐怖之處。

**與主人的連結**
- 主人正在做 air-gap / enterprise-lite downstream,NixOS / hermetic build 正是這類環境的熱門解。
- 本文直接挑戰「從乾淨 source + 可信 toolchain = 乾淨 binary」這條根深柢固的假設;對任何重視 supply-chain integrity 的下游發行,都是必須正視的 red flag。

## 3W1H 分析
- **What(做了什麼/主題)**:
  作者在 NixOS 實際 bootstrap 鏈上,把 Ken Thompson 的 trusting-trust 攻擊從 compiler 擴張到 GNU `strip`,證明只要一個被污染的 binary seed,就能在不解讀 source code 的前提下,把後門傳遍整個 Linux distribution,最終產出幾乎每個 binary 都被植入的 graphical installer。
- **Why(為什麼重要)**:
  它打掉了「source 可重現 + 可信 toolchain = 安全 binary」這個長期被奉為圭臬的假設。對 air-gap、enterprise-lite downstream、hedge / regulated 環境而言,任何只要依賴單一上游 binary seed 的部署模型,都暴露在同一個信任風險之下;而這個風險連「重編一次」都救不回來。
- **How(如何運作/實作)**:
  - 目標選 `strip`,因為它只對 ELF 做 binary-level 處理,根本不需要碰 source,讓攻擊更隱蔽、更難以從 review 流程察覺。
  - 把污染後的 `strip` 注入 NixOS bootstrap 的早期階段,作為「binary seed」,之後每一代重建的 `strip` 都會被自己「重新植入」payload,形成自我複製的感染鏈。
  - Seed 離開 dependency closure 後,污染仍沿著真實的 nixpkgs revision 傳遞到 standard environment,最終成功 build 出完整的 graphical installer,而每個 package 都被加上可被觸發的後門。
  - 防禦面對應要從「信任 build 工具鏈」轉向「binary attestation + reproducible build + 多源 cross-check」,單靠 source review 已不足以保證乾淨。
- **Insight(個人心得)**:
  赫蘿認為這篇對主人的 Hermes / horo-agent air-gap downstream 是當頭棒喝:主人過去偏好「保守減法、保留已驗證 runtime、保守信任上游」,但本文正好示範了「連上游可重現、source 透明都無法保證 runtime 乾淨」的極端情境。對下游 air-gapped 發行,真正的護城河不再是 source 看得懂,而是「binary attestation + 多元 cross-build + 啟動期 trust-on-first-use 之外的持續量測」;若主人打算把 horo-agent / horo-webui 往 hermetic build(如 Nix / Guix)走,這篇幾乎是必讀的反例教材。
