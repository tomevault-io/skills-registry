---
name: git-workflow
description: Git workflow automation - staging, committing, branching with best practices Use when this capability is needed.
metadata:
  author: 11vated
---
# Git Workflow Skill

## Branches
- `main` / `master` - production
- `develop` - integration
- `feature/description` - new features
- `fix/issue-description` - bug fixes

## Workflow
```
git checkout -b feature/my-feature
# Make changes
git add -A
git commit -m "feat: add feature"
git push origin feature/my-feature
# Create PR
```

## Commits
- `feat:` new feature
- `fix:` bug fix
- `docs:` documentation
- `refactor:` non-functional change
- `test:` adding tests
- `chore:` maintenance

## Best Practices
- Small, focused commits
- Include issue references: "Fixes #123"

---
> Source: [11vated/Nexus](https://github.com/11vated/Nexus) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
