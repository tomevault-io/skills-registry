---
name: commit-message
description: Generates commit messages. Use before any git operations involving a commit, or when writing, generating, or creating commit messages.
metadata:
  author: vexxhost
---

# Commits

- Format: `type(scope): message`
- Scope SHOULD indicate the area of change
- Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`, `ci`
- SHOULD be one logical change per commit - suggest splitting unrelated changes
- SHOULD explain why, not just what changed
- MUST use `git commit -s` to add DCO sign-off (do not add sign-off manually)
- MUST update PR body when adding follow-up commits to an open PR

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vexxhost) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
