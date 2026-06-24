---
name: pr-workflow
description: >- Use when this capability is needed.
metadata:
  author: Imaginary-Space
---

# PR workflow

## Defaults

- Branch from latest `main` (or the repo’s default): `git fetch origin && git checkout -b claude/<short-slug>`.
- Prefer **small commits** with clear messages (see `.claude/rules/commit-style.md`).
- Open PRs with `gh pr create` when available; include summary, test plan, and risk notes.

## Commands (adapt names if your default branch differs)

```bash
git status
git diff
git add -A && git commit -m "feat: ..."
git push -u origin HEAD
gh pr create --fill
```

## After the PR exists

- For sustained triage (comments, CI, conflicts), delegate to the **`pr-babysitter`** subagent when appropriate.
- If CI is red, reproduce locally with the narrowest test command before pushing speculative fixes.

---
> Source: [Imaginary-Space/claude-toolkit](https://github.com/Imaginary-Space/claude-toolkit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
