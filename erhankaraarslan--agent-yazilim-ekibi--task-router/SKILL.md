---
name: task-router
description: Gelen görevi analiz edip uygun agent'a yönlendirir. Kullanıcı hangi agent'ı kullanacağını bilmediğinde yardımcı olur. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Task Router Skill

Bu skill, Anthropic'in "Building Effective Agents" makalesindeki routing pattern'ını uygular. Kullanıcının görevini analiz eder ve en uygun agent'ı önerir.

## Ne Zaman Kullanılır?

- Kullanıcı "bunu kim yapmalı?" diye sorduğunda
- Karmaşık bir görev geldiğinde hangi agent'ların gerektiğini belirlemek için
- "En uygun agent hangisi?" sorusuna cevap vermek için

## Routing Matrisi

### Kritik Kararlar (ZORUNLU Agent)

| Trigger | Agent | Neden |
|---------|-------|-------|
| Yeni modül, mimari karar | `architect` | ADR gerekli |
| Database şema değişikliği | `database-admin` | Migration kritik |
| Auth/güvenlik kodu | `security-engineer` | Güvenlik review şart |
| 50+ satır kod değişikliği | `code-reviewer` | Kalite kontrolü |

### Özellik Bazlı Routing

| Anahtar Kelimeler | Önerilen Agent |
|-------------------|----------------|
| api, endpoint, backend, server | `backend-developer` |
| ui, component, frontend, react, css | `frontend-developer` |
| test, qa, bug, coverage | `qa-engineer` |
| deploy, ci/cd, docker, k8s | `devops-engineer` |
| docs, readme, documentation | `tech-writer` |
| user story, requirements, feature | `product-manager` |
| ilerleme, özet, durum | `progress-tracker` |
| proje başlat, initialize | `initializer` |

### Karmaşık Görevler (Çoklu Agent)

| Görev Türü | Agent Sırası |
|------------|--------------|
| Yeni feature (full stack) | 1. `architect` → 2. `database-admin` → 3. `backend-developer` → 4. `frontend-developer` → 5. `qa-engineer` |
| Bug fix | 1. `backend/frontend-developer` → 2. `qa-engineer` → 3. `code-reviewer` |
| Refactoring | 1. `architect` (plan) → 2. Developer → 3. `code-reviewer` |
| Security audit | 1. `security-engineer` → 2. `code-reviewer` |
| Release prep | 1. `qa-engineer` → 2. `devops-engineer` → 3. `tech-writer` |

## Routing Algoritması

```
1. INPUT: Kullanıcı görevi

2. ANALIZ:
   - Anahtar kelimeleri çıkar
   - Görev boyutunu tahmin et (S/M/L/XL)
   - Risk seviyesini belirle (güvenlik, veri, kritik path)

3. KARAR:
   - Kritik trigger var mı? → Zorunlu agent
   - Tek domain mı? → İlgili developer
   - Cross-domain mı? → Architect + takım

4. OUTPUT:
   - Önerilen agent(lar)
   - Çağrı sırası
   - Neden bu agent
```

## Routing Response Formatı

```markdown
## 🎯 Task Routing

**Görev:** [kullanıcının görevi]
**Analiz:** [kısa analiz]

### Önerilen Agent(lar)

| Sıra | Agent | Görev | Neden |
|------|-------|-------|-------|
| 1 | `architect` | Mimari plan | Yeni modül, ADR gerekli |
| 2 | `backend-developer` | API implementasyonu | CRUD endpoint'leri |
| 3 | `qa-engineer` | Test yazımı | Integration test gerekli |

### Alternatif Yaklaşım
[Eğer farklı bir yol da varsa]

### Başlangıç Komutu
```
@architect Bu yeni modül için mimari tasarım yap: [görev]
```
```

## Özel Durumlar

### "Bilmiyorum, sen karar ver"
```
Görevinizi analiz ediyorum...

Bu görev için en uygun yaklaşım:
1. Önce [agent] ile başlayın
2. Sonra [agent] devam etsin

Çünkü: [kısa açıklama]
```

### Basit Görevler
Routing gerektirmeyen basit görevler:
- Tek dosya düzenleme
- Basit bug fix
- Küçük refactoring

Bu durumda: "Bu görevi doğrudan yapabilirim, agent çağırmaya gerek yok."

### Belirsiz Görevler
```
Görevinizi daha iyi anlamak için:
1. Bu yeni bir özellik mi yoksa mevcut bir şeyi düzeltme mi?
2. Hangi modül/katmanı etkiliyor?
3. Test yazılması gerekiyor mu?
```

## Quick Reference Card

```
🏗️  Mimari → architect
🔧  Backend → backend-developer
🎨  Frontend → frontend-developer
🗄️  Database → database-admin
🔒  Güvenlik → security-engineer
🧪  Test → qa-engineer
🚀  Deploy → devops-engineer
📝  Docs → tech-writer
📋  Requirements → product-manager
📊  İlerleme → progress-tracker
🆕  Yeni proje → initializer
👀  Code review → code-reviewer
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
