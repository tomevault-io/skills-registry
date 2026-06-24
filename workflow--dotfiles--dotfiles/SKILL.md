---
name: pr-review
description: Summarize and adversarially review a GitHub pull request. Use when this capability is needed.
metadata:
  author: workflow
---

Review pull request `$ARGUMENTS`.

1. Fetch the PR metadata and diff via `gh pr view` and `gh pr diff`.
2. If the PR branch isn't already checked out locally, run `jj git fetch` and `jj new <branch>` to get it.
3. Summarize what the PR does and why.
4. Analyze the architecture and check whether this change could have been achieved in a simpler or more idiosyncratic way.
5. Perform an adversarial review: actively try to break the code. Look for logic errors, incorrect assumptions, edge cases, silent failures, security holes, race conditions, and performance traps. Only report real, substantiated issues with file:line references — not hypotheticals or linter-level nitpicks.

---
> Source: [workflow/dotfiles](https://github.com/workflow/dotfiles) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
