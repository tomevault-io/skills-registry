---
name: commit-work
description: Create a properly formatted git commit for ECSdb work Use when this capability is needed.
metadata:
  author: cameron5906
---

# Commit Work

Create a properly formatted commit for the ECSdb project.

## Arguments
- `$0` = commit type (feat, test, fix, docs, refactor, chore)
- `$1` = ticket ID (e.g., 02-004)
- `$2` = commit message description

## Pre-Commit Checklist

Before committing, verify:

```bash
# 1. Run tests
cargo test

# 2. Run clippy
cargo clippy

# 3. Check formatting
cargo fmt --check
```

If ANY of these fail, DO NOT COMMIT. Fix the issues first.

## Commit Format

```bash
git add -A
git commit -m "$(cat <<'EOF'
$0: $2 [TICKET-$1]

<detailed description if needed>
EOF
)"
```

## Commit Types

| Type | When to Use |
|------|-------------|
| `feat` | New feature implementation |
| `test` | Adding or updating tests |
| `fix` | Bug fixes |
| `docs` | Documentation updates |
| `refactor` | Code restructuring without behavior change |
| `chore` | Build, dependencies, tooling |

## Examples

**Test commit:**
```bash
/commit-work test 02-004 "add ComponentData serialization tests"
```
Results in:
```
test: add ComponentData serialization tests [TICKET-02-004]
```

**Feature commit:**
```bash
/commit-work feat 02-011 "implement fjall StorageEngine adapter"
```
Results in:
```
feat: implement fjall StorageEngine adapter [TICKET-02-011]
```

**Documentation commit:**
```bash
/commit-work docs 02-004 "mark ticket complete in sprint README"
```
Results in:
```
docs: mark ticket complete in sprint README [TICKET-02-004]
```

## Post-Commit

After committing, show the git log:
```bash
git log --oneline -3
```

## Remember

- Small, focused commits
- One logical change per commit
- Never commit with failing tests
- Always include ticket ID for traceability

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cameron5906) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
