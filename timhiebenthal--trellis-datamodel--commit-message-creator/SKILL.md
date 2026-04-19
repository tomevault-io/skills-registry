---
name: commit-message-creator
description: This skill ensures all git commit messages follow the Conventional Commits specification. Use when this capability is needed.
metadata:
  author: timhiebenthal
---


# Commit Message Creator

## Instructions
When I ask you to commit changes or write a commit message, you must analyze the staged changes (diff) and categorize the work into one of the following types:

- **feat**: A new feature for the user, not a new feature for builds or internal components.
- **fix**: A bug fix for the user, not a fix to a build script.
- **docs**: Changes to the documentation.
- **style**: Formatting, missing semi colons, etc; no production code change.
- **refactor**: Refactoring production code, eg. renaming a variable.
- **test**: Adding missing tests, refactoring tests; no production code change.
- **chore**: Updating grunt tasks etc; no production code change.

## Rules
1. **Format**: `<type>(<optional scope>): <description>`
2. **Style**: Use the imperative, present tense: "change" not "changed" nor "changes".
3. **Case**: The description should be lowercase and should not end with a period.
4. **Scope**: If the change is specific to a module or package, include it in parentheses, e.g., `feat(auth): add login logic`.

## Example
- `feat: add password strength meter`
- `fix(ui): resolve alignment issue on mobile`
- `chore: update dependencies`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/timhiebenthal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
