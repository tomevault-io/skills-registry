---
name: commit
description: Generate git commits following Conventional Commits (commitlint) format. Make sure to use this skill whenever the user mentions git commit, commit changes, make a commit, conventional commits, commitlint, or asks to commit any changes — even if they don't explicitly say "skill" or "Conventional Commits". This skill is especially useful when the user says things like "commit this", "commit my changes", "git add and commit", or wants a properly formatted commit message. Use when this capability is needed.
metadata:
  author: vainjs
---

## Format

```
<type>(<scope>): <description>
[optional body]
[optional footer(s)]
```

## Types

`feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`

## Rules

- **git user**: Always use the user's git config (user.name, user.email). Do NOT use AI-authored credentials. Check `git config --list` first to verify the correct user is configured.
- **type**: Required, lowercase, from list above
- **scope**: Optional. Only include when it clearly adds context (e.g., `feat(ui):` vs `feat:`). Use lowercase, kebab-case if multi-word.
- **description**: Required, lowercase start, no period at end, imperative mood (say what the change does, not what it did), max 72 chars
- **body**: Optional, wrap at 100 chars, explain "what" and "why" (not "how")
- **footer**: Optional, for breaking changes (`BREAKING CHANGE:`) or issue refs (`Closes #123`, `Fixes #456`)

## Breaking Changes

Append `!` to type/scope, or add `BREAKING CHANGE:` footer.

## Examples

```
feat(auth): add OAuth2 login support
fix(ui): resolve button alignment on mobile
docs: update API documentation
refactor(auth): simplify error handling logic
chore(deps): upgrade React to v19
```

## Workflow

1. Stage all changes with `git add -A`
2. Generate commit message following the format above
3. Output the commit message on its own line at the END of your response — nothing should follow it:

   feat(auth): add OAuth2 login

4. Wait for user confirmation or prompt suggestion prefill. Do NOT run `git commit` yourself.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vainjs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
