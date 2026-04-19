---
name: commit
description: Create commits following Conventional Commits format. Analyzes staged changes and auto-generates appropriate commit messages. Use when the user says "commit", "commit changes", "commit staged files", etc., or when git changes need to be committed. Use when this capability is needed.
metadata:
  author: hackersheet
---

# Commit Creation Skill

## Project-Specific Rules

- **Do not add Co-Authored-By**
- **Body is required by default** - See [commit-rules.md](references/commit-rules.md) for omission conditions
- **Use only one scope** - Split commits if multiple scopes are needed
- Check recent commit style with `git log --oneline -5` before creating message

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hackersheet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
