---
name: rollback
description: Git revert stratejileri, feature checkpoint'leri, emergency rollback. Kritik hatalarda kullanılacak kurtarma mekanizması. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Rollback Skill

Bu skill, hatalı deployment veya kod değişikliklerini geri almak için güvenli rollback stratejileri sağlar.

---

## 🎯 Rollback Prensipleri

### Golden Rule

> **"Forward is better than backward, but backward is better than broken"**

Önce düzelt (hotfix), düzeltemiyorsan geri al (rollback).

---

## 🚨 Ne Zaman Rollback?

### ✅ Rollback YAP

- Production'da kritik hata var
- Data corruption riski var
- Güvenlik açığı keşfedildi
- Performans %50+ düştü
- Fix süresi > rollback süresi

### ❌ Rollback YAPMA

- Küçük UI bug (hotfix at)
- Test environment hatası (fix et)
- Non-critical warning (log'la, sonra fix)

---

## 🔄 Rollback Türleri

### 1. Git Rollback (Code)

#### A. git revert (Önerilen)

```bash
# Son commit'i geri al (yeni commit oluşturur)
git revert HEAD

# Belirli commit'i geri al
git revert abc123

# Merge commit'i geri al
git revert -m 1 abc123

# Push
git push origin main
```

**Avantaj:** Tarihçe bozulmaz, audit trail var.

---

#### B. git reset (Tehlikeli!)

```bash
# ⚠️ DİKKAT: Sadece local branch için!

# Son commit'i sil (değişiklikler staged kalır)
git reset --soft HEAD~1

# Son commit'i sil (değişiklikler working dir'de)
git reset --mixed HEAD~1

# Son commit'i sil (değişiklikler kaybolur)
git reset --hard HEAD~1

# Remote'a zorla push (ÇOOOOK TEHLİKELİ!)
git push --force origin main  # ❌ ASLA main/master'da yapma!
```

**Kural:** `git reset --hard` sadece local branch'lerde!

---

#### C. git checkout (Geçici)

```bash
# Önceki commit'e geç (detached HEAD)
git checkout abc123

# Test et, sorun yoksa:
git checkout main

# Sorun varsa revert et:
git revert abc123..HEAD
```

---

### 2. Feature Rollback (feature-list.json)

```bash
# Feature durumunu değiştir
jq '.features[] | select(.id == "F005") | .status = "cancelled"' feature-list.json > tmp.json
mv tmp.json feature-list.json

# Feature'ı geri al (git diff ile bul)
git log --all --oneline --grep="F005"
git revert <commit-hash>
```

---

### 3. Database Rollback (Migration)

#### Prisma

```bash
# Son migration'ı geri al
npx prisma migrate resolve --rolled-back <migration-name>

# Veya manuel SQL
psql -d mydb -f migrations/down/20260126_rollback.sql
```

#### TypeORM

```bash
# Son migration'ı geri al
npm run migration:revert

# Birden fazla
npm run migration:revert
npm run migration:revert
```

#### Manual SQL

```sql
-- Rollback script örneği
BEGIN;

-- 1. Yeni kolonu sil
ALTER TABLE users DROP COLUMN new_column;

-- 2. Constraint'i geri koy
ALTER TABLE orders ADD CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES users(id);

-- 3. Data'yı geri yükle (eğer backup varsa)
INSERT INTO users SELECT * FROM users_backup WHERE created_at > '2026-01-26';

-- Test et, sorun yoksa commit
COMMIT;
-- Sorun varsa: ROLLBACK;
```

---

### 4. CLAUDE.md Rollback

```bash
# CLAUDE.md'yi backup'tan geri yükle
cp .claude-backup/CLAUDE.md.2026-01-26 CLAUDE.md

# Veya git'ten geri al
git checkout HEAD~5 -- CLAUDE.md
```

---

## 🛡️ Safe Rollback Procedure

### Adım adım güvenli rollback:

```bash
# 1. Backup al
git branch backup-before-rollback-$(date +%Y%m%d)
pg_dump mydb > backup-$(date +%Y%m%d).sql
cp CLAUDE.md CLAUDE.md.backup

# 2. Rollback yap
git revert HEAD
# veya
npm run migration:revert

# 3. Lokal test et
npm run test
npm run lint
npm run build

# 4. Staging'de test et
git push origin staging
# Smoke test yap

# 5. Production'a deploy
git push origin main
./deploy.sh

# 6. Health check
curl https://myapp.com/health/ready

# 7. Monitoring izle (5-10 dakika)
tail -f /var/log/app/error.log
# Sentry, Datadog kontrol et

# 8. Incident report yaz
# docs/incidents/2026-01-26-rollback.md
```

---

## 📦 Checkpoint Sistemi

### Otomatik Checkpoint (Önerilen)

```bash
# PreToolUse hook'una ekle (settings.json)
{
  "matcher": "Write|Edit",
  "hooks": [{
    "type": "command",
    "command": "if git diff --quiet; then exit 0; fi; git add -A && git commit -m 'checkpoint: auto-save before changes' 2>/dev/null || true"
  }]
}
```

### Manuel Checkpoint

```bash
# Feature başlamadan önce checkpoint
git add -A
git commit -m "checkpoint: before F005 implementation"
git tag checkpoint-F005-start

# Feature bittikten sonra
git tag checkpoint-F005-complete

# Geri dönmek için
git checkout checkpoint-F005-start
```

---

## 🔥 Emergency Rollback (Production)

### Scenario: Production çöktü, 2 dakikada geri al!

```bash
#!/bin/bash
# emergency-rollback.sh

set -e

echo "🚨 EMERGENCY ROLLBACK BAŞLIYOR..."

# 1. Son çalışan commit'i bul
LAST_WORKING_COMMIT=$(git log --oneline --grep="deploy: production" -1 --format="%H")

echo "📍 Geri dönülecek commit: $LAST_WORKING_COMMIT"

# 2. Revert yap
git revert HEAD --no-edit

# 3. Push
git push origin main

# 4. Deploy trigger
curl -X POST https://deploy.myapp.com/webhook

echo "✅ Rollback tamamlandı. Monitoring'i izleyin!"
echo "📊 Health: curl https://myapp.com/health/ready"
```

Kullanım:

```bash
chmod +x emergency-rollback.sh
./emergency-rollback.sh
```

---

## 📋 Rollback Checklist

Rollback öncesi:

- [ ] Backup aldın mı? (git branch, database dump)
- [ ] Rollback sebebi net mi?
- [ ] Stakeholder'lar bilgilendirildi mi?
- [ ] Rollback planı hazır mı?
- [ ] Test environment'ta denedin mi?

Rollback sonrası:

- [ ] Health check geçti mi?
- [ ] Error rate düştü mü?
- [ ] Monitoring normal mi?
- [ ] Data consistency kontrolü yapıldı mı?
- [ ] Incident report yazıldı mı?
- [ ] Post-mortem planlandı mı?

---

## 🧪 Rollback Test

### Test script:

```bash
# test-rollback.sh
#!/bin/bash

echo "🧪 Rollback simulation başlıyor..."

# 1. Dummy feature ekle
echo "// Test feature" >> src/test.ts
git add src/test.ts
git commit -m "feat: test feature for rollback simulation"

# 2. Rollback yap
git revert HEAD --no-edit

# 3. Doğrula
if [ ! -f src/test.ts ]; then
  echo "✅ Rollback başarılı"
  exit 0
else
  echo "❌ Rollback başarısız"
  exit 1
fi
```

Her sprint sonunda rollback test et!

---

## 🗄️ Database Rollback Stratejileri

### Blue-Green Migration

```sql
-- Eski tablo (blue)
CREATE TABLE users_v1 AS SELECT * FROM users;

-- Yeni tablo (green)
CREATE TABLE users_v2 (
  id SERIAL PRIMARY KEY,
  email TEXT,
  new_column TEXT -- Yeni alan
);

-- Data migration
INSERT INTO users_v2 (id, email, new_column)
SELECT id, email, NULL FROM users_v1;

-- Switch (atomic rename)
BEGIN;
ALTER TABLE users RENAME TO users_old;
ALTER TABLE users_v2 RENAME TO users;
COMMIT;

-- Rollback gerekirse:
BEGIN;
ALTER TABLE users RENAME TO users_v2;
ALTER TABLE users_old RENAME TO users;
COMMIT;
```

---

## 📝 Rollback Documentation

### Incident Report Template

```markdown
# Incident Report: Rollback - [YYYY-MM-DD]

## Özet
Production'da kritik hata tespit edildi ve rollback yapıldı.

## Timeline
- **10:00** - Deploy (commit: abc123)
- **10:15** - Error rate spike (%5 → %30)
- **10:18** - Sentry alert
- **10:20** - Rollback decision
- **10:22** - Rollback complete
- **10:25** - System normal

## Root Cause
Database migration'da foreign key constraint eksikti.

## Rollback Details
- **Commit:** `git revert abc123`
- **Database:** `npm run migration:revert`
- **Duration:** 2 dakika

## Impact
- Affected users: ~500
- Downtime: 5 dakika
- Data loss: Yok

## Prevention
- [ ] Migration review process ekle
- [ ] Staging'de constraint test et
- [ ] Rollback drill yap (aylık)

## Post-Mortem
Tarih: 2026-01-27 14:00
Katılımcılar: Team lead, DevOps, Backend
```

---

## 🎓 Rollback Best Practices

### DO ✅

- **Hızlı karar ver** - Rollback kararını 5 dakikada ver
- **Backup her zaman** - Rollback öncesi backup al
- **Incremental rollback** - Önce staging, sonra prod
- **Monitoring izle** - Rollback sonrası 30 dakika izle
- **Documentation** - Her rollback'i belgele
- **Test rollback** - Rollback prosedürünü test et

### DON'T ❌

- **Panic rollback** - Planla, sonra rollback
- **Partial rollback** - Tam geri al veya hiç alma
- **Silent rollback** - Takımı bilgilendir
- **Blame game** - Rollback = başarısızlık değil
- **Skip post-mortem** - Neden oldu? Nasıl önlenir?

---

## 🔗 Related Skills

- **error-recovery** - Rollback gerekmeden fix et
- **monitoring** - Rollback ihtiyacını erkenden tespit et
- **git-workflow** - Safe branching strategy

---

## 📚 Kaynakları

- [Git Revert vs Reset vs Checkout](https://www.atlassian.com/git/tutorials/undoing-changes)
- [Database Migration Best Practices](https://www.prisma.io/docs/guides/migrate)
- [Site Reliability Engineering - Rollback Chapter](https://sre.google/sre-book/release-engineering/)

---

**Son Güncelleme:** 2026-01-26
**Kullanıcı:** devops-engineer, backend-developer agent'lar
**Kritiklik:** HIGH - Production kurtarma mekanizması

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
