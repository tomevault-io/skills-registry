---
name: git-conventions
description: Conventional commits et garde-fous git. Se charge sur : git commit, git push, git branch, git checkout -b, git merge, git rebase, git reset, git pull, git cherry-pick, git revert, création de PR (gh pr create), édition de message de commit. Couvre : messages conventional (feat:, fix:, refactor:, docs:, chore:, test:, style:, perf:, build:, ci:), nommage de branches (feat/*, fix/*, chore/*), messages et descriptions de PR, garde-fous destructifs (force push, reset --hard, branch -D, clean -f, push --force). Hors /codebloom:push qui gère son propre flow. Ne se charge PAS quand : opération git en lecture seule (git status, git log, git diff, git show, git blame). Use when this capability is needed.
metadata:
  author: vendeesign
---

# Git Conventions — Guide automatique

Ce skill s'active quand des opérations git sont effectuées en dehors de la commande `/codebloom:push`.

## Conventional Commits

Format : `type(scope): description`

### Types

| Type | Quand |
|------|-------|
| `feat` | Nouvelle fonctionnalité |
| `fix` | Correction de bug |
| `refactor` | Restructuration sans changement de comportement |
| `docs` | Documentation uniquement |
| `test` | Ajout ou modification de tests |
| `chore` | Maintenance, config, CI |
| `style` | Formatage, espaces, points-virgules |
| `perf` | Amélioration de performance |

### Règles de message

- **Impératif** : "add feature" pas "added feature"
- **Minuscule** : pas de majuscule après le type
- **Court** : max 72 caractères pour la première ligne
- **Descriptif** : expliquer le "pourquoi" dans le body si nécessaire
- **Scope** : optionnel, indique le module/composant concerné

### Exemples

```
feat(auth): add JWT token refresh
fix(api): handle timeout on slow connections
refactor: extract validation logic into helpers
docs: update API endpoints in README
```

## Nommage de branches

Format : `type/description-courte`

```
feature/user-auth
fix/login-timeout
chore/update-deps
refactor/api-structure
```

## Granularité des commits

- **Un commit = un changement logique** — pas un dump de fin de journée
- Séparer refactoring et feature dans des commits distincts
- Si le message nécessite "et" → probablement 2 commits
- Commit fréquent en local, squash si nécessaire avant merge

```
# MAL — un dump de fin de journée
git commit -m "fix login, add dashboard, update deps, refactor utils"

# BIEN — un commit par changement logique
git commit -m "fix(auth): handle expired JWT on refresh"
git commit -m "feat(dashboard): add weekly stats chart"
git commit -m "chore: update axios to 1.7.0"
```

## Garde-fous

### Avant chaque commit

- `.gitignore` vérifié — pas de `.env`, secrets, `node_modules`, fichiers temp
- Pas de clés API, tokens, mots de passe dans le code
- Pas de fichiers binaires volumineux
- `git diff --cached` relu pour vérifier ce qui part

### Opérations dangereuses — demander avant d'exécuter

Ces opérations sont irréversibles ou risquent de perdre du travail. Confirmer avec l'utilisateur :

- `git push --force` → réécrit l'historique distant, peut détruire le travail des autres (surtout sur main/master)
- `git reset --hard` → supprime les modifications locales non commitées — pas de récupération possible
- `git branch -D` → suppression sans vérification de merge — le travail sur cette branche peut être perdu
- `git rebase` sur branche partagée → réécrit l'historique, crée des conflits pour tous les collaborateurs

### En cas de conflit

- **Investiguer** avant de résoudre — comprendre les deux côtés du conflit
- Écraser silencieusement les changements d'un côté risque de casser des fonctionnalités
- En doute → demander à l'utilisateur quel côté préserver

## Message de PR

```markdown
## Summary
- [changements principaux en 1-3 bullets]

## Test plan
- [ ] [ce qui a été vérifié]
```

## Code Review via PR

Quand on review ou crée une PR, vérifier :

### Avant de créer la PR
- Titre court et descriptif (max 70 caractères)
- Description avec contexte (pourquoi, pas juste quoi)
- Une PR = un sujet (pas de "fix bug + add feature + update deps")
- Pas de fichiers hors périmètre (console.log, formatting, imports non utilisés)

### Quand on review
- Comprendre le contexte **avant** de commenter
- **Critique le code, pas la personne**
- Distinguer bloquant (bug, sécu) vs suggestion (style, perf)
- Proposer une alternative concrète, pas juste "c'est pas bien"
- Valider les tests et le build avant d'approuver

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vendeesign) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
