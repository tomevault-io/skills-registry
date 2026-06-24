---
name: git-workflow
description: Apply Athens' git conventions when creating branches, writing commits, or opening PRs. Branches use type prefixes (feat/, fix/, chore/, hotfix/, docs/, refactor/, test/), commits follow Conventional Commits, and PRs target `dev` (not `main`) — except hotfixes, which target `main`. Trigger on phrases like "commit this", "create a branch", "open a PR", "push my changes", or any version-control task. Use when this capability is needed.
metadata:
  author: vincent-24
---

# Git Workflow (Athens)

## Branches

Format: `<prefix>/<short-kebab-description>`.

| Prefix      | Use for                                                  |
|-------------|----------------------------------------------------------|
| `feat/`     | New user-facing functionality                            |
| `fix/`      | Non-urgent bug fixes                                     |
| `hotfix/`   | Urgent production fixes (target `main` directly)         |
| `chore/`    | Deps, configs, tooling, build scripts                    |
| `docs/`     | Documentation only                                       |
| `refactor/` | Restructuring with no behavior change                    |
| `test/`     | Adding or improving tests                                |

Lowercase kebab-case, 3–5 words. No usernames in branch names. When a change crosses categories, pick the prefix that describes what the user gets — usually `feat/`.

## Pull requests

**PRs merge into `dev`, not `main`.** `main` reflects what's released; `dev` is the integration branch. The exception is `hotfix/` — those target `main` directly, then merge `main` back into `dev` so the branches don't drift.

### Title

`type: imperative description` — same prefixes as branches, lowercase after the colon, no trailing period.

- `feat: add walk mode camera controls`
- `fix: prevent parser crash on empty file`
- `chore: bump three.js to 0.160`

### Description

Three short sections:
1. **What** — one or two sentences on what changed.
2. **Why** — the problem or motivation.
3. **How to verify** — concrete steps, or note that tests cover it.

Add screenshots/recordings for client/UI changes. Call out anything risky or deferred.

## Commits

[Conventional Commits](https://www.conventionalcommits.org): `<type>(<optional scope>): <subject>`.

Types: `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `perf`, `style`. Scope narrows where the change lives — for Athens, scopes typically match the package: `core`, `parser`, `github`, `server`, `client`, `cli`.

- `feat(client): add fly-mode keybindings`
- `fix(parser): handle TSX fragments in analyzer`
- `chore(cli): bump cloudflared dep`
- `refactor(core): extract graph-walk helper`

Breaking changes append `!`: `feat(server)!: rename /api/graph to /api/scene`.

## Pre-PR gate

Before opening a PR, run:

```bash
npm run lint && npm run typecheck
npm test
```

## Typical flow

```bash
git checkout dev
git pull
git checkout -b feat/short-description
# ...work, commit with conventional messages...
git push -u origin feat/short-description
# Open PR into dev
```

For hotfixes, branch from `main`, PR into `main`, then merge `main` back into `dev`.

---
> Source: [vincent-24/athens](https://github.com/vincent-24/athens) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
