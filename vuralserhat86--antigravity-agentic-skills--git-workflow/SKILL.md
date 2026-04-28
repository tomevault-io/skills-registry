---
name: git-workflow
description: Branch stratejisi, commit conventions, merge conflict çözümü ve Git best practices rehberi. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# 🌿 Git Workflow

> Branch stratejisi, commit conventions ve Git best practices rehberi.

---

## 📋 İçindekiler

1. [Branching Stratejileri](#1-branching-stratejileri)
2. [Commit Conventions](#2-commit-conventions)
3. [Merge vs Rebase](#3-merge-vs-rebase)
4. [Conflict Resolution](#4-conflict-resolution)
5. [Useful Commands](#5-useful-commands)

---

## 1. Branching Stratejileri

### Git Flow
```
main (production)
  └── develop
        ├── feature/user-auth
        ├── feature/payment
        └── release/v1.2.0
              └── hotfix/critical-bug
```

### GitHub Flow (Önerilen - Basit)
```
main (always deployable)
  ├── feature/add-login
  ├── fix/button-style
  └── chore/update-deps
```

### Branch Naming
```bash
# Feature
feature/user-authentication
feature/JIRA-123-add-payment

# Bug Fix
fix/login-redirect-issue
bugfix/memory-leak

# Hotfix (production)
hotfix/critical-security-patch

# Other
chore/update-dependencies
refactor/auth-module
docs/api-documentation
```

---

## 2. Commit Conventions

### Conventional Commits
```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Types
| Type | Açıklama |
|------|----------|
| `feat` | Yeni özellik |
| `fix` | Bug düzeltme |
| `docs` | Dokümantasyon |
| `style` | Formatting (kod değişikliği yok) |
| `refactor` | Refactoring |
| `perf` | Performans iyileştirme |
| `test` | Test ekleme/düzeltme |
| `chore` | Build, CI, dependencies |
| `ci` | CI configuration |
| `revert` | Revert commit |

### Örnekler
```bash
feat(auth): add OAuth2 login support

fix(api): resolve null pointer in user endpoint
Closes #123

refactor!: drop support for Node 14
BREAKING CHANGE: Minimum Node version is now 18

chore(deps): update lodash to 4.17.21
```

### Commit Message Rules
```
✅ DOĞRU:
- Imperative mood: "Add feature" (not "Added" or "Adds")
- 50 karakter başlık limiti
- Büyük harfle başla, nokta koyma
- Açıklayıcı body (neden, nasıl)

❌ YANLIŞ:
- "Fixed stuff"
- "WIP"
- "asdfasdf"
- "Updated code"
```

---

## 3. Merge vs Rebase

### Merge
```bash
# Feature branch'i main'e merge
git checkout main
git merge feature/user-auth

# Merge commit oluşturur
# History korunur
```

### Rebase
```bash
# Feature branch'i main üzerine rebase
git checkout feature/user-auth
git rebase main

# Linear history
# Commit'ler yeniden yazılır
```

### Ne Zaman Hangisi?
| Durum | Strateji |
|-------|----------|
| Public/shared branch | Merge |
| Local feature branch | Rebase |
| Main'e feature merge | Squash merge |
| Hotfix | Merge |

### Squash Merge
```bash
git checkout main
git merge --squash feature/user-auth
git commit -m "feat(auth): add user authentication"
```

---

## 4. Conflict Resolution

### Conflict Markers
```
<<<<<<< HEAD
Current branch content
=======
Incoming branch content
>>>>>>> feature-branch
```

### Resolution Steps
```bash
# 1. Conflict'leri gör
git status

# 2. Dosyaları düzenle (markers kaldır)

# 3. Çözümlenmiş dosyaları stage
git add <resolved-file>

# 4. Merge/rebase devam
git merge --continue
# veya
git rebase --continue
```

### VS Code ile
```bash
# Accept Current Change
# Accept Incoming Change
# Accept Both Changes
# Compare Changes
```

### Abort
```bash
git merge --abort
git rebase --abort
```

---

## 5. Useful Commands

### History
```bash
# Güzel log
git log --oneline --graph --all

# Son 10 commit
git log -10 --oneline

# Dosya history
git log --follow -p -- path/to/file
```

### Undo
```bash
# Son commit'i geri al (değişiklikleri koru)
git reset --soft HEAD~1

# Son commit'i tamamen geri al
git reset --hard HEAD~1

# Commit'i revert et (yeni commit oluştur)
git revert <commit-hash>

# Staged dosyayı unstage
git restore --staged <file>

# Değişiklikleri geri al
git restore <file>
```

### Stash
```bash
# Değişiklikleri sakla
git stash

# Mesajla sakla
git stash push -m "WIP: feature X"

# Stash listesi
git stash list

# Son stash'i uygula
git stash pop

# Belirli stash'i uygula
git stash apply stash@{2}
```

### Interactive Rebase
```bash
# Son 3 commit'i düzenle
git rebase -i HEAD~3

# Açılan editörde:
pick abc1234 First commit
squash def5678 Second commit  # Öncekiyle birleştir
reword ghi9012 Third commit   # Mesajı değiştir
```

### Cherry Pick
```bash
# Belirli commit'i al
git cherry-pick <commit-hash>

# Birden fazla
git cherry-pick <hash1> <hash2>
```

---

*Git Workflow v1.1 - Enhanced*

## 🔄 Workflow

> **Kaynak:** [Atlassian Git Workflows](https://www.atlassian.com/git/tutorials/comparing-workflows) & [Trunk Based Development](https://trunkbaseddevelopment.com/)

### Aşama 1: Branching
- [ ] **Strategy**: Çoğu ekip için "Trunk Based Development" (kısa ömürlü feature branch'ler) kullan.
- [ ] **Naming**: `feat/` `fix/` ön eklerini standartlaştır.
- [ ] **Lifetime**: Branch ömrü 2 günü geçmemeli. Geçiyorsa parçala.

### Aşama 2: Committing
- [ ] **Atomic**: Bir commit sadece bir şeyi değiştirmeli.
- [ ] **Message**: `feat(auth): add login` formatını (Conventional Commits) zorunlu tut.
- [ ] **Verification**: `pre-commit` hook ile linter/test çalıştır.

### Aşama 3: Merging
- [ ] **Review**: Code Owner onayı olmadan merge etme.
- [ ] **Method**: Geçmişi temiz tutmak için `Squash Merge` tercih et.
- [ ] **Cleanup**: Merge sonrası branch'i sil.

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | Main branch her an deploy edilebilir (Green) durumda mı? |
| 2 | `git log --oneline` okunduğunda bir hikaye anlatıyor mu? |
| 3 | Conflict çözülürken kod kaybı yaşanma riski var mı? |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
