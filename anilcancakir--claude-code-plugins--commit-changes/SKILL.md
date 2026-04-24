---
name: commit-changes
description: Create a well-formatted commit following project conventions. Auto-detects commit style from git history. Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Commit Task

Create a commit for staged/unstaged changes following project conventions.

## Steps

1. Check `git status` for changes
2. Analyze `git log --oneline -10` to detect commit style
3. Categorize changes (feat/fix/docs/refactor/test/chore)
4. Generate commit message matching detected style
5. Present to user for approval
6. Execute commit

## Commit Style Detection

Use the detection script for accurate results:

```bash
# Returns JSON with style and confidence
"${CLAUDE_PLUGIN_ROOT}/scripts/detect-commit-style.sh"
```

**Output example:**
```json
{
  "style": "conventional-scoped",
  "confidence": 80,
  "stats": { "total": 10, "conventional_scoped": 8, "gitmoji": 2 }
}
```

| Pattern | Style |
|---------|-------|
| `type(scope): message` | Conventional Commits (scoped) |
| `type: message` | Conventional (no scope) |
| `:emoji: message` | Gitmoji |
| Plain text | Simple |

## Conventional Commits Types

| Type | Use For |
|------|---------|
| feat | New features |
| fix | Bug fixes |
| docs | Documentation only |
| style | Formatting changes |
| refactor | Code restructuring |
| perf | Performance improvements |
| test | Adding/fixing tests |
| chore | Maintenance tasks |

## Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Subject Rules:**
- Imperative mood ("add" not "added")
- No period at end
- Max 50 characters
- Lowercase

**Body** (optional):
- Explain what and why
- Wrap at 72 characters

**Footer** (optional):
- `Closes #123`
- `BREAKING CHANGE: description`

## Output

Present message for approval before committing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
