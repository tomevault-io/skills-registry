---
name: commit
description: Run tests and commit changes. Use when the user asks to commit, save work, or says /commit. Use when this capability is needed.
metadata:
  author: kentrino
---

Run tests, then commit the session's changes with a conventional commit message.

## Steps

### 1. Run autofix and tests

```bash
bun run autofix
bun run test
```

If any check fails, fix the issues and re-run. Do NOT proceed to commit until all checks pass.

### 2. Check for changes

Run `git add --intent-to-add .` then `git diff HEAD -- . ':!bun.lock'`.
If there are no changes, inform the user and stop.

### 3. Commit

Write a conventional commit message based on the session's changes.

- Format: `type(scope): subject` — imperative mood, lowercase, no trailing dot, max 100 chars
- Add a body after a blank line only when the subject alone is not self-explanatory
- If unrelated changes exist, split into separate commits per logical unit of work
- Do NOT stage files that likely contain secrets (`.env`, credentials)

Examples:

- `fix(poll): handle timeout edge case when interval exceeds deadline`
- `ci(autofix): allow renovate[bot] in claude-code-action`
- `refactor: simplify AwaitReadyResult to plain string union`

Stage relevant files and commit using a HEREDOC. Run `git status` after to confirm.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kentrino) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
