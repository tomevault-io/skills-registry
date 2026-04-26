---
name: branch-completion-workflow
description: Branch completion workflow done Use when this capability is needed.
metadata:
  author: jrc1883
---

# Finishing a Development Branch

## Overview

Guide completion of development work by presenting clear options and handling chosen workflow.

**Core principle:** Verify tests → Present options → Execute choice → Clean up.

**Announce at start:** "I'm using the finishing-a-development-branch skill to complete this work."

## The Process

### Step 1: Verify Tests

Before presenting options, run project test suite:

```bash
npm test / cargo test / pytest / go test ./...
```

**If tests fail:** Show failures and stop. Cannot proceed until tests pass.

**If tests pass:** Continue to Step 2.

### Step 2: Determine Base Branch

```bash
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

Or ask: "This branch split from main - is that correct?"

### Step 3: Present Options

### Step 3a: Outside Voice Only For PR-Ready

Do not block the whole finish-branch flow on outside-voice review. It matters when the user is creating a PR and especially when they want to move a draft PR to ready.

Do not ask about outside voice before presenting the four completion options. First let the user choose merge locally, create PR, keep as-is, or discard.

Use standalone outside-voice review only when the user explicitly wants a second opinion before creating the PR:

```bash
python packages/popkit-ops/skills/pop-cross-model-review/scripts/run_review.py
```

When the user is taking the PR path, prefer creating or keeping a draft PR until they decide it should become ready.

When the user wants to mark a draft PR ready from this flow, always use the executable helper instead of asking them to manually verify review state:

```bash
python packages/popkit-ops/skills/pop-cross-model-review/scripts/pr_ready.py --pr <number>
```

If the current head has no advisory artifact yet, either run the review automatically:

```bash
python packages/popkit-ops/skills/pop-cross-model-review/scripts/pr_ready.py --pr <number> --run-review-if-missing --publish comment
```

or record an explicit skip:

```bash
python packages/popkit-ops/skills/pop-cross-model-review/scripts/pr_ready.py --pr <number> --skip-outside-voice
```

### Step 3b: Present Options

**ALWAYS use AskUserQuestion** - never present plain text numbered options:

```
Use AskUserQuestion tool with:
- question: "Implementation complete. What would you like to do?"
- header: "Complete"
- options:
  - label: "Merge locally"
    description: "Merge back to <base-branch> and clean up"
  - label: "Create PR"
    description: "Push and create a Pull Request for review"
  - label: "Keep as-is"
    description: "Keep the branch, I'll handle it later"
  - label: "Discard"
    description: "Delete this work permanently"
- multiSelect: false
```

### Step 4: Execute Choice

See `examples/merge-workflow.md` for detailed implementation of each option.

| Option            | Actions                                                                 |
| ----------------- | ----------------------------------------------------------------------- |
| **Merge locally** | Switch to base → Pull → Merge → Test → Delete branch → Cleanup worktree |
| **Create PR**     | Push branch → Create PR with gh → Cleanup worktree                      |
| **Keep as-is**    | Report preservation → Keep worktree                                     |
| **Discard**       | Confirm → Delete branch (force) → Cleanup worktree                      |

**Worktree cleanup** (Options 1, 2, 4 only):

```bash
git worktree list | grep $(git branch --show-current)
git worktree remove <worktree-path>  # if found
```

### Step 5: Issue Close & Continue

**Only when invoked via `/popkit:dev work #N`** - skip for standalone use.

#### 5a: Close Prompt

After merge (Option 1) or PR (Option 2):

```
Use AskUserQuestion tool with:
- question: "Work on issue #N complete. Close the issue?"
- header: "Close Issue"
- options:
  - label: "Yes, close it"
    description: "Mark issue as completed"
  - label: "No, keep open"
    description: "Issue needs more work or follow-up"
```

If "Yes": `gh issue close <number> --comment "Completed via /popkit:dev work #<number>"`

#### 5b: Epic Parent Check

Check for parent epic reference in issue body:

```bash
gh issue view <number> --json body --jq '.body' | grep -oE '(Part of|Parent:?) #[0-9]+'
```

If all children closed → Prompt to close epic.

#### 5c: Context-Aware Next Actions

See `examples/next-action-example.md` for detailed flow.

Generate dynamic options by fetching prioritized issues, sorting by priority/phase, and presenting top 3 + "session capture and exit".

## Quick Reference

| Option        | Merge | Push | Keep Worktree | Cleanup Branch | Close Prompt |
| ------------- | ----- | ---- | ------------- | -------------- | ------------ |
| Merge locally | ✓     | -    | -             | ✓              | ✓ (if issue) |
| Create PR     | -     | ✓    | ✓             | -              | ✓ (if issue) |
| Keep as-is    | -     | -    | ✓             | -              | -            |
| Discard       | -     | -    | -             | ✓ (force)      | -            |

## Red Flags

**Never:**

- Proceed with failing tests
- Merge without verifying tests on result
- Delete work without confirmation
- Force-push without explicit request

**Always:**

- Verify tests before offering options
- Present exactly 4 options via AskUserQuestion
- Get typed "discard" confirmation for Option 4
- Clean up worktree for Options 1 & 4 only

## Integration

**Called by:**

- **subagent-driven-development** (Step 7) - After all tasks complete
- **executing-plans** (Step 5) - After all batches complete

**Pairs with:**

- **using-git-worktrees** - Cleans up worktree created by that skill

<details>
<summary>Common Mistakes (Click to expand)</summary>

**Skipping test verification**

- Problem: Merge broken code, create failing PR
- Fix: Always verify tests before offering options

**Open-ended questions**

- Problem: "What should I do next?" → ambiguous
- Fix: Present exactly 4 structured options

**Automatic worktree cleanup**

- Problem: Remove worktree when might need it (Option 2, 3)
- Fix: Only cleanup for Options 1 and 4

**No confirmation for discard**

- Problem: Accidentally delete work
- Fix: Require typed "discard" confirmation

</details>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jrc1883) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
