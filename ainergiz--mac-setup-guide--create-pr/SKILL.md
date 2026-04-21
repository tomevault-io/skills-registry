---
name: create-pr
description: Create a GitHub pull request with proper issue linking and conventional commits. Use when user says "create pr", "submit pr", "open pull request", "push and create pr", or after pre-pr review passes. Use when this capability is needed.
metadata:
  author: ainergiz
---

# Create Pull Request

Create a well-structured GitHub PR with proper issue linking.

## Workflow

```
Step 1: Commit Changes    → Review diff, stage, commit
Step 2: Push              → Push to origin
Step 3: Review PR Diff    → Verify what goes into PR
Step 4: Draft PR          → Generate title + body
Step 5: Confirm with User → AskUserQuestion
Step 6: Create PR         → gh pr create
```

## Step 1: Commit Changes

Review and commit any uncommitted changes:

```bash
git diff
git status --short
git add -A
git commit -m "type(scope): description"
```

**Conventional commit types:**

- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation only
- `refactor` - Code restructuring
- `test` - Adding/updating tests
- `chore` - Maintenance tasks

Keep subject line under 80 characters. If all changes are already committed, skip to Step 2.

## Step 2: Push to Origin

```bash
git push origin <branch-name>
```

If push fails (e.g., no upstream), set it:

```bash
git push -u origin <branch-name>
```

## Step 3: Review PR Diff

```bash
git diff origin/main...HEAD --stat
```

Present summary:

```
Files to be included in PR:
- file1.ts (+50, -10)
- file2.ts (+20, -5)

Total: X files changed, +Y insertions, -Z deletions
```

## Step 4: Draft PR Content

**Get issue context:**

```bash
git branch --show-current  # Extract issue number
gh issue view <number> --json title,body
```

**Generate PR title:**
Format: `type(scope): description`

- Keep under 80 characters
- Use conventional commit format

**Generate PR body:**

```markdown
## Summary

<1-2 sentences: what does this PR do and why>

## Related Issues

Closes #<issue-number>

## Changes

- Change 1
- Change 2
- Change 3

## Testing

- [ ] Tests pass locally
- [ ] Manual verification done
```

## Step 5: Confirm with User

Present the draft PR:

```
## Proposed Pull Request

**Title:** feat(auth): add OAuth2 login flow

**Body:**
<generated body>

Ready to create this PR?
```

Use AskUserQuestion:

- Options: ["Create PR", "Edit title", "Edit body", "Cancel"]

If user edits, update and re-confirm.

## Step 6: Create PR

```bash
gh pr create --base main --title "<title>" --body "<body>"
```

On success, display:

```
## PR Created ✓

#<pr-number>: <title>
<pr-url>

Issues linked: #<issue-number> (will close on merge)
```

## Issue Linking Reference

| Keyword         | Effect                     |
| --------------- | -------------------------- |
| `Closes #X`     | Auto-closes issue on merge |
| `Fixes #X`      | Auto-closes issue on merge |
| `Resolves #X`   | Auto-closes issue on merge |
| `Related to #X` | Links without auto-close   |
| `Part of #X`    | Links to parent/epic       |

## Error Handling

### If push fails

```
Push failed. Possible causes:
- No upstream branch set
- Remote has new commits

Options: ["Set upstream", "Pull and retry", "Ask user for help"]
```

### If PR creation fails

```
PR creation failed: <error>

Options: ["Retry", "Open in browser", "Ask user for help"]
```

## Notes

- Always include Related Issues section with proper linking
- Default to `Closes` for issue from branch name
- Keep description under 5 sentences unless user specifies otherwise
- If any step fails, ask user for help before proceeding

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ainergiz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
