---
name: commit
description: Create well-formatted git commits with conventional commit messages and emoji. Use when user asks to commit changes, save work, or after completing a task that should be committed. Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Commit Skill

Create well-formatted commits with conventional commit messages and emoji prefixes.

## When to Use

- User explicitly asks to commit changes
- User asks to "save" or "commit" their work
- After completing a significant task (ask user first)
- User says "commit this" or similar

## Process

1. **Check status**: Run `git status` to see changes
2. **Review diff**: Run `git diff` to understand changes
3. **Check recent commits**: Run `git log --oneline -5` for commit style reference
4. **Stage files**: If no files staged, add relevant files with `git add`
5. **Analyze changes**: Determine if multiple commits are needed
6. **Create commit**: Use conventional commit format with emoji

## Commit Message Format

```
<emoji> <type>: <description>

[optional body]

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
```

## Commit Types with Emoji

| Type | Emoji | When to Use |
|------|-------|-------------|
| `feat:` | ✨ | New feature |
| `fix:` | 🐛 | Bug fix |
| `docs:` | 📝 | Documentation |
| `refactor:` | ♻️ | Code refactoring |
| `chore:` | 🔧 | Build/tooling |
| `perf:` | ⚡️ | Performance |
| `test:` | ✅ | Tests |
| `style:` | 🎨 | Code formatting |
| `ci:` | 🚀 | CI/CD changes |
| `fix:` | 🔒️ | Security fix |
| `chore:` | 🔖 | Release/version tag |

## Git Safety Rules

- NEVER update git config
- NEVER use destructive commands (push --force, hard reset) unless explicitly requested
- NEVER skip hooks unless explicitly requested
- NEVER amend commits that have been pushed
- NEVER commit files that may contain secrets (.env, credentials.json)

## Splitting Commits

Consider multiple commits when changes involve:
- Different concerns (unrelated code areas)
- Different types (features + fixes + docs)
- Different file patterns (source vs documentation)

## Example

```bash
git add src/components/NewFeature.tsx src/services/feature.ts
git commit -m "$(cat <<'COMMIT'
✨ feat: add user authentication system

Implements login, logout, and session management.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
COMMIT
)"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
