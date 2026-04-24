---
name: commit
description: Generate Conventional Commit messages from staged changes. Use when committing code, generating commit messages, or when the user asks for a commit. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# Commit Message Skill

> **Purpose:** Generate consistent Conventional Commit messages from staged changes.

## Trigger

**When:** User stages files and requests commit
**Context Needed:** `git diff --staged`, file paths
**MCP Tools:** `mcp_gitkraken_git_add_or_commit`

## Format

```
type(scope): description
```

## Types

| Change        | Type       |
| :------------ | :--------- |
| New feature   | `feat`     |
| Bug fix       | `fix`      |
| Documentation | `docs`     |
| Refactor      | `refactor` |
| Tests         | `test`     |
| Build/deps    | `chore`    |

## Scope Detection

| Path          | Scope  |
| :------------ | :----- |
| `apps/api/**` | `api`  |
| `apps/web/**` | `web`  |
| `prisma/**`   | `db`   |
| `docs/**`     | `docs` |
| `libs/ui/**`  | `ui`   |

## Examples

```
feat(api): add payment webhook endpoint
fix(web): resolve signal update in product list
docs(api): update authentication API documentation
chore(db): add index for merchant lookup
```

## Reference

- [TOOLING-STYLE-GUIDE.md](/docs/process/standards/TOOLING-STYLE-GUIDE.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
