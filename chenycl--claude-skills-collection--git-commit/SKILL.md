---
name: git-commit
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Git Commit Message Generator

Generate clear, semantic commit messages following the [Conventional Commits](https://www.conventionalcommits.org/) specification and best practices from major open-source projects.

## Conventional Commits Format

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(auth): add OAuth2 login` |
| `fix` | Bug fix | `fix(api): handle null response` |
| `docs` | Documentation only | `docs(readme): add setup instructions` |
| `style` | Formatting, no code change | `style: fix indentation` |
| `refactor` | Code restructuring | `refactor(db): extract query builder` |
| `perf` | Performance improvement | `perf(images): add lazy loading` |
| `test` | Adding/fixing tests | `test(auth): add login unit tests` |
| `build` | Build system changes | `build: upgrade webpack to v5` |
| `ci` | CI configuration | `ci: add GitHub Actions workflow` |
| `chore` | Maintenance tasks | `chore: update dependencies` |
| `revert` | Revert previous commit | `revert: undo feat(auth) commit` |

### Scope (optional)
- Module/component name: `auth`, `api`, `ui`, `db`
- Feature area: `login`, `checkout`, `search`
- Layer: `frontend`, `backend`, `infra`

## Writing Guidelines

### Description (subject line)
- Use imperative mood: "add" not "added" or "adds"
- No capitalization at start
- No period at end
- Max 50 characters (hard limit: 72)
- Complete the sentence: "This commit will..."

### Body (when needed)
- Explain **what** and **why**, not how
- Wrap at 72 characters
- Use bullet points for multiple changes
- Reference issues: `Fixes #123`, `Closes #456`

### Footer
- Breaking changes: `BREAKING CHANGE: <description>`
- Issue references: `Refs: #123, #456`
- Co-authors: `Co-authored-by: Name <email>`

## Examples

### Simple feature
```
feat(search): add fuzzy matching support
```

### Bug fix with context
```
fix(auth): prevent session timeout on active users

Users were being logged out even during active sessions due to
the token refresh not resetting the timeout timer.

Fixes #234
```

### Breaking change
```
feat(api)!: change response format to JSON:API spec

BREAKING CHANGE: API responses now follow JSON:API specification.
All clients must update their response parsing logic.

Migration guide: docs/migration/v2-api.md
```

### Multiple changes (consider splitting)
```
refactor(db): restructure user module

- Extract UserRepository from UserService
- Add connection pooling configuration
- Implement query result caching

Part of #567 (database optimization initiative)
```

## Commit Analysis Workflow

When asked to generate a commit message:

1. **Analyze the diff** - Understand what changed
2. **Identify the type** - Is it a feature, fix, refactor, etc.?
3. **Determine scope** - What module/area is affected?
4. **Write subject** - Concise summary in imperative mood
5. **Add body if needed** - For non-obvious changes
6. **Check breaking changes** - Note any API changes

## Anti-patterns to Avoid

- `fix: fix bug` (not descriptive)
- `update code` (missing type and detail)
- `WIP` (don't commit work in progress)
- `misc changes` (be specific)
- Commits with unrelated changes (split them)

## Emoji Convention (optional)

Some projects use emoji prefixes:

| Emoji | Type |
|-------|------|
| ✨ | feat |
| 🐛 | fix |
| 📚 | docs |
| ♻️ | refactor |
| ⚡ | perf |
| 🧪 | test |
| 🔧 | config |
| 🚀 | deploy |

Example: `✨ feat(auth): add biometric login`

## References

- [Conventional Commits Spec](https://www.conventionalcommits.org/)
- [Angular Commit Guidelines](https://github.com/angular/angular/blob/main/CONTRIBUTING.md#commit)
- [How to Write a Git Commit Message](https://cbea.ms/git-commit/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
