---
name: git-commit
description: > Use when this capability is needed.
metadata:
  author: j03fr0st
---

# Git Commit with Conventional Commits

Create standardized, semantic git commits using the Conventional Commits specification. Analyze the actual diff to determine appropriate type, scope, and message.

## Conventional Commit Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Commit Types

| Type       | Purpose                        |
| ---------- | ------------------------------ |
| `feat`     | New feature                    |
| `fix`      | Bug fix                        |
| `docs`     | Documentation only             |
| `style`    | Formatting/style (no logic)    |
| `refactor` | Code refactor (no feature/fix) |
| `perf`     | Performance improvement        |
| `test`     | Add/update tests               |
| `build`    | Build system/dependencies      |
| `ci`       | CI/config changes              |
| `chore`    | Maintenance/misc               |
| `revert`   | Revert commit                  |

## Breaking Changes

```
# Exclamation mark after type/scope
feat!: remove deprecated endpoint

# BREAKING CHANGE footer
feat: allow config to extend other configs

BREAKING CHANGE: `extends` key behavior changed
```

## Workflow

### 1. Analyze Diff

```bash
# If files are staged, use staged diff
git diff --staged

# If nothing staged, use working tree diff
git diff

# Also check status
git status --porcelain
```

### 2. Stage Files (if needed)

Stage specific files rather than using `git add .` or `git add -A` to avoid accidentally including secrets or large binaries.

```bash
# Stage specific files
git add path/to/file1 path/to/file2

# Stage by pattern
git add *.test.*
git add src/components/*
```

Check for secrets before staging (.env, credentials.json, private keys). Committing secrets can expose them permanently in git history, even after deletion.

### 3. Generate Commit Message

Analyze the diff to determine:

- **Type**: What kind of change is this?
- **Scope**: What area/module is affected?
- **Description**: One-line summary of what changed (present tense, imperative mood, <72 chars)

### 4. Execute Commit

```bash
# Single line
git commit -m "<type>[scope]: <description>"

# Multi-line with body/footer
git commit -m "$(cat <<'EOF'
<type>[scope]: <description>

<optional body>

<optional footer>
EOF
)"
```

### Example

User has modified `src/auth/login.ts` and `src/auth/login.test.ts` to fix a password validation bug:

```bash
git add src/auth/login.ts src/auth/login.test.ts
git commit -m "$(cat <<'EOF'
fix(auth): validate password length before hashing

Previously passwords under 8 chars were hashed then rejected,
wasting cycles. Now validation happens first.

Closes #247
EOF
)"
```

## Best Practices

- One logical change per commit
- Present tense: "add" not "added"
- Imperative mood: "fix bug" not "fixes bug"
- Reference issues: `Closes #123`, `Refs #456`
- Keep description under 72 characters

## Git Safety Protocol

- Do not update git config -- the user's git identity and settings are intentional.
- Do not run destructive commands (`--force`, `--hard` reset) unless the user explicitly asks. These can permanently lose work that cannot be recovered.
- Do not skip hooks (`--no-verify`) unless the user asks. Hooks enforce project-specific quality checks (linting, tests, secrets scanning).
- Do not force push to main/master. This rewrites shared history and can break other developers' work.
- If a commit fails due to hooks, fix the issue and create a NEW commit. Using `--amend` after a failed commit would modify the previous (unrelated) commit, destroying its content.
- By default, omit "Co-authored-by" and "Generated with" footers. If the user or project conventions request AI attribution, add it.
- Do not use emojis in commit messages (they add noise and can cause tooling issues).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/j03fr0st) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
