---
name: ci-cd
description: CI/CD pipeline patterns for GitHub Actions including test, build, deploy stages, caching, matrix testing, and release automation. Use when setting up or modifying CI/CD pipelines. Use when this capability is needed.
metadata:
  author: bigpapicb
---

# CI/CD

## Decision Tree

```
Need CI/CD → What stack?
    ├─ Node/TypeScript → Use assets/ci-node.yml.template
    ├─ Python → Use assets/ci-python.yml.template
    └─ Multi-stack → Combine relevant templates
```

## Pipeline Stages

```
Push/PR → Lint → Test → Build → Deploy (main only)
```

## Key Patterns

### Caching
```yaml
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: npm-${{ hashFiles('**/package-lock.json') }}
    restore-keys: npm-
```

### Branch Protection
- Require PR reviews before merge
- Require status checks to pass
- Require branch is up to date
- No force pushes to main

### Secrets
- Use GitHub Secrets (never echo in logs)
- Use OIDC for cloud deployments (no static credentials)
- Scope secrets to environments (staging, production)

## Anti-Patterns

| Anti-Pattern | Fix |
|-------------|-----|
| No caching | Cache dependencies |
| `npm install` in CI | Use `npm ci` (deterministic) |
| Skip tests for speed | Parallelize instead |
| Deploy on every push | Use tags/releases |
| Manual version bumps | Automate with semantic-release |

## Templates:
- [Node/TypeScript CI](assets/ci-node.yml.template)
- [Python CI](assets/ci-python.yml.template)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bigpapicb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
