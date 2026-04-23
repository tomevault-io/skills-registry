---
name: bugfix
description: Quality bug fixes (P1/P2). Full TDD cycle, branch from master via feature/, no production deploy. Use when this capability is needed.
metadata:
  author: fall-out-bug
---

# @bugfix

Quality bug fixes with full TDD cycle. Branch from master via feature/.

## When to Use

- P1 (HIGH) or P2 (MEDIUM) issues
- Feature broken but not production
- Reproducible errors

## Workflow

1. **Read issue** — `bd show <id>` or load from `docs/issues/`
2. **Branch** — `git checkout master && git pull && git checkout -b fix/{id}-{slug}`
3. **TDD** — Red: failing test → Green: minimal fix → Refactor
4. **Quality gates** — Run quality gates (see Quality Gates in AGENTS.md)
5. **Commit** — `git commit -m "fix(scope): description"`
6. **Push** — `git push -u origin fix/{branch}` then `gh pr create --base master`

## Output

Bug fixed, tests added, issue closed, changes pushed.

## See Also

- @hotfix — P0 emergency
- @issue — Classification
- @debug — Root cause analysis

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fall-out-bug) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
