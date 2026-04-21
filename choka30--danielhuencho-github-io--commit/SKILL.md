---
name: commit
description: Create atomic, traceable git commits following conventional commit standard. Links commits to plan tasks. Use after task completion and brain sync. Use when this capability is needed.
metadata:
  author: choka30
---

# Commit

Create atomic git commits with conventional commit format.

## Pre-Commit Checklist

Before committing, verify:
- [ ] Tests passing (`/test affected`)
- [ ] Brain files updated (`/update-brain`)
- [ ] Changes are atomic (single task/purpose)
- [ ] No unintended files staged

## Execution Protocol

### Step 1: Review Changes
```bash
git status
git diff --staged
```

### Step 2: Stage Changes
```bash
# Stage specific files (preferred)
git add src/module/file.py tests/test_file.py brain/

# Or stage all (use carefully)
git add -A
```

### Step 3: Generate Commit Message

**Format:**
```
<type>(<scope>): <description>

- [Specific change 1]
- [Specific change 2]

Refs: brain/plan.md#task-N
```

**Commit Types:**

| Type | Use Case | Example |
|------|----------|---------|
| `feat` | New feature | `feat(auth): add JWT token validation` |
| `fix` | Bug fix | `fix(parser): handle empty input gracefully` |
| `refactor` | Code restructure | `refactor(utils): extract validation logic` |
| `test` | Adding/updating tests | `test(auth): add token expiry tests` |
| `docs` | Documentation | `docs(readme): update installation steps` |
| `chore` | Maintenance | `chore(deps): update pytest to 8.0` |

**Scope:** Module or component name (e.g., `auth`, `parser`, `api`, `models`)

### Step 4: Commit
```bash
git commit -m "<type>(<scope>): <description>" -m "- Change 1
- Change 2

Refs: brain/plan.md#task-N"
```

### Step 5: Verify
```bash
git log -1 --oneline
```

## Output Format

```
═══════════════════════════════════════════════════════
  ✓ COMMITTED
═══════════════════════════════════════════════════════

Hash:   abc1234
Type:   feat
Scope:  auth
Files:  3 changed, +45, -12

Message:
  feat(auth): add JWT token validation
  
  - Add validate_token() function
  - Add test_validate_token_expired test
  
  Refs: brain/plan.md#task-1

Branch: feature/auth
```

## Rules

1. **One task = One commit** — Don't bundle multiple tasks
2. **Always reference plan** — Include `Refs: brain/plan.md#task-N`
3. **Meaningful messages** — Future you should understand why
4. **No broken commits** — All tests must pass before commit

## Undo Last Commit (if needed)

```bash
# Undo commit, keep changes staged
git reset --soft HEAD~1

# Undo commit, unstage changes
git reset HEAD~1

# Completely discard (DANGEROUS)
git reset --hard HEAD~1
```

## Multi-File Commit Example

```bash
git add src/core/auth.py
git add tests/unit/test_auth.py
git add brain/plan.md brain/codebase_index.md

git commit -m "feat(auth): implement token validation

- Add validate_token() with expiry check
- Add test coverage for valid/expired/malformed tokens
- Update codebase index with new function

Refs: brain/plan.md#task-1"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choka30) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
