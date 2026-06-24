---
name: git-workflow
description: Advanced git workflows with branch management, conflict resolution, and PR lifecycle Use when this capability is needed.
metadata:
  author: sparkling
---

# Git Workflow

Advanced git workflow automation for branch management and PR lifecycle.

## When to use

When managing complex git operations — multi-branch workflows, release branching, conflict resolution, or PR coordination.

## Steps

1. **Analyze repo** — call `mcp__ruflo__github_repo_analyze` for repository health metrics
2. **Check diff risk** — call `mcp__ruflo__analyze_diff-risk` before merging
3. **Manage PRs** — call `mcp__ruflo__github_pr_manage` for PR lifecycle operations
4. **View metrics** — call `mcp__ruflo__github_metrics` for merge frequency, review times, etc.

## Common workflows

### Feature branch
```bash
git checkout -b feat/my-feature
# ... make changes ...
# analyze diff before PR
# create PR with risk assessment
```

### Release branch
```bash
git checkout -b release/v1.2.0
# cherry-pick fixes
# analyze all diffs for risk
# merge when risk score is acceptable
```

## CLI alternative

```bash
npx @sparkleideas/cli@latest hooks pre-task --description "git workflow"
```

---
> Source: [sparkling/ruflo](https://github.com/sparkling/ruflo) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
