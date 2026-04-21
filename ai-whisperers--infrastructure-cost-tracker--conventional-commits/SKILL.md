---
name: conventional-commits
description: Guide for writing Conventional Commits. Use when writing commit messages, creating changelogs, or automating semantic versioning. Follows the Conventional Commits specification. Use when this capability is needed.
metadata:
  author: ai-whisperers
---

# Conventional Commits Skill

Guide for writing Conventional Commits to create clear, structured commit messages that enable automatic changelog generation and semantic versioning.

## When to Use

- Writing commit messages
- Creating changelogs
- Automating semantic versioning
- Maintaining clear git history
- Enforcing commit standards

## Commit Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Types

| Type | Description | SemVer |
|------|-------------|--------|
| `feat` | New feature | MINOR |
| `fix` | Bug fix | PATCH |
| `docs` | Documentation only | PATCH |
| `style` | Code style (formatting) | PATCH |
| `refactor` | Code refactoring | PATCH |
| `perf` | Performance improvement | PATCH |
| `test` | Adding/updating tests | PATCH |
| `chore` | Maintenance tasks | PATCH |
| `ci` | CI/CD changes | PATCH |
| `build` | Build system changes | PATCH |
| `revert` | Revert previous commit | PATCH |

## Examples

### Feature Commit

```
feat(auth): add OAuth2 login support

- Implement Google OAuth2 provider
- Add token refresh logic
- Update user model for OAuth fields

Closes #123
```

### Bug Fix Commit

```
fix(api): resolve null pointer in user validation

The validation was failing when user object was undefined.
Added null checks and proper error handling.

Fixes #456
```

### Breaking Change

```
feat(api): redesign authentication endpoints

BREAKING CHANGE: `/api/v1/login` is now `/api/v2/auth/login`
Old endpoints will be removed in v3.0.0
```

### Documentation Update

```
docs(readme): update installation instructions

- Add Docker setup guide
- Update Node.js version requirements
- Fix broken links
```

## Scopes

Common scopes for different projects:

**Web Projects:**
- `api` - Backend API changes
- `ui` - User interface changes
- `auth` - Authentication related
- `db` - Database changes

**AI/ML Projects:**
- `model` - ML model changes
- `data` - Data pipeline changes
- `train` - Training logic
- `infer` - Inference/prediction

## Tools

### Commitlint

Validate commits:

```bash
npx commitlint --from HEAD~1 --to HEAD --verbose
```

### Standard Version

Auto-generate changelog:

```bash
npx standard-version
```

### Semantic Release

Automated versioning:

```bash
npx semantic-release
```

## Best Practices

1. Use present tense ("add feature" not "added feature")
2. Use imperative mood ("move cursor" not "moves cursor")
3. Don't capitalize first letter
4. No period at the end
5. Keep first line under 72 characters
6. Use body to explain what and why, not how

## Common Patterns

```bash
# Feature with scope
feat(user): add profile page

# Fix with issue reference
fix(api): handle timeout error

Fixes #789

# Breaking change
feat(db): migrate to PostgreSQL

BREAKING CHANGE: MySQL no longer supported

# Multiple changes
docs: update README and CONTRIBUTING
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-whisperers) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
