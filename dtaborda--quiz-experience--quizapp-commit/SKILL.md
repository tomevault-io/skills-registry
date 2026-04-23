---
name: quizapp-commit
description: > Use when this capability is needed.
metadata:
  author: dtaborda
---

## Critical Rules

- Only commit after the user explicitly asks for it—never proactively offer.
- ALWAYS use Conventional Commits: `type(scope): subject` (≤72 chars).
- ALWAYS describe the outcome/why; keep file counts, line numbers, and code minutiae out of the subject.
- ALWAYS ensure `pnpm lint`, `pnpm test`, and `pnpm format` already ran (or explain if you could not run them).
- NEVER stage secrets, `.env*`, generated artifacts, or unrelated files.
- NEVER use `git commit -n/--no-verify`, `git push --force`, or amend commits unless the user instructs you to.
- NEVER invent scopes that do not reflect the touched packages.

---

## Commit Format

```
type(scope): concise subject

- Key change 1
- Key change 2
```

### Types

| Type | Use When |
|------|----------|
| `feat` | New feature or functionality |
| `fix` | Bug fix |
| `docs` | Documentation-only changes |
| `chore` | Maintenance, dependency bumps, config tweaks |
| `refactor` | Code changes that don't add features or fixes |
| `test` | Adding/updating tests |
| `perf` | Performance optimizations |
| `style` | Formatting, no logic change |

### Scopes

| Scope | When |
|-------|------|
| `frontend` | Files under `frontend/` |
| `backend` | Files under `backend/` |
| `shared` | Files under `shared/` |
| `docs` | Files under `docs/` |
| `infra` | Docker, Devbox, `infra/` |
| `skills` | Files under `skills/` |
| `config` | Root configs (`package.json`, `pnpm-workspace.yaml`, `turbo.json`, etc.) |
| *omit* | Cross-package or repo-wide commits |

---

## Good vs Bad Examples

### Title Line

```
# GOOD - Concise and clear
feat(frontend): add learning mode banner
fix(backend): handle quiz not found response
chore(skills): add quizapp-pr guidance
docs: update introduction guide

# BAD - Too specific or verbose
feat(frontend): add learning mode banner with gradient animation for every quiz card (3 variants)
chore(skills): add eight new paragraphs to quizapp-pr skill documentation
fix(backend): fix the bug in quiz-service.ts on line 45
```

### Body (Bullet Points)

```
# GOOD - High-level changes
- Surface learning mode badge on quiz cards
- Persist toggle inside attempt metadata
- Document QA workflow in docs/developer-guide/introduction.mdx

# BAD - Too detailed
- Add `learningBadgeShown` flag to QuizCard.tsx default true
- Update attempt-store.ts persist config at lines 45-67
- Add three screenshots to docs page
```

---

## Workflow

1. **Analyze changes**
   ```bash
   git status -sb
   git diff --stat HEAD
   git log -5 --oneline   # match existing style
   ```

2. **Draft commit message**
   - Pick the correct `type(scope)`.
   - Keep the subject outcome-focused and ≤72 chars.
   - Add 2–4 bullet points when multiple files change.

3. **Stage intentionally**
   ```bash
   git add <files>
   git status -sb
   ```
   Confirm only the intended paths are staged.

4. **Execute commit**
   ```bash
   git commit -m "type(scope): subject"
   # Multi-line body
   git commit -m "$(cat <<'EOF'
   type(scope): subject

   - Change 1
   - Change 2
   EOF
   )"
   ```

---

## Decision Tree

```
Single file changed?
├─ Yes → Subject only may be enough (body optional)
└─ No  → Include bullet body with key outcomes

Multiple scopes?
├─ Yes → Omit scope: type: subject
└─ No  → Include scope: type(scope): subject

Bug fix?
├─ User-facing → fix(scope)
└─ Internal → chore(scope)

Docs change?
├─ Standalone doc update → docs(scope)
└─ Code docstrings → part of corresponding feat/fix
```

---

## Commands

```bash
# Check current state
git status -sb
git diff --stat HEAD

# Stage files
git add <files>

# Standard commit
git commit -m "type(scope): subject"

# Multi-line commit
git commit -m "$(cat <<'EOF'
type(scope): subject

- Change 1
- Change 2
EOF
)"

# Amend last commit (if user asked)
git commit --amend --no-edit
git commit --amend -m "new message"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtaborda) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
