---
name: refactoring-strategies
description: Safe refactoring süreci - test-first, incremental changes ve güvenlik ağı. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# 🛡️ Refactoring Strategies

> Safe refactoring süreci ve incremental değişiklikler.

---

## ⏰ Ne Zaman Refactor?

| Sinyal | Aksiyon |
|--------|---------|
| Code Smell | Refactor et |
| Before feature | Zemin hazırla |
| After bug fix | Kodu iyileştir |

### ❌ Ne Zaman YAPMA
- Deadline çok yakın
- Test coverage düşük
- Sistemi anlamadan

---

## 🔒 Güvenlik Ağı

```typescript
// Önce mevcut davranışı test et
describe('calculateTotal', () => {
  test('single item', () => {
    expect(calculateTotal([{ price: 100, qty: 1 }])).toBe(100);
  });
  
  test('multiple items', () => {
    expect(calculateTotal([
      { price: 100, qty: 2 },
      { price: 50, qty: 1 }
    ])).toBe(250);
  });
  
  test('empty array', () => {
    expect(calculateTotal([])).toBe(0);
  });
});
```

---

## 📊 Incremental Strategy

1. **Test yaz** → Mevcut davranışı belgele
2. **Küçük değişiklik** → Tek bir şeyi değiştir
3. **Test çalıştır** → Hala geçiyor mu?
4. **Commit** → Küçük, atomik commit
5. **Tekrarla**

---

## ✅ Checklist

- [ ] Testler geçiyor
- [ ] Davranış değişmedi
- [ ] Küçük commit'ler
- [ ] Feature ile karıştırma

---

*Refactoring Strategies v1.1 - Enhanced*

## 🔄 Workflow

> **Kaynak:** [Working Effectively with Legacy Code](https://www.goodreads.com/book/show/44919.Working_Effectively_with_Legacy_Code) & [Refactoring to Patterns](https://www.tindustries.com/refactoring-to-patterns/)

### Aşama 1: Safety Net (Test First)
- [ ] **Characterization Tests**: Kodun "ne yapması gerektiğini" değil, "ne yaptığını" doğrulayan testler yaz.
- [ ] **Golden Master**: Çıktının değişmediğini garanti etmek için büyük ölçekli snapshot testleri al.
- [ ] **Coverage**: Refactor edilecek bölgenin coverage'ını %100'e getirmeden dokunma.

### Aşama 2: Strategic Patterns
- [ ] **Strangler Fig**: Eski sistemi bir anda değiştirmek yerine, yeni özellikleri "boğarak" (çevreleyerek) yavaş yavaş yerine geçir.
- [ ] **Parallel Change**: Eski ve yeni kodu bir süre paralel çalıştır (Feature Flag ile), emin olunca eskiyi sil.
- [ ] **Branch by Abstraction**: Kodun arasına bir interface (API) katmanı koy, arkasındaki implementasyonu değiştir.

### Aşama 3: Lifecycle Management
- [ ] **Deprecation**: Eski API'leri `@deprecated` olarak işaretle ve loglara uyarı bas.
- [ ] **Monitoring**: Refactor sonrası error rate ve latency'yi izle.
- [ ] **Kill Switch**: Feature Flag ile eski koda saniyeler içinde dönebilme imkanı sağla.

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | Test paketi 2 dakikanın altında çalışıyor mu? (Hızlı feedback). |
| 2 | Veritabanı şeması değişiyor mu? (Migration planı var mı?). |
| 3 | Rollback planı test edildi mi? |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
