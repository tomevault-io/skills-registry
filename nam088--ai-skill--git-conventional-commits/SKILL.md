---
name: git-conventional-commits
description: Guide for writing commit messages that follow the Conventional Commits specification. Use when committing code to ensure messages are parsed correctly by commitlint. Use when this capability is needed.
metadata:
  author: nam088
---

# Conventional Commits Skill

## When to use
- When the user asks to "commit these changes".
- When crafting commit messages for `git commit`.

## Format structure
```text
<type>(<scope>): <subject>

<body>

<footer>
```

## Rules
1. **Type**: Must be one of:
   - `feat`: New feature
   - `fix`: Bug fix
   - `docs`: Documentation only
   - `style`: Formatting, missing semi-colons, etc (no code change)
   - `refactor`: Refactoring production code
   - `test`: Adding tests, refactoring tests
   - `chore`: Update build scripts, no production code change
2. **Scope**: Optional, but recommended (e.g., `deps`, `api`, `auth`). Surounded by parenthesis.
3. **Subject**:
   - Use imperative, present tense: "change" not "changed" nor "changes".
   - No dot (.) at the end.
   - Lowercase.
4. **Body**: Motivation for the change and contrast with previous behavior.
5. **Footer**:
   - `BREAKING CHANGE: <description>`
   - `Closes #123`

## Example
```text
feat(auth): add google oauth2 login support

- implement strategy in auth.service
- add callback route
- update user schema

Closes #42
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nam088) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
