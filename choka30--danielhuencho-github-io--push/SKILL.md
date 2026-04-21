---
name: push
description: Push committed changes to remote repository. Runs pre-push validation including tests and brain sync check. Use after commits are ready for remote. Use when this capability is needed.
metadata:
  author: choka30
---

# Push

Push committed changes to remote repository with validation.

## Pre-Push Checklist

Before pushing, verify:
- [ ] All tests passing
- [ ] Brain files synced and committed
- [ ] No uncommitted changes
- [ ] Branch is up to date with remote

## Execution Protocol

### Step 1: Validate State

```bash
# Check for uncommitted changes
if [ -n "$(git status --porcelain)" ]; then
    echo "⚠️  Uncommitted changes detected"
    echo "Run /commit first or stash changes"
    exit 1
fi

# Get current branch
BRANCH=$(git branch --show-current)
echo "Branch: ${BRANCH}"
```

### Step 2: Run Tests

```bash
poetry run pytest tests/ -v --tb=short
```

If tests fail → **STOP** and fix before pushing.

### Step 3: Check Remote Status

```bash
# Fetch latest from remote
git fetch origin

# Check if behind
BEHIND=$(git rev-list --count HEAD..origin/${BRANCH} 2>/dev/null || echo "0")

if [ "${BEHIND}" -gt 0 ]; then
    echo "⚠️  Branch is ${BEHIND} commits behind origin/${BRANCH}"
    echo "Consider: git pull --rebase origin ${BRANCH}"
fi
```

### Step 4: Confirm Push

```
═══════════════════════════════════════════════════════
  PUSH CONFIRMATION
═══════════════════════════════════════════════════════

Branch:          feature/auth
Remote:          origin
Commits to push: 3

Commits:
  abc1234 feat(auth): add JWT validation
  def5678 test(auth): add token tests  
  ghi9012 docs(brain): update codebase index

Tests: ✓ All passing
Behind remote: 0 commits

Proceed with push? (yes/no)
```

### Step 5: Execute Push

```bash
git push origin ${BRANCH}
```

### Step 6: Verify

```bash
git log origin/${BRANCH} -1 --oneline
```

## Output Format

### Success
```
═══════════════════════════════════════════════════════
  ✓ PUSHED TO REMOTE
═══════════════════════════════════════════════════════

Branch:   feature/auth → origin/feature/auth
Commits:  3 pushed
Latest:   ghi9012 docs(brain): update codebase index

Remote URL: git@github.com:user/repo.git

Next steps:
  - Create PR if ready for review
  - Continue development with /session-start
  - Merge with /orchestrator-merge (if using worktrees)
```

### Failure
```
═══════════════════════════════════════════════════════
  ✗ PUSH FAILED
═══════════════════════════════════════════════════════

Error: [git error message]

Common fixes:
  - Rejected (non-fast-forward): git pull --rebase origin ${BRANCH}
  - Permission denied: Check SSH keys or credentials
  - Branch protection: Create PR instead of direct push
```

## Force Push (Use with Caution)

Only when you know what you're doing:

```bash
# After rebase or amend
git push origin ${BRANCH} --force-with-lease
```

**Never force push to:**
- `main` or `master`
- Shared branches with other developers

## Branch Protection Awareness

If pushing to protected branch fails:
1. Create a pull request instead
2. Or push to feature branch first

```bash
# Create PR via GitHub CLI (if available)
gh pr create --title "Feature: Auth" --body "Implements JWT validation"
```

## Worktree Considerations

When using worktrees:
- Push from worktree branch, not main
- Main branch updates happen via `/orchestrator-merge`
- Each worktree pushes independently

## Notes

- Always run tests before push
- Fetch before push to avoid conflicts
- Use `--force-with-lease` instead of `--force`
- Consider PR workflow for team projects

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choka30) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
