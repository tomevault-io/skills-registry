---
name: commit
description: Create conventional git commits from all uncommitted changes. Extracts ticket numbers from branch names and uses conventional commit format. Use when this capability is needed.
metadata:
  author: marsicdev
---

# Git Commit (All Changes)

Create a git commit from all uncommitted changes using Conventional Commits format with ticket extraction.

## Process

1. **Stage all changes**: Run `git add -A` to stage all changes
2. **Get branch name**: Run `git branch --show-current` to extract ticket number
3. **Review changes**: Run `git diff --cached` to see what will be committed
4. **Create commit**: Generate and execute commit with proper format

## Commit Message Format

```
type(scope): TICKET-123 subject

[optional body]
```

If no ticket found in branch name, omit it:
```
type(scope): subject
```

### Ticket Extraction

Extract ticket from branch name patterns:
- `feature/TEC-16401-description` → `TEC-16401`
- `bugfix/PROJ-283-fix-login` → `PROJ-283`
- `hotfix/APP-12345-critical` → `APP-12345`
- Pattern: `[A-Z]+-\d+` (uppercase prefix, dash, numbers)

### Types

| Type | Description |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, missing semicolons (no code change) |
| `refactor` | Code restructuring (no feature/fix) |
| `perf` | Performance improvement |
| `test` | Adding/updating tests |
| `build` | Build system or dependencies |
| `ci` | CI configuration |
| `chore` | Maintenance tasks |
| `revert` | Revert previous commit |

### Rules

- **Subject**: Imperative mood, present tense, no period, ~50 chars
- **Scope**: Optional, describes the affected area (e.g., `auth`, `ui`, `api`)
- **Ticket**: Include if found in branch name, placed after colon
- **Body**: Optional, wrap at 72 chars, explain "what" and "why"

## Examples

With ticket (from branch `feature/TEC-16401-auth-improvements`):
```
feat(auth): TEC-16401 add biometric login support

fix(cart): PROJ-283 resolve quantity update bug
```

Without ticket (from branch `main` or `develop`):
```
refactor: simplify user repository data mapping

docs: update API endpoint documentation
```

## Constraints

- Never commit sensitive files (.env, credentials, API keys)
- Review diff before committing
- Keep commits atomic and focused
- Never include AI attribution (Co-Authored-By, etc.)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marsicdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
