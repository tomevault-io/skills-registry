---
name: finishing-a-development-branch
description: Use when implementation is complete and all tests pass, to choose and execute the integration path: merge locally, push PR, keep as-is, or discard.
metadata:
  author: jbro
---

# Finishing a Development Branch

## Overview

Guide completion of development work by presenting clear options and handling the chosen workflow.

**Core principle:** Verify tests → Present options → Execute choice → Clean up.

**Announce at start:** "I'm using the finishing-a-development-branch skill to complete this work."

## The Process

### Step 1: Verify Tests

```bash
npm test / cargo test / pytest / go test ./...
```

If tests fail — show failures, stop. Do not proceed to Step 2.

### Step 2: Determine Base Branch

```bash
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

Or ask: "This branch split from main — is that correct?"

### Step 3: Present Options

For polished user-facing status/PR text, use the `writing-clearly-and-concisely` skill.

```
Implementation complete. What would you like to do?

1. Merge back to <base-branch> locally
2. Push and create a Pull Request
3. Keep the branch as-is (I'll handle it later)
4. Discard this work

Which option?
```

### Step 4: Execute Choice

#### Option 1: Merge Locally

```bash
git checkout <base-branch>
git pull
git merge <feature-branch>
<test command>          # re-verify on merged result
git branch -d <feature-branch>
```

Then: Step 5 (cleanup worktree).

#### Option 2: Push and Create PR

```bash
git push -u origin <feature-branch>
gh pr create --title "<title>" --body "$(cat <<'EOF'
## Summary
<2-3 bullets of what changed>

## Test Plan
- [ ] <verification steps>
EOF
)"
```

Then: Step 5 (cleanup worktree).

#### Option 3: Keep As-Is

Report: "Keeping branch `<name>`. Worktree preserved at `<path>`." Do not clean up.

#### Option 4: Discard

**Confirm first:**

```
This will permanently delete:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path>

Type 'discard' to confirm.
```

Wait for exact word. If confirmed:

```bash
git checkout <base-branch>
git branch -D <feature-branch>
```

Then: Step 5 (cleanup worktree).

### Step 5: Cleanup Worktree (Options 1, 2, 4 only)

```bash
git worktree list | grep $(git branch --show-current)
git worktree remove <worktree-path>
```

## Quick Reference

| Option | Merge | Push | Keep Worktree | Delete Branch |
|--------|-------|------|---------------|---------------|
| 1. Merge locally | ✓ | — | — | ✓ |
| 2. Create PR | — | ✓ | ✓ | — |
| 3. Keep as-is | — | — | ✓ | — |
| 4. Discard | — | — | — | ✓ (force) |

## Rules

**Never:**
- Offer integration options before tests pass
- Merge without re-verifying tests on the merged result
- Delete work without typed "discard" confirmation
- Force-push without explicit request

**Always:**
- Verify tests → present exactly 4 options → get typed confirmation for discard → clean up worktree for options 1 & 4

## Integration

**Called by:** executing-plans (Step 5) after all batches complete

**Pairs with:** using-git-worktrees — cleans up the worktree created by that skill

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
