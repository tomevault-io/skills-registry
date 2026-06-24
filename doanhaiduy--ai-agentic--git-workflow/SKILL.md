---
name: git-workflow
description: >- Use when this capability is needed.
metadata:
  author: Doanhaiduy
---

# 🌿 Skill: Git Workflow

> Chuẩn hóa git workflow cho team development: branching, commits, PR process.

## Khi nào sử dụng
- Setup git conventions cho project mới
- Review PR workflow
- Onboarding team member

## Branching Strategy (GitFlow Simplified)

```
main ───────────────────────────────────────────────▶ production
  │                                        ↑ (release merge)
  └── develop ─────────────────────────────┤
        │         ↑          ↑             │
        ├── feature/AUTH-42-oauth ──┘      │
        ├── feature/ORD-15-checkout ┘      │
        └── hotfix/SEC-01-xss-fix ─────────┘ (direct to main)
```

### Branch Naming Convention

```bash
# Pattern: <type>/<ticket-id>-<short-description>
feature/AUTH-42-google-oauth          # New feature
bugfix/UI-87-button-alignment         # Bug fix
hotfix/SEC-01-xss-fix                 # Urgent production fix
chore/update-dependencies             # Maintenance
refactor/orders-service-cleanup       # Code improvement
docs/api-documentation                # Documentation
```

### Branch Rules

| Branch | Created From | Merges Into | Who Can Merge | Protection |
|---|---|---|---|---|
| `main` | — | — | Tech Lead only | ✅ Protected |
| `develop` | main | main | Senior Dev | ✅ Protected |
| `feature/*` | develop | develop | Any Dev | ❌ |
| `bugfix/*` | develop | develop | Any Dev | ❌ |
| `hotfix/*` | main | main + develop | Senior Dev | ❌ |

## Conventional Commits

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type | When | Changelog? | Example |
|---|---|---|---|
| `feat` | New feature | ✅ MINOR | `feat(auth): add Google OAuth login` |
| `fix` | Bug fix | ✅ PATCH | `fix(orders): handle null address` |
| `docs` | Documentation | ❌ | `docs(api): update endpoint docs` |
| `test` | Add/update tests | ❌ | `test(users): add edge case tests` |
| `refactor` | Code improvement | ❌ | `refactor(common): extract validation utils` |
| `chore` | Maintenance | ❌ | `chore(deps): upgrade bcrypt to v5.1` |
| `ci` | CI/CD changes | ❌ | `ci(actions): add staging deploy` |
| `perf` | Performance | ❌ | `perf(queries): add index on users.email` |
| `style` | Formatting only | ❌ | `style: apply prettier formatting` |

### Breaking Changes

```bash
feat(api)!: change user endpoint response format

BREAKING CHANGE: The /api/v1/users response now uses `data` wrapper.
Before: [{ id, email }]
After: { data: [{ id, email }], meta: { page, total } }

Migration: Update all API consumers to unwrap `data` field.
```

## PR (Pull Request) Standards

```markdown
## Description
Brief description of what this PR does and why.

## Related Issues
Closes #42

## Type of Change
- [x] Feature
- [ ] Bug fix  
- [ ] Refactor
- [ ] Documentation

## Changes Made
- Added Google OAuth strategy with Passport.js
- Created OAuth callback handler
- Added user account linking logic

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing completed

## Checklist
- [ ] Code follows project conventions
- [ ] All tests passing (`npm test`)
- [ ] Lint passing (`npm run lint`)
- [ ] No console.log left in code
- [ ] CHANGELOG updated (if user-facing)
- [ ] Documentation updated (if behavior changed)

## Screenshots (if UI change)
```

## Merge Strategy

| From → To | Strategy | Reason |
|---|---|---|
| feature → develop | **Squash merge** | Clean history, 1 commit per feature |
| develop → main | **Merge commit** | Preserves feature history |
| hotfix → main | **Merge commit** | Cherry-pick to develop after |
| hotfix → develop | **Cherry-pick** | Keep histories separate |

## Git Hooks (Husky)

```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "npx lint-staged",
      "commit-msg": "npx commitlint --edit $1",
      "pre-push": "npm test"
    }
  },
  "lint-staged": {
    "*.ts": ["eslint --fix", "prettier --write"]
  }
}
```

## Quality Checklist
- [ ] Branch name follows convention?
- [ ] Commits follow conventional format?
- [ ] PR description complete with checklist?
- [ ] No merge conflicts?
- [ ] Squash merge for feature branches?
- [ ] Git hooks configured?

---
> Source: [Doanhaiduy/AI_agentic](https://github.com/Doanhaiduy/AI_agentic) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
