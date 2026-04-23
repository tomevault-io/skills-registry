---
name: e2e-testing
description: End-to-end testing yaklaşımı. Feature'ları production-like ortamda test etmek için kullanılır. Puppeteer MCP ile browser automation destekler. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# End-to-End Testing Skill

Bu skill, Anthropic'in "Effective Harnesses for Long-Running Agents" makalesindeki test yaklaşımını uygular.

## Neden E2E Testing Kritik?

> Claude's tendency to mark a feature as complete without proper testing. Absent explicit prompting, Claude tended to make code changes, and even do testing with unit tests or `curl` commands against a development server, but would fail to recognize that the feature didn't work end-to-end.

## Testing Hierarchy

```
     ┌─────────────────────┐
     │   E2E (Browser)     │  ← Feature tamamlandı mı?
     ├─────────────────────┤
     │   Integration       │  ← API'ler çalışıyor mu?
     ├─────────────────────┤
     │   Unit Tests        │  ← Fonksiyonlar doğru mu?
     └─────────────────────┘
```

## Feature Tamamlanmadan ÖNCE

Bir feature'ı `passes: true` yapmadan önce:

### 1. Unit Test
```bash
npm test -- --grep "feature-name"
```

### 2. Integration Test (API)
```bash
curl -X POST http://localhost:3000/api/endpoint \
  -H "Content-Type: application/json" \
  -d '{"test": "data"}'
```

### 3. E2E Test (Browser - Puppeteer MCP)
```
# Puppeteer MCP ile:
1. puppeteer_navigate → http://localhost:3000
2. puppeteer_screenshot → başlangıç durumu
3. puppeteer_click → form elemanları
4. puppeteer_fill → test verileri
5. puppeteer_click → submit button
6. puppeteer_screenshot → sonuç durumu
7. puppeteer_evaluate → DOM kontrolleri
```

## Test Senaryoları Template

### Happy Path
```
1. Kullanıcı ana sayfaya gider
2. [Feature'a özgü adımlar]
3. Başarılı sonuç görülür
```

### Error Path
```
1. Kullanıcı geçersiz veri girer
2. Hata mesajı görülür
3. Form state korunur
```

### Edge Cases
```
1. Boş veri
2. Çok uzun veri
3. Özel karakterler
4. Concurrent requests
```

## Test Sonuçlarını Kaydetme

Test screenshot'larını kaydet:
```
tests/screenshots/
├── F001-start.png
├── F001-step1.png
├── F001-result.png
└── F001-error.png
```

## Puppeteer MCP Kullanımı

Puppeteer MCP bağlıysa:

```javascript
// Navigate
await puppeteer_navigate({ url: "http://localhost:3000" });

// Screenshot
await puppeteer_screenshot({ name: "feature-test" });

// Click
await puppeteer_click({ selector: "#submit-btn" });

// Fill form
await puppeteer_fill({ selector: "#email", value: "test@test.com" });

// Wait
await puppeteer_wait({ selector: ".success-message" });

// Evaluate
const result = await puppeteer_evaluate({ 
  script: "document.querySelector('.result').textContent" 
});
```

## Test Checklist

Feature'ı tamamlamadan önce:

- [ ] Unit testler geçiyor
- [ ] API testleri başarılı
- [ ] E2E happy path çalışıyor
- [ ] Error handling test edildi
- [ ] Edge case'ler test edildi
- [ ] Screenshot'lar kaydedildi
- [ ] Test sonuçları dokümante edildi

## ⚠️ Önemli Uyarılar

1. **Test etmeden feature tamamlama** - Sadece kod yazmak yeterli değil
2. **Sadece unit test** - E2E olmadan feature eksik
3. **Manual test** - Otomatize et, tekrarlanabilir olsun
4. **Browser modals** - Puppeteer bazı native dialog'ları göremez, dikkat et

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
