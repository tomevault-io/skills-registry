---
name: commit-conventions
description: Standards pour les commits Git. Use when "commit", "git commit", "commit message", "prepare commit". Use when this capability is needed.
metadata:
  author: feraudet
---

# Commit Conventions

## Purpose
Définir les standards pour les commits Git dans le projet consultant-manager.

## Format de Commit Message

### Structure
```
<type>(<scope>): <subject>

[body optionnel]

[footer optionnel]
```

### Type (Obligatoire)
- `feat` - Nouvelle fonctionnalité
- `fix` - Correction de bug
- `refactor` - Refactoring (ni feat ni fix)
- `style` - Changements de style (format, indentation)
- `docs` - Documentation uniquement
- `test` - Ajout ou modification de tests
- `chore` - Maintenance (deps, config, build)
- `perf` - Amélioration de performance

### Scope (Optionnel mais Recommandé)
Indique la partie du projet affectée:
- `backend` - Backend Express/Prisma
- `frontend` - Frontend React
- `api` - Routes API
- `db` - Base de données/Prisma
- `ui` - Interface utilisateur
- `consultants` - Fonctionnalité consultants
- `missions` - Fonctionnalité missions
- `dashboard` - Dashboard

### Subject (Obligatoire)
- Commence par un verbe à l'impératif (add, update, fix, remove)
- Pas de point final
- Maximum 72 caractères
- En français ou anglais (cohérence dans le projet)

### Body (Optionnel)
- Explique le "pourquoi" plus que le "quoi"
- Séparé du subject par une ligne vide
- Peut contenir plusieurs paragraphes

### Footer (Optionnel)
- Breaking changes: `BREAKING CHANGE: description`
- Références issues: `Fixes #123`, `Closes #456`

## Exemples

### ✅ Bons Commits

**Feature simple:**
```
feat(consultants): add email validation in form
```

**Fix avec contexte:**
```
fix(api): prevent duplicate consultant emails

Validate email uniqueness before creating consultant to avoid
Prisma unique constraint errors.
```

**Refactoring:**
```
refactor(backend): extract status calculation to utility

Move consultant status calculation logic from controller to
utils/status.ts for better testability and reusability.
```

**Breaking change:**
```
feat(api): change consultant status enum values

BREAKING CHANGE: Status values changed from French to uppercase
constants (DISPONIBLE, EN_MISSION, EN_CONGES, INDISPONIBLE).
Frontend must be updated to use new values.
```

**Multiple scopes:**
```
feat(consultants,missions): add revenue calculation

- Add calculateRevenue utility function
- Display total revenue in mission list
- Show consultant total earnings in profile
```

**Documentation:**
```
docs: update README with Prisma Studio instructions
```

**Chore:**
```
chore(deps): update dependencies to latest versions

Updated React, Express, Prisma to latest stable releases.
No breaking changes.
```

### ❌ Mauvais Commits

```
fix bug
```
❌ Pas de scope, pas de détails

```
Updated stuff
```
❌ Pas de type, trop vague

```
feat(consultants): Added a new feature that allows users to create consultants with validation and error handling and also refactored the form component
```
❌ Subject trop long, fait plusieurs choses

```
fix: typo
```
❌ Pas assez de contexte (où?)

```
WIP
```
❌ Work in progress ne devrait pas être committé sur main

## Workflow Git

### Branches

**Main branch:**
- `master` ou `main` - Production-ready code
- Toujours stable, tests passent

**Feature branches:**
- `feature/add-consultant-photo`
- `feature/export-to-excel`
- `fix/mission-date-validation`
- `refactor/api-error-handling`

### Convention de Nommage des Branches
```
<type>/<short-description>

Exemples:
- feature/consultant-filters
- fix/date-calculation-bug
- refactor/prisma-queries
- docs/api-documentation
```

### Workflow Typique

```bash
# Créer une feature branch
git checkout -b feature/add-consultant-search

# Faire des commits atomiques
git add src/components/ConsultantSearch.tsx
git commit -m "feat(consultants): add search input component"

git add src/pages/Consultants.tsx
git commit -m "feat(consultants): integrate search in consultant list"

# Pousser la branch
git push -u origin feature/add-consultant-search

# Créer une Pull Request
# (via GitHub/GitLab UI ou gh CLI)
```

## Commits Atomiques

### Principe
Un commit = Une modification logique complète et testable.

### ✅ Bon: Commits Séparés
```bash
git commit -m "feat(consultants): add TJM field to consultant form"
git commit -m "feat(api): add TJM validation in backend"
git commit -m "test(consultants): add TJM validation tests"
```

### ❌ Mauvais: Commit Monolithique
```bash
git commit -m "feat: add TJM everywhere"
# (contient form, API, tests, docs dans un seul commit)
```

## Quand Commiter?

### ✅ Commiter Quand:
- Une fonctionnalité est complète et testée
- Un bug est corrigé
- Un refactoring est terminé (code marche toujours)
- Tests ajoutés/mis à jour
- Documentation mise à jour

### ❌ Ne Pas Commiter:
- Code qui ne compile pas
- Tests qui échouent
- Code commenté en masse
- Console.log de debug partout
- Fichiers temporaires (.env.local, node_modules)
- WIP (sauf sur feature branch personnelle)

## Pull Requests

### Titre de PR
Format similaire au commit:
```
feat(consultants): Add consultant search and filters
```

### Description de PR
Template:
```markdown
## Description
Ajoute la fonctionnalité de recherche et filtres pour les consultants.

## Changes
- Ajout composant SearchBar
- Ajout filtres par statut
- Intégration API avec query params
- Tests des filtres

## Screenshots
[Si applicable]

## Test Plan
- [ ] Rechercher par nom fonctionne
- [ ] Filtrer par statut fonctionne
- [ ] Combinaison recherche + filtre fonctionne
- [ ] Tests unitaires passent
- [ ] Pas de régression sur liste consultants

## Notes
Nécessite backend version >= 1.2.0
```

## Hooks Git

### Pre-commit
```bash
#!/bin/bash
# .git/hooks/pre-commit

# Run linter
npm run lint

# Run tests
npm test

# Check TypeScript
npm run build
```

## Messages Utiles

### Pour Chercher dans l'Historique
```bash
# Commits par auteur
git log --author="John"

# Commits sur un fichier
git log -- path/to/file

# Commits avec un mot-clé
git log --grep="consultant"

# Commits de la dernière semaine
git log --since="1 week ago"
```

## Co-Authoring (avec Claude)

Quand Claude Code aide à coder:
```
feat(consultants): add consultant export to Excel

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

## Checklist Pre-Commit

Avant de commiter:
- [ ] Code compile sans erreurs TypeScript
- [ ] Tests passent (`npm test`)
- [ ] Pas de console.log oubliés
- [ ] Pas de commentaires TODO non résolus (ou trackés)
- [ ] Message de commit suit la convention
- [ ] Commit est atomique (une seule responsabilité)
- [ ] .gitignore respecté (pas de node_modules, .env, etc.)

## Erreurs à Éviter

### ❌ Commit trop gros
```bash
# 50 fichiers modifiés
git add .
git commit -m "updates"
```

### ✅ Commits logiques
```bash
git add src/components/ConsultantForm.tsx
git commit -m "feat(consultants): improve form validation"

git add src/api/consultants.ts
git commit -m "feat(api): add email uniqueness check"
```

### ❌ Messages vagues
```
git commit -m "fix stuff"
git commit -m "update"
git commit -m "changes"
```

### ✅ Messages descriptifs
```
git commit -m "fix(missions): correct revenue calculation for partial months"
git commit -m "refactor(api): extract validation logic to middleware"
```

## Outils

### Commit Template
```bash
# Créer un template
git config commit.template ~/.gitmessage.txt

# ~/.gitmessage.txt
# <type>(<scope>): <subject>
#
# [body]
#
# [footer]
#
# Types: feat, fix, refactor, style, docs, test, chore, perf
# Scopes: backend, frontend, api, db, ui, consultants, missions, dashboard
```

### Conventional Commits CLI
```bash
npm install -g @commitlint/cli @commitlint/config-conventional

# Valider les messages
echo "feat: add new feature" | npx commitlint
```

## Ressources

- [Conventional Commits](https://www.conventionalcommits.org/)
- [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)
- [Semantic Commit Messages](https://gist.github.com/joshbuchea/6f47e86d2510bce28f8e7f42ae84c716)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/feraudet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
