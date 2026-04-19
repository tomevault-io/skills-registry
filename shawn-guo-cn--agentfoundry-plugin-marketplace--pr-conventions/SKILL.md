---
name: pr-conventions
description: Pull request conventions for comprehensive commit analysis and test planning. Use when creating or reviewing pull requests. Use when this capability is needed.
metadata:
  author: shawn-guo-cn
---

# PR Conventions

## Full Commit History Analysis

When creating a pull request, **always analyse the full commit history** — not just the latest commit.

1. Use `git diff [base-branch]...HEAD` to see all changes since the branch diverged
2. Use `git log [base-branch]..HEAD` to review every commit message
3. Draft a comprehensive PR summary that covers ALL commits, not just the most recent one

## Test Plan Requirement

Every PR must include a **Test plan** section with a bulleted TODO checklist:

```markdown
## Test plan
- [ ] Unit tests pass for new functionality
- [ ] Integration tests cover the critical path
- [ ] Manual verification of [specific behaviour]
- [ ] Edge cases tested: [list them]
```

## Branch Push Convention

When pushing a new branch for the first time, always use the `-u` flag:

```bash
git push -u origin <branch-name>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shawn-guo-cn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
