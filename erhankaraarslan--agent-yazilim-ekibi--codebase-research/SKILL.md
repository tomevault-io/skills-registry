---
name: codebase-research
description: Codebase'i derinlemesine araştırır. Mimari sorular, kod akışı analizi veya 'nasıl çalışıyor' sorularında kullan. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Codebase Research

$ARGUMENTS konusunu araştır.

## Araştırma Stratejisi

1. **Dosya Keşfi**
   - İlgili dosyaları `Glob` ile bul
   - Pattern'ları `Grep` ile ara

2. **Kod Analizi**
   - Entry point'leri belirle
   - Data flow'u takip et
   - Dependencies'i analiz et

3. **Raporlama**
   - Ana bulgular
   - İlgili dosyalar (path + satır)
   - Mimari pattern'lar
   - Öneriler

## Çıktı Formatı

```markdown
## Araştırma: [Konu]

### Özet
[1-2 cümle]

### Ana Bulgular
1. [Bulgu]
2. [Bulgu]

### İlgili Dosyalar
- `path/to/file.ts:L10-L50` - [açıklama]

### Sonraki Adımlar
- [öneri]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
