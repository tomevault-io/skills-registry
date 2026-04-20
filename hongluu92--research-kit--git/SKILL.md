---
name: git
description: Standards for version control, commit messages, and branching strategy Use when this capability is needed.
metadata:
  author: hongluu92
---

# Git - Version Control Standards

> **CRITICAL SKILL** - Maintain a clean, traversable, and semantic history.

---

## Core Principles

| Principle | Rule |
|-----------|------|
| **Atomic Commits** | One logical change per commit (e.g., "Fix bug" vs "Fix bug and add feature") |
| **Conventional Commits** | Use structured messages (feat, fix, docs, refactor, etc.) |
| **Clean History** | Don't commit broken code. Squash WIP commits before merging. |
| **Ignore Artifacts** | Never commit derived files (builds, local configs, secrets). |

---

## Commit Message Convention

Follow the **Conventional Commits** specification:

```text
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

| Type | Description |
|------|-------------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation only changes |
| `style` | Changes that do not affect the meaning of the code (white-space, formatting, etc) |
| `refactor` | A code change that neither fixes a bug nor adds a feature |
| `perf` | A code change that improves performance |
| `test` | Adding missing tests or correcting existing tests |
| `chore` | Changes to the build process or auxiliary tools and libraries |

### Examples

**Good:**
- `feat(auth): implement jwt token validation`
- `fix(search): handle empty result sets gracefully`
- `docs(readme): update installation instructions`

**Bad:**
- `update code`
- `fix bug`
- `wip`

---

## Workflow Rules

1.  **Before Committing:**
    -   Check `git status` to see what will be staged.
    -   Run `git diff` to review changes line-by-line.
    -   Ensure sensitive data (API keys) is NOT included.

2.  **Branching (if applicable):**
    -   `main` / `master`: Stable production code.
    -   `develop` / `dev`: Integration branch.
    -   `feat/feature-name`: Feature branches.
    -   `fix/issue-description`: Bug fix branches.

3.  **Handling Conflicts:**
    -   Pull changes from remote often to minimize conflicts.
    -   Resolve conflicts manually, understanding both sides of the change.

---

## Common Commands Checklist

| Action | Command |
|--------|---------|
| **Check Status** | `git status` |
| **View Changes** | `git diff` |
| **Stage Files** | `git add <file>` (Avoid `git add .` unless sure) |
| **Commit** | `git commit -m "type(scope): message"` |
| **Log** | `git log --oneline --graph --decorate --all` |

---

## 🔴 Self-Check Before Committing

**Ask yourself:**
1.  Does this commit break the build?
2.  Did I accidentally include `node_modules`, `venv`, or `.env`?
3.  Is the commit message descriptive enough for someone else to understand 6 months from now?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hongluu92) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
