---
name: create-pr
description: Create a pull request after code review Use when this capability is needed.
metadata:
  author: rubberduck203
---

# Create PR Workflow

1. **Run the `code-review` subagent** to review this branch. Fix all blockers before proceeding.
2. Run `git status` and `git diff main...HEAD` to understand all changes
3. Run `git log main..HEAD --oneline` to see all commits on this branch
4. Push the branch: `git push -u origin HEAD`
5. Create the PR with `gh pr create`:
   - Title: short, under 70 characters
   - Body: Summary bullets, test plan checklist, and the Claude Code footer
6. Return the PR URL
7. **Run the `reflect` skill** — retrospect on what was just built and persist any new patterns, surprises, or lessons to rules and memory

Do NOT create the PR if the code-review subagent reports blockers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rubberduck203) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
