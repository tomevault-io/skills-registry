---
name: session-start-verification
description: Her coding session'ın başında çalıştırılacak verification rutini. Ortamı kontrol eder, son durumu anlar ve nereden devam edileceğini belirler. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Session Start Verification

Bu skill, Anthropic'in long-running agent best practice'lerine göre her session başında çalıştırılır.

## Otomatik Verification Adımları

### 1. Çalışma Dizini Kontrolü
```bash
pwd
ls -la
```

### 2. Git Durumu ve Son Commit'ler
```bash
git status
git log --oneline -10
git branch --show-current
```

### 3. Progress ve Feature Dosyalarını Oku
```bash
# CLAUDE.md oku - son durum ne?
cat CLAUDE.md | head -100

# Feature listesi oku - ne bekliyor?
cat feature-list.json 2>/dev/null || echo "Feature list bulunamadı"
```

### 4. Development Server Kontrolü
```bash
# init.sh varsa çalıştır
if [ -f init.sh ]; then
  ./init.sh
fi

# Veya package.json varsa
if [ -f package.json ]; then
  npm run dev &
  sleep 5
  curl -s http://localhost:3000/health || echo "Server başlamadı"
fi
```

### 5. Temel Fonksiyonellik Testi
Eğer Puppeteer MCP bağlıysa:
- Ana sayfayı aç
- Temel bir akışı test et
- Screenshot al

### 6. Durum Özeti Oluştur

Session başında şu format ile rapor ver:

```
📊 SESSION START VERIFICATION
============================
Branch: main
Son commit: abc1234 - "Add user authentication"
Uncommitted files: 3
Feature progress: 5/15 tamamlandı
Son çalışılan feature: F003 - Login flow
Sonraki feature: F004 - User registration

⚠️ DİKKAT EDİLECEKLER:
- 2 test başarısız
- Lint uyarıları var
- Migration pending

✅ DEVAM EDİLECEK İŞ:
F004 - User registration formunu implement et
```

## KURALLAR

1. **ASLA bu adımları atlama** - Her session bu kontrolle başlamalı
2. **Broken state varsa ÖNCE düzelt** - Yeni feature'a geçme
3. **Findings'leri CLAUDE.md'ye yaz** - Sonraki session için
4. **Tek bir feature seç** - Birden fazla işe başlama

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
