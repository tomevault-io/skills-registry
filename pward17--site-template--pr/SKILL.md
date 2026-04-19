---
name: pr
description: Create and manage pull requests with gh CLI Use when this capability is needed.
metadata:
  author: pward17
---

# PR Skill

Create and manage GitHub pull requests using the `gh` CLI.

## Usage

```
/pr [command]
```

**Commands:**
- `/pr` or `/pr create` - Create a pull request
- `/pr check` - Show status of current PR checks
- `/pr view` - View current PR details

## Prerequisites

- `gh` CLI installed and authenticated (`gh auth status`)
- Current branch pushed to remote

## Commands

### `/pr` or `/pr create`

Create a pull request for the current branch.

**Step 1: Gather context**

Run these in parallel to understand the current state:

```bash
git status
git diff
git log --oneline -10
git branch --show-current
```

Determine the base branch (usually `main`):

```bash
git diff main...HEAD --stat
```

**Step 2: Generate PR content**

- **Title:** Short (under 70 chars), derived from branch name or commit messages
- **Body:** Summary of changes + test plan

**Step 3: Push and create PR**

Push to remote if needed:

```bash
git push -u origin $(git branch --show-current)
```

Create the PR:

```bash
gh pr create --title "the pr title" --body "$(cat <<'EOF'
## Summary
<1-3 bullet points summarizing the changes>

## Test plan
- [ ] <testing steps>

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

**Step 4: Return the PR URL** to the user.

### `/pr check`

Show the status of checks on the current PR:

```bash
gh pr checks
```

### `/pr view`

View details of the current PR:

```bash
gh pr view
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pward17) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
