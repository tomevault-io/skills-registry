---
name: commit
description: > Use when this capability is needed.
metadata:
  author: alexander-turner
---

# Conventional Commits Skill

## Workflow

### 1. Review Changes

Run in parallel: `git status`, `git diff`, `git diff --cached`, `git log --oneline -5`

### 2. Stage Files

Stage by name — never `git add -A` or `git add .`. Skip secrets (`.env`, credentials). If changes span unrelated areas, ask user whether to split into multiple commits.

### 3. Commit

Format: `<type>(<optional scope>): <imperative lowercase description>`

- Allowed types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `ci`, `style`, `perf`, `build`
- Under 72 chars, no trailing period
- Add `!` for breaking changes: `feat!: remove legacy API`
- Optional body (blank line after subject) for the **why**
- Use HEREDOC for multi-line messages:

```bash
git commit -m "$(cat <<'EOF'
feat(sdk): return Result type from authenticate

BREAKING CHANGE: authenticate() no longer throws on failure.
EOF
)"
```

### 4. Verify

If commitlint rejects the message, fix and create a **new** commit (don't amend). Confirm hash and message to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexander-turner) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
