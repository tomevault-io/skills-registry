---
name: github
description: Interact with GitHub using the gh CLI for pull requests, issues, and runs. Use when this capability is needed.
metadata:
  author: qhkm
---

# GitHub Skill

Use the `gh` CLI for repository operations.

## Pull Requests

Check CI status:
```bash
gh pr checks 55 --repo owner/repo
```

List workflow runs:
```bash
gh run list --repo owner/repo --limit 10
```

## Issues

List issues:
```bash
gh issue list --repo owner/repo --json number,title,state
```

Create issue:
```bash
gh issue create --repo owner/repo --title "Bug" --body "Description"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qhkm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
