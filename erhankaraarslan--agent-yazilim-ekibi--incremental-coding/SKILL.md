---
name: incremental-coding
description: Anthropic'in long-running agent best practice'lerine göre incremental coding yaklaşımı. Her session'da tek bir feature üzerinde çalış ve clean state bırak. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Incremental Coding Approach

Bu skill, [Anthropic'in "Effective Harnesses for Long-Running Agents"](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) makalesindeki best practice'lere göre çalışır.

## Session Başı Rutini (ZORUNLU)

Her coding session'ında ŞU SIRALAMAYI UYGULA:

### 1. Ortamı Anla
```bash
pwd                           # Çalışma dizini
git log --oneline -10         # Son commit'ler
git status                    # Değişiklikler
cat CLAUDE.md | head -50      # Son durum
```

### 2. Feature Listesini Oku
```bash
cat feature-list.json | jq '.features[] | select(.passes == false) | {id, priority, description}' | head -20
```

### 3. Tek Bir Feature Seç
- En yüksek priority'li
- Dependency'leri tamamlanmış
- "passes: false" olan

### 4. Development Server Başlat
```bash
if [ -f init.sh ]; then ./init.sh; fi
```

### 5. Mevcut Durumu Test Et
Temel fonksiyonelliğin çalıştığını doğrula. Broken state varsa ÖNCE düzelt.

## Coding Kuralları

### ✅ YAPILMASI GEREKENLER:
1. **Tek feature'a odaklan** - Birden fazla feature'a aynı anda başlama
2. **Incremental ilerle** - Küçük, test edilebilir adımlar at
3. **Sık commit yap** - Her mantıklı değişiklikten sonra
4. **Test et** - Acceptance criteria'yı end-to-end doğrula
5. **Document et** - CLAUDE.md ve feature-list.json güncelle

### ❌ YAPILMAMASI GEREKENLER:
1. **One-shotting** - Her şeyi tek seferde yapmaya çalışma
2. **Premature completion** - Test etmeden "bitti" deme
3. **Dirty state bırakma** - Yarım kalmış, belgelenmemiş kod bırakma
4. **Feature silme/değiştirme** - feature-list.json'da sadece `passes` değiştir

## Session Sonu Rutini (ZORUNLU)

Her session sonunda ŞU ADIMLAR YAPILMALI:

### 1. Clean State Kontrolü
```bash
npm run lint                  # Lint hataları?
npm run test                  # Test failure?
git status                    # Uncommitted files?
```

### 2. Feature Durumunu Güncelle
Eğer feature tamamlandıysa ve test edildiyse:
```json
{
  "passes": true,
  "completed_at": "2025-01-25",
  "actual_sessions": 1
}
```

### 3. Git Commit
```bash
git add -A
git commit -m "feat(F001): Implement user registration

- Added registration form component
- Integrated with API endpoint
- Added validation
- E2E tested with Puppeteer

Closes F001"
```

### 4. CLAUDE.md Güncelle
"Son Durum" ve "Aktif Context" bölümlerini güncelle.

### 5. Sonraki Session İçin Not
Eğer feature tamamlanmadıysa:
- Nerede kaldığını yaz
- Sonraki adımları belirt
- Blocker varsa not et

## Session Geçişi

Bir session'dan diğerine geçerken kaybolmaması gereken bilgiler:
- Hangi feature üzerinde çalışılıyordu
- Son yapılan değişiklikler
- Karşılaşılan sorunlar
- Test sonuçları
- Sonraki adımlar

Bu bilgiler CLAUDE.md'de ve git commit mesajlarında olmalı.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
