---
name: commit
description: Generate conventional commit messages with emoji, ensure no Co-Authored-By Use when this capability is needed.
metadata:
  author: umans-ai
---

# Commit Helper

Generate conventional commit messages following project conventions.

## Usage

### `/commit [message]`

Without message: analyzes staged changes and suggests a commit message.
With message: validates and formats the message.

## Conventions

Format: `<type>: <description> <emoji>`

Types: feat, fix, docs, style, refactor, perf, test, build, ci, chore

Emojis:
- feat: ✨
- fix: 🐛
- docs: 📝
- style: 💄
- refactor: ♻️
- perf: ⚡
- test: ✅
- build: 📦
- ci: 🔧
- chore: 🧹

## Safety Checks

- NEVER add `Co-Authored-By:` lines
- Verify working directory has staged changes
- Warn if committing sensitive files (.env, credentials)

## Examples

```
/commit "add auth middleware"
→ "feat: add auth middleware ✨"

/commit "fix timeout bug"
→ "fix: timeout bug 🐛"

/commit
→ Analyzes git diff --staged and suggests type + message
```

## Errors

- No staged changes → "Error: No staged changes. Run: git add <files>"
- Co-Authored-By detected in message → "Error: Remove Co-Authored-By before commit"
- Sensitive files staged → "Warning: .env files detected. Unstage with: git reset HEAD <file>"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/umans-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
