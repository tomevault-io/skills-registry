---
name: git-commit
description: Generate git commit messages that strictly follow the Conventional Commits v1.0.0 specification. Use when writing, reviewing, or formatting commit messages. Use when this capability is needed.
metadata:
  author: wredzio
---

# Conventional Commit Skill

This skill generates git commit messages compliant with the
**Conventional Commits v1.0.0 specification**.

Specification:
https://www.conventionalcommits.org/en/v1.0.0/

---

## Purpose

Use this skill to ensure that all commit messages:

- follow a consistent, machine-readable format
- are suitable for automated changelogs and semantic versioning
- are clear, concise, and review-friendly

---

## Commit Message Format

```
<type>[optional scope][!]: <subject>

[optional body]

[optional footer(s)]
```

### Rules

- The header is **required**
- Each line must not exceed **100 characters**
- Scope is optional
- `!` indicates a **breaking change**

---

## Commit Types

- `feat` — introduce a new feature
- `fix` — fix a bug
- `docs` — documentation changes only
- `style` — formatting only, no logic change
- `refactor` — code restructuring without behavior change
- `perf` — performance improvement
- `test` — adding or correcting tests
- `build` — build system or dependency changes
- `ci` — CI configuration changes
- `chore` — maintenance tasks
- `revert` — revert a previous commit

---

## Subject Line Rules

- Use imperative, present tense (e.g. "Add", not "Added")
- Capitalize the first letter
- Do not end with a period
- Maximum 70 characters

---

## Body Guidelines (Optional)

- Explain **what** changed and **why**
- Use imperative mood and present tense
- Wrap lines at approximately 100 characters
- Do not describe implementation details unless necessary

---

## Footer Guidelines (Optional)

### Issue References

```
Fixes #123
Closes #456
Refs #789
```

### Breaking Changes

Breaking changes must be described in the footer:

```
BREAKING CHANGE: Description of the breaking change
```

---

## Examples

### Feature

```
feat(auth): add JWT authentication

Enable stateless authentication using JSON Web Tokens.
Closes #42
```

### Fix

```
fix(api): handle null user response

Prevent server crash when user record is missing.
Fixes #101
```

### Refactor

```
refactor(validation): extract shared email validator

Move duplicated validation logic into a reusable helper.
No behavior change.
```

### Breaking Change

```
feat(api)!: remove legacy endpoints

BREAKING CHANGE: Legacy API endpoints have been removed.
Clients must migrate to API v2.
```

---

## Principles

- One logical change per commit
- Each commit must be buildable and reviewable
- Commit history should clearly communicate intent

---

End of skill.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wredzio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
