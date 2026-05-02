---
name: commit
description: This skill should be used when the user asks to "commit", "create a commit", "write a commit message", "commit my changes", "commit the changes", or needs help crafting a well-structured git commit message following best practices. Use when this capability is needed.
metadata:
  author: eabay
---

# Commit Skill

Create well-crafted git commit messages following established best practices.

## Purpose

Generate commit messages that effectively communicate context about changes to fellow developers and future maintainers. A diff shows _what_ changed; the commit message explains _why_.

## Workflow

### 1. Analyze Changes

Run these commands to understand the current state:

```bash
git status
git diff --staged
git diff
git log --oneline -5
```

Review:
- What files are modified, added, or deleted
- The nature of the changes (bug fix, feature, refactor, docs, etc.)
- Recent commit message style in the repository

### 2. Stage Appropriate Files

**Critical**: Only commit files modified during the current session. Never use `git add -A` or `git add .` unless explicitly instructed.

```bash
git add <specific-file-paths>
```

### 3. Craft the Commit Message

Apply the seven rules from `references/git-commit-guide.md`:

1. **Separate subject from body** with a blank line
2. **Limit subject to 50 characters** (72 hard limit)
3. **Capitalize the subject line**
4. **No period** at end of subject
5. **Use imperative mood** in subject ("Add feature" not "Added feature")
6. **Wrap body at 72 characters**
7. **Explain what and why**, not how

#### Subject Line Test

A proper subject completes this sentence:
> If applied, this commit will _[your subject line]_

Examples:
- "Add user authentication middleware" ✓
- "Fix race condition in connection pool" ✓
- "Added new feature" ✗ (not imperative)
- "Fixing bug" ✗ (not imperative)

### 4. Commit Format

For simple changes (no body needed):
```bash
git commit -m "Fix typo in README installation section"
```

For changes requiring explanation:
```bash
git commit -m "$(cat <<'EOF'
Refactor database connection handling

The previous implementation created a new connection for each query,
causing performance issues under load. This change introduces connection
pooling with a configurable pool size.

- Add ConnectionPool class with acquire/release methods
- Update DatabaseClient to use pool instead of direct connections
- Add pool_size configuration option (default: 10)

Resolves: #234
EOF
)"
```

### 5. Verify

After committing:
```bash
git log -1
git status
```

## Quick Reference

| Rule | Good | Bad |
|------|------|-----|
| Imperative | "Add tests" | "Added tests" |
| Capitalized | "Fix bug" | "fix bug" |
| No period | "Update docs" | "Update docs." |
| Length | "Add user auth" (13) | "Add user authentication system with JWT tokens and refresh capability" (71) |

## Additional Resources

For detailed guidelines and examples, consult:
- **`references/git-commit-guide.md`** - Complete guide with the seven rules explained in depth, examples of good and bad commits, and references to authoritative sources

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eabay) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
