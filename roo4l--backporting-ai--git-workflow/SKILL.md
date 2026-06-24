---
name: git-workflow
description: Follow project git conventions for commits, branches, PRs, and publishing changes. Use when committing code, creating branches, opening pull requests, or pushing changes to the repository. Use when this capability is needed.
metadata:
  author: Roo4L
---

# Git Workflow

Full conventions are documented in [docs/dev/git-workflow.md](../../../docs/dev/git-workflow.md). Read that file for detailed rules and examples.

## Quick Reference

- **Commits**: imperative mood, capitalized, ~72 char subject. Add body for non-trivial changes.
- **Branches**: flat descriptive names (`add-search`, `fix-broken-link`). Branch from `main`.
- **Merge strategy**: rebase and merge (linear history).
- **PRs**: use `gh pr create`. Brief summary description. Target `main`.
- **Deploy**: merging to `main` auto-deploys to GitHub Pages.

## Maintainer Shortcut

As the sole maintainer, small changes that do **not** need CI/CD to verify can be pushed directly to `main` without a feature branch or PR. This includes:

- Documentation updates (markdown files, docs/)
- Cursor skills and rules (.cursor/)
- Minor config that does not affect the build

Everything else (UI changes, features, bug fixes, build-affecting config) **must** go through a feature branch and PR.

## Feature Workflow

Use this for any change that affects the site build or functionality.

```
1. git checkout main && git pull origin main
2. git checkout -b <branch-name>
3. Make commits (imperative mood, capitalized)
4. git fetch origin && git rebase origin/main
5. git push -u origin <branch-name>
6. gh pr create --title "<title>" --body "<summary>"
7. Merge via "Rebase and merge" on GitHub
8. Delete the branch after merge
```

## Quick-Fix Workflow

Use this for docs, cursor skills/rules, and other non-CI changes (maintainer only).

```
1. git checkout main && git pull origin main
2. Make changes and commit
3. git push origin main
```

---
> Source: [Roo4L/backporting_ai](https://github.com/Roo4L/backporting_ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
