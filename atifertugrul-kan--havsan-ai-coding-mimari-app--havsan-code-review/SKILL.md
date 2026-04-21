---
name: havsan-code-review
description: name: havsan-code-review Use when this capability is needed.
metadata:
  author: atifertugrul-kan
---
﻿---
version: 3.1.6
name: havsan-code-review
description: HAVSAN Mühendislik Standartlarına göre kapsamlı kod incelemesi yapar. Kullanıcı "kod incelemesi", "hataları kontrol et" dediğinde veya görev bitiminde tetiklenir.
---

# 🛡️ HAVSAN Code Reviewer

Bu beceri, kodun sadece "çalışıp çalışmadığını" değil, **HAVSAN Standartlarına** uygun olup olmadığını denetler.

## 📋 İnceleme Kontrol Listesi (Checklist)

### 1. HAVSAN Mimarisi (Kritik)
*   [ ] **Docker:** Kod yerel yollara (C:\Users...) başvuruyor mu? (Yasak). Her şey Docker içinde mi?
*   [ ] **Dil:** Değişkenler İngilizce (`userList`), Yorumlar ve Commitler Türkçe (`// Kullanıcı listesi getiriliyor`) mi?
*   [ ] **Config:** Şifreler koda gömülü mü? (Yasak). Environment variable kullanılmış mı?

### 2. Kalite ve Temizlik
*   [ ] **Fonksiyonlar:** Bir fonksiyon 50 satırdan uzun mu? (Bölünmeli).
*   [ ] **İsimlendirme:** `var a = 1` gibi anlamsız isimler var mı?
*   [ ] **Hata Yönetimi:** `try-catch` blokları var mı? Hata kullanıcıya düzgün dönüyor mu?

### 3. Güvenlik
*   [ ] **SQL Injection:** Parametrik sorgu kullanılmış mı?
*   [ ] **Input Validation:** Kullanıcıdan gelen veri filtreleniyor mu?

## 📊 Raporlama Formatı

İnceleme sonucunu şu formatta sun:

```markdown
## 🔍 Kod İnceleme Raporu

### 🚨 Kritik Hatalar
*   [Dosyaadi.js]: Şifre açıkta unutulmuş.

### 💡 İyileştirme Önerileri
*   [Dosyaadi.js]: Fonksiyon çok uzun, ikiye bölünebilir.

### ✅ Onaylananlar
*   Mimari HAVSAN standartlarına uygun.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/atifertugrul-kan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
