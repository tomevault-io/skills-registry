---
name: sentry-commit
description: Create commits following Sentry conventions - meaningful messages, atomic changes, and proper formatting. Use when making git commits. Use when this capability is needed.
metadata:
  author: allanninal
---

# Sentry Commit Conventions

## When to Use This Skill

- Making git commits
- Writing commit messages
- Organizing changes into commits
- Preparing code for review

## Commit Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(auth): add SSO login` |
| `fix` | Bug fix | `fix(api): handle null response` |
| `docs` | Documentation | `docs(readme): update setup guide` |
| `style` | Formatting | `style(lint): fix indentation` |
| `refactor` | Code restructure | `refactor(db): extract query builder` |
| `perf` | Performance | `perf(cache): add redis caching` |
| `test` | Tests | `test(auth): add login tests` |
| `build` | Build system | `build(deps): upgrade webpack` |
| `ci` | CI/CD | `ci(github): add lint workflow` |
| `chore` | Maintenance | `chore: update gitignore` |
| `revert` | Revert commit | `revert: feat(auth): add SSO` |

### Scope

The scope should be the module, component, or area affected:

```
feat(auth): ...       # Authentication module
fix(api/users): ...   # Users API endpoint
docs(contributing): ...  # Contributing docs
refactor(ui/button): ... # Button component
```

### Subject Line Rules

```markdown
## Good Subject Lines
- Start with lowercase (unless proper noun)
- No period at the end
- Use imperative mood ("add" not "added")
- Keep under 50 characters
- Be specific and descriptive

## Examples
✅ feat(auth): add password reset flow
✅ fix(api): handle rate limit errors gracefully
✅ refactor(db): extract connection pooling logic

❌ feat(auth): Added password reset flow.
❌ fix: fix stuff
❌ refactor(db): this refactors the database connection pooling logic to be more efficient
```

### Body (Optional but Recommended)

```markdown
fix(api): handle rate limit errors gracefully

The API was crashing when receiving 429 responses from
the upstream service. This adds proper error handling
and implements exponential backoff.

- Add RateLimitError exception class
- Implement retry logic with exponential backoff
- Add circuit breaker for repeated failures
- Log rate limit events for monitoring
```

### Footer

```markdown
# Reference issues
Fixes #123
Closes #456
Refs #789

# Breaking changes
BREAKING CHANGE: API response format changed

The `user` field is now `users` (array) in the response.
Migration guide: https://docs.example.com/migration
```

## Atomic Commits

### Principles

```markdown
## Each Commit Should:
1. Represent one logical change
2. Leave the codebase in a working state
3. Be independently reviewable
4. Be revertable without side effects
```

### Example: Breaking Up Changes

```bash
# Instead of one large commit:
# "feat: add user management"

# Break into atomic commits:
git commit -m "feat(db): add users table migration"
git commit -m "feat(models): add User model with validation"
git commit -m "feat(api): add user CRUD endpoints"
git commit -m "feat(ui): add user management page"
git commit -m "test(users): add integration tests"
git commit -m "docs(api): document user endpoints"
```

## Git Workflow

### Staging Changes

```bash
# Stage specific files
git add src/auth/login.ts src/auth/logout.ts

# Interactive staging (pick hunks)
git add -p

# Stage all changes (be careful!)
git add .
```

### Creating Commits

```bash
# Quick commit with message
git commit -m "type(scope): subject"

# Open editor for detailed message
git commit

# Amend last commit (before push)
git commit --amend

# Amend without changing message
git commit --amend --no-edit
```

### Before Committing

```bash
# Review what's staged
git diff --staged

# Check status
git status

# Run tests
npm test

# Run linter
npm run lint
```

## Commit Message Templates

### Feature

```
feat(scope): add feature description

Implement [feature] to enable [capability].

- Add [component/function]
- Update [related component]
- Include [tests/docs]

Closes #123
```

### Bug Fix

```
fix(scope): resolve issue description

The issue occurred because [root cause].
This fix [explanation of solution].

Before: [problematic behavior]
After: [correct behavior]

Fixes #456
```

### Refactor

```
refactor(scope): improve code description

Restructure [component] to improve [quality attribute].

Changes:
- Extract [function/class]
- Rename [old name] to [new name]
- Remove unused [code]

No functional changes.
```

### Breaking Change

```
feat(api)!: change authentication method

BREAKING CHANGE: JWT tokens now required for all API calls.

Migration:
1. Generate API key in dashboard
2. Include in Authorization header
3. Update client SDK to v2.0+

See migration guide: https://docs.example.com/auth-migration
```

## Commit Hooks

### Pre-commit

```bash
#!/bin/bash
# .git/hooks/pre-commit

# Run linter
npm run lint || exit 1

# Run type check
npm run typecheck || exit 1

# Run tests
npm test -- --passWithNoTests || exit 1
```

### Commit-msg

```bash
#!/bin/bash
# .git/hooks/commit-msg

MSG=$(cat "$1")
PATTERN="^(feat|fix|docs|style|refactor|perf|test|build|ci|chore|revert)(\(.+\))?(!)?: .{1,50}"

if ! echo "$MSG" | grep -qE "$PATTERN"; then
  echo "❌ Invalid commit message format"
  echo "Expected: type(scope): subject"
  exit 1
fi
```

## Best Practices

- [ ] One logical change per commit
- [ ] Write clear, descriptive messages
- [ ] Reference related issues
- [ ] Test before committing
- [ ] Don't commit secrets or sensitive data
- [ ] Keep commits small and focused
- [ ] Use imperative mood in subjects
- [ ] Explain "why" in the body

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
