---
name: push
description: Stage, commit, and push changes with co-author attribution Use when this capability is needed.
metadata:
  author: lylaminju
---

Stage the updated files, commit them, and push to remote.

Follow these steps:
1. Run `git status` to see what files have changed
2. Run `git diff` to understand the changes
3. Stage the relevant changed files with `git add`
4. Commit with a clear, concise message following conventional commits:
   - feat: new feature
   - fix: bug fix
   - style: formatting/styling changes
   - perf: performance improvement
   - refactor: code refactoring
   - chore: maintenance tasks
   - docs: documentation changes

End the commit message with:
Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>

5. Push to remote with `git push`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lylaminju) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
