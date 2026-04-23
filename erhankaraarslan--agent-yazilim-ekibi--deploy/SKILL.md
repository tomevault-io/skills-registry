---
name: deploy
description: Production deployment workflow. Testleri çalıştırır, build alır ve deploy eder. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Deploy Workflow

$ARGUMENTS ortamına deployment yapar.

## Deployment Adımları

1. **Pre-flight Checks**
   ```bash
   # Git durumu temiz mi?
   git status --porcelain
   
   # Doğru branch'te miyiz?
   git branch --show-current
   ```

2. **Run Tests**
   ```bash
   npm test
   ```

3. **Build**
   ```bash
   npm run build
   ```

4. **Deploy**
   - `dev`: Otomatik deploy
   - `staging`: Otomatik deploy + smoke test
   - `prod`: Manuel onay gerekli

## Rollback

Hata durumunda:
```bash
git revert HEAD
npm run deploy:rollback
```

## Checklist

- [ ] Testler geçti
- [ ] Build başarılı
- [ ] Environment variables kontrol edildi
- [ ] Database migration'lar çalıştı
- [ ] Health check OK

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
