---
name: git-workflow
description: Perform git operations following a clean commit workflow Use when this capability is needed.
metadata:
  author: yai-dev
---
Follow these steps when committing or managing git history:

1. **Check the current state first**: run `git status` to see staged, unstaged, and untracked files before doing anything else.

2. **Review the diff before staging**: run `git diff` (unstaged) and `git diff --cached` (staged) to confirm you know exactly what will be committed.

3. **Stage selectively**: prefer `git add <file>` over `git add .` to avoid accidentally including build artifacts, secrets, or unrelated changes.

4. **Never commit secrets** — check for `.env`, API keys, tokens, and passwords. If found, remove them from the diff and add the file to `.gitignore`.

5. **Write a clear commit message**:
   - First line: `<type>: <short imperative summary>` (max 72 chars). Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`.
   - Leave a blank line, then add a body explaining *why* if needed.
   - Example: `feat: add steering queue to agent loop`

6. **Run tests before committing** if a test command exists (`npm test`, `pnpm test`, etc.). Do not commit if tests fail.

7. **Check for lint errors** before committing: run the project's lint command (`pnpm lint`, `eslint .`, etc.) if available.

8. **Use `git commit -m "$(cat <<'EOF' ... EOF)"` syntax** for multi-line commit messages to avoid shell escaping issues.

9. **After committing**, run `git log --oneline -5` to confirm the commit looks correct.

10. **For branch operations**: create feature branches with `git checkout -b <type>/<short-description>`. Never force-push to `main` or `master`.

---
> Source: [yai-dev/a101](https://github.com/yai-dev/a101) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
