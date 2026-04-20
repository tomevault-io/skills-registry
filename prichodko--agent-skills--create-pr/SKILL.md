---
name: create-pr
description: Create GitHub PR with auto-generated description from commits and diff. Use when user wants to open a pull request or needs PR description. Use when this capability is needed.
metadata:
  author: prichodko
---

# Create PR

Generate PR description from commits/diff, confirm with user, then create PR.

## Workflow

### 1. Pre-flight checks

```bash
# ensure not on main/master
git branch --show-current
```

Abort if on `main` or `master`.

### 2. Detect base branch

```bash
# try local detection first (faster)
git branch -l main master 2>/dev/null | head -1 | xargs
```

Fallback if neither exists:
```bash
git remote show origin | grep "HEAD branch" | cut -d: -f2 | xargs
```

### 3. Check if pushed

```bash
git status -sb
```

If "ahead", need to push first:
```bash
git push -u origin HEAD
```

### 4. Check for PR template

```bash
cat .github/PULL_REQUEST_TEMPLATE.md 2>/dev/null
```

Use template structure if exists.

### 5. Gather context

```bash
# commits since base
git log --oneline <base>..HEAD

# full diff
git diff <base>...HEAD

# changed files
git diff --name-only <base>...HEAD
```

Read key changed files if helpful for understanding context.

### 6. Analyze and draft

- extract intent from commit messages
- group changes: feat/fix/refactor/docs/test
- note breaking changes (look for `BREAKING:` or `!:`)
- link issues mentioned (`#123`, `fixes #123`)

### 7. Draft PR

```markdown
## Summary
<1-3 bullets, what and why>

## Changes
<grouped list>

## Testing
<how to verify, if applicable>
```

### 8. Confirm with user

Present:
- suggested title
- full body

Wait for approval or edits. Do NOT proceed without confirmation.

### 9. Create PR

```bash
gh pr create --title "<title>" --body "$(cat <<'EOF'
<body>
EOF
)"
```

Return PR URL to user.

## Notes

- never create PR without user confirmation
- conventional commits → conventional PR title
- keep summary focused on "why", changes on "what"
- if PR template exists, adapt to its structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/prichodko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
