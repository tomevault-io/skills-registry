---
name: agent-orchestration
description: End-to-end project orchestration from idea to implementation. Use when user wants to build a complete project, implement an MVP, or says "build this app/feature". Coordinates product-design, architecture, task-decomposition skills, then delegates to task-spec-generator agent, task-implementation skill, and code-reviewer agent for execution. Resumable—introspects filesystem and git state to pick up where it left off if interrupted. Use when this capability is needed.
metadata:
  author: porkbutts
---

# Agent Orchestration

Coordinate the full development lifecycle: requirements → architecture → tasks → implementation → review → merge.

## Prerequisites Check

Before starting, verify which artifacts exist:

| Artifact | Path | If Missing |
|----------|------|------------|
| PRD | `docs/PRD.md` | Invoke `/product-design` skill |
| Architecture | `docs/ARCHITECTURE.md` | Invoke `/architecture` skill |
| Brand Guidelines | `docs/BRAND-GUIDELINES.md` | Invoke `/frontend-design` skill |
| Tasks | `docs/TASKS.md` | Invoke `/task-decomposition` skill |

Invoke each missing skill in order. After Phase 1 is complete, clear context before proceeding to Phase 2.

## Resume Detection

On startup, introspect state to determine where to resume. `docs/TASKS.md` is the sole source of truth for progress. If any partial work exists (worktrees, unmerged task branches), clean it up before proceeding.

### Check sequence:

```bash
# 1. Check planning artifacts
ls docs/PRD.md docs/ARCHITECTURE.md docs/BRAND-GUIDELINES.md docs/TASKS.md 2>/dev/null

# 2. Check task specs
ls docs/tasks/task-*.md 2>/dev/null

# 3. Clean up any leftover worktrees and branches from interrupted runs
git worktree list | grep '.worktrees/task-'
git branch --list 'task/*'

# 4. Parse TASKS.md for progress
grep -E '^\s*- \[(x| )\]' docs/TASKS.md
```

### Cleanup:

Remove any worktrees and branches from incomplete tasks before resuming. Only tasks marked `[x]` in `docs/TASKS.md` are considered done—everything else starts fresh.

### Resume logic:

| State | Resume Point |
|-------|--------------|
| No `docs/PRD.md` | Start Phase 1 |
| No `docs/ARCHITECTURE.md` | Phase 1: architecture skill |
| No `docs/BRAND-GUIDELINES.md` | Phase 1: frontend-design skill |
| No `docs/TASKS.md` | Phase 1: task-decomposition skill |
| No `docs/tasks/*.md` | Start Phase 2 |
| All tasks `[x]` | Complete—nothing to do |
| Otherwise | Resume Phase 3 from first unchecked task |

Report detected state to user before proceeding:
```
Detected state:
- Planning: Complete (PRD, Architecture, Brand Guidelines, Tasks exist)
- Task specs: 12 generated
- Progress: 5/12 tasks complete
- Resuming from: task-2.1.3
```

### Merge Mode

Before starting Phase 3, ask the user to choose a merge mode:

- **Full auto**: After code review passes, automatically merge the PR, clean up the worktree/branch, and continue. QA runs after all tasks in a story are merged.
- **Manual**: After code review passes, stop and wait for the user to merge the PR themselves before continuing. QA still runs automatically after all tasks in the story are merged.

Remember the chosen mode for the duration of the run. Default to asking if not previously set (including on resume).

## Workflow

```
Phase 0: Resume Detection & Configuration
  → Introspect files, worktrees, branches to find resume point
  → Ask user for merge mode (full auto / manual)

Phase 1: Planning
  → PRD → Architecture → Brand Guidelines → Tasks (invoke skills as needed)

Phase 2: Task Spec Generation
  → Launch task-spec-generator → creates docs/tasks/task-<id>.md

Phase 3: Implementation Loop
  For each story:
    For each task in the story (in dependency order):
      1. Determine task doc from TASKS.md
      2. Launch task-implementer agent → creates worktree, tests, implements, opens PR
      3. Launch code-reviewer agent
      4. If REQUEST CHANGES → re-launch task-implementer, re-review (up to 3 cycles)
      5. Merge PR, cleanup worktree and branch
    After all tasks in the story are merged:
      6. Launch qa-tester agent against main branch deployment
      7. If QA fails → create fix task, implement, review, merge, re-QA

Phase 4: Completion
  → All tasks done, optionally tag release
```

## Phase 2: Task Spec Generation

Launch the **task-spec-generator** agent to create individual task files:

```
Task agent: task-spec-generator
Prompt: "Generate task specs from docs/TASKS.md"
```

This creates `docs/tasks/task-<id>.md` for each task (e.g., `task-1.1.1.md`, `task-2.3.2.md`).

## Phase 3: Implementation Loop

**IMPORTANT**: For the implementation loop, use subagents not skills!

Process stories in order. Within each story, implement all tasks, then QA the story as a whole.

### For each story:

#### Task Loop (repeat for each task in the story)

##### Step 1: Determine Task Doc

Read the next unchecked task in this story from `docs/TASKS.md` (in dependency order). Locate its spec at `docs/tasks/task-<id>.md`.

##### Step 2: Implement

Launch the **task-implementer** agent:
```
Task agent: task-implementer
Prompt: "Implement task <id> from docs/tasks/task-<id>.md"
```

The agent creates a worktree, writes tests, implements, and opens a PR.

##### Step 3: Code Review

Launch the **code-reviewer** agent on the PR:
```
Task agent: code-reviewer
Prompt: "Review PR #<number> against docs/tasks/task-<id>.md"
```

##### Step 4: Address Feedback (if REQUEST CHANGES)

If the reviewer requests changes, re-launch **task-implementer** — the Review Feedback section in the task spec tells it what to fix:
```
Task agent: task-implementer
Prompt: "Address review feedback for task <id>"
```

Then re-run Step 3. Repeat up to 3 cycles — escalate to user after that.

##### Step 5: Merge & Cleanup

Once code review passes, wait for all CI checks to pass before merging:

```bash
# Wait for CI checks to complete and pass (times out after 10 minutes)
gh pr checks <number> --watch --fail-fast
```

If checks fail, inspect the failures, fix the issues (re-launch task-implementer if needed), and wait for checks again. Do not merge until all checks pass.

- **Full auto mode**: After checks pass, merge the PR (`gh pr merge --squash`), remove the worktree (`git worktree remove .worktrees/<branch>`), delete the branch (`git branch -d <branch>`), and continue to the next task in the story.
- **Manual mode**: After checks pass, notify the user that the PR is ready to merge. Wait for the user to confirm they've merged before cleaning up and continuing.

The task is already marked `[x]` in `docs/TASKS.md` by the task-implementer as part of the PR.

#### Story QA (after all tasks in the story are merged)

##### Step 6: QA Verification

Once all tasks in the story are merged to main, run QA against the full story. QA tests the user flow end-to-end — individual tasks don't have enough context for meaningful QA.

Wait for the deployment to be ready, then launch the **qa-tester** agent:
```bash
# Wait for main branch deployment to be ready
# Use production/staging URL or Vercel deployment for main
```
```
Task agent: qa-tester
Prompt: "Verify story <story-id> acceptance criteria against <deployment-url>. Test the complete user flow: <summarize the story's user flow from TASKS.md>"
```

The QA prompt must include the story's acceptance criteria and describe the user flow to test, since there is no single task doc to reference.

##### Step 7: Handle QA Failure

If QA fails, create a fix task scoped to the QA findings:

1. Create a new task spec at `docs/tasks/task-<story-id>-fix.md` with the QA report findings as acceptance criteria
2. Run the task through the standard task loop (Steps 2-5: implement → review → merge)
3. Re-run Story QA (Step 6)
4. If QA fails again after 2 fix cycles, escalate to user

### Parallel Execution

Tasks within a story MAY run in parallel if:
- No dependencies between them (check Dependencies table in TASKS.md)
- They don't modify the same files

Launch multiple task-implementer agents simultaneously for independent tasks. Each operates in its own worktree.

**Story QA runs after all tasks in the story are merged**, so parallel task execution does not affect QA scheduling.

**Merge conflict handling:** If conflicts occur during merge:
1. Resolve conflicts manually
2. Run tests to verify resolution
3. Complete the merge

## Phase 4: Completion

When all tasks are merged:
1. Verify acceptance criteria in PRD are met
2. Optionally create a release tag

## Error Handling

| Situation | Action |
|-----------|--------|
| Task-implementer blocked | Document blocker in task-spec, skip to next task, return later |
| Tests won't pass after 3 review cycles | Escalate to user for manual intervention |
| Merge conflicts unresolvable | Pause parallel execution, resolve sequentially |
| Missing dependency | Implement dependency first, then resume |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/porkbutts) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
