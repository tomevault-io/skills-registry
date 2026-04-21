---
name: committing-changes
description: Creates ONE git commit following Conventional Commits. Use when user asks to commit, save changes, or mentions /commit.
metadata:
  author: gabbloquet
---

# Commit Changes

## Command
```bash
git add <files>
git commit -m "type(scope): description"
```

## Commit Format
```
<type>(<scope>): <description>

Co-Authored-By: Claude <noreply@anthropic.com>
```

## Types
| Type | Usage |
|------|-------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation |
| `refactor` | Code restructure |
| `test` | Add/fix tests |
| `chore` | Maintenance |

## Scopes
`battle`, `creatures`, `moves`, `ui`, `world`, `player`, `events`, `store`, `config`, `tests`, `assets`

## Rules
- Lowercase description, no period
- Body explains "why" if needed
- Breaking changes: add `!` after type

## Example
Input: "Commit les changements"
Output:
```bash
git add src/data/species.ts
git commit -m "feat(creatures): add Rocklet rock-type creature

Co-Authored-By: Claude <noreply@anthropic.com>"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gabbloquet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
