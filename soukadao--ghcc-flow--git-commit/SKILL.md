---
name: git-commit
description: | Use when this capability is needed.
metadata:
  author: soukadao
---

# Git Commit

Stage source code changes and create commits following strict commit message format rules.

## Commit Message Format

**Required format:** `<prefix>: <message>`

**Rules:**
- Prefix must be lowercase
- Message must start with lowercase letter
- Prefix and message separated by colon and space

**Valid prefixes:**
- `build`
- `chore`
- `ci`
- `docs`
- `feat`
- `fix`
- `perf`
- `refactor`
- `revert`
- `style`
- `test`

## Examples

**Valid:**
```bash
fix: some message
feat: add new feature
docs: update readme
```

**Invalid:**
```bash
Fix: some message       # prefix not lowercase
fix: SomeMessage        # message not lowercase
foo: some message       # invalid prefix
fix:some message        # missing space after colon
: some message          # prefix empty
fix:                    # message empty
```

## Workflow

### 1. Check current status
```bash
git status
```

### 2. Stage files
```bash
# Stage specific files
git add <file1> <file2>

# Stage all changes
git add .
```

### 3. Create commit
```bash
git commit -m "<prefix>: <message>"
```

## Tips

- **Small commits:** Stage related files in small chunks after completing a task
- **Clear messages:** Be specific about what changed and why
- **Choose correct prefix:** Match the prefix to the type of change
- **Validate first:** Always validate commit message format before committing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/soukadao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
