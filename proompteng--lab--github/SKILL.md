---
name: github
description: Work with GitHub in this repo: PR creation, CI checks, and gh CLI operations. Use when this capability is needed.
metadata:
  author: proompteng
---

# GitHub

## Overview

Use the GitHub CLI to create PRs, review checks, and inspect CI logs. Follow the repo conventions for commits and PR titles.

## Commit conventions

Use Conventional Commits:

```
fix(bumba): stabilize workflows
```

Example:

```
fix(bumba): stabilize workflows
```

## Create a PR

1. Copy `.github/PULL_REQUEST_TEMPLATE.md` to a temp file.
2. Fill it out.
3. Create the PR with `gh pr create`.

```bash
cp .github/PULL_REQUEST_TEMPLATE.md /tmp/pr.md
$EDITOR /tmp/pr.md
gh pr create --body-file /tmp/pr.md
```

## Check CI

```bash
gh pr checks 2259
gh run view 123456789 --log
```

## Merge

Use squash merge, do not delete the branch via CLI:

```bash
gh pr merge 2202 --squash -R proompteng/lab
```

## Resources

- Reference: `references/github-pr-guide.md`
- Helper: `scripts/create-pr.sh`
- Sample PR body: `assets/pr-body-template.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/proompteng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
