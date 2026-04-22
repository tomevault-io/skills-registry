---
name: git
description: Git workflows, branching strategies, and hooks. Use when this capability is needed.
metadata:
  author: a5c-ai
---

# Git Skill

Expert assistance for Git workflows and best practices.

## Capabilities

- Configure Git workflows
- Set up branching strategies
- Implement Git hooks
- Handle merge strategies
- Configure commit conventions

## Branching Strategy

```
main
  └── develop
        ├── feature/user-auth
        ├── feature/dashboard
        └── bugfix/login-issue
```

## Git Hooks (Husky)

```json
// package.json
{
  "scripts": {
    "prepare": "husky install"
  }
}
```

```bash
# .husky/pre-commit
npm run lint
npm run test:staged
```

## Target Processes

- git-workflow
- code-review
- team-collaboration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/a5c-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
