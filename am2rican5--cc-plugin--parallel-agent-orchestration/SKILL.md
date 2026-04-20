---
name: am2rican5parallel-agent-orchestration
description: Orchestrate multiple Claude agents working in parallel on a shared codebase with task locking, conflict avoidance, and autonomous work loops. Use when user says "run parallel agents", "orchestrate agents", "multi-agent workflow", "coordinate agents on shared codebase", "parallelize this work", or "run multiple agents simultaneously". Key capabilities include task decomposition, lock-file coordination, git worktree isolation, and branch-per-agent strategies. Do NOT use for single-agent task execution (use task-runner agent instead) or for learning about testing patterns (use test-driven-autonomous-dev instead). Use when this capability is needed.
metadata:
  author: am2rican5
---

# Parallel Agent Orchestration

## Critical Rules

- NEVER spawn parallel agents without explicit user confirmation of the work plan
- NEVER allow two agents to work on the same file simultaneously — verify file-level independence first
- ALWAYS set up a task-locking mechanism before launching parallel work
- ALWAYS verify task independence before parallelizing — if tasks share state, they must run sequentially
- WHEN a merge conflict occurs, STOP and ask the user how to resolve — do not auto-resolve
- WHEN an agent finishes, it MUST release its lock before picking up new work

## Instructions

### Step 1: Assess Task Decomposability

Analyze the work the user wants to parallelize:

1. List all subtasks or work items
2. For each pair of tasks, check: do they touch the same files? Do they depend on each other's output?
3. Classify each task as **independent** (can run in parallel) or **dependent** (must run after another task)
4. Present the dependency graph to the user for confirmation

IF all tasks touch the same files or share heavy state → recommend sequential execution and **stop**.
IF tasks are clearly independent → proceed to Step 2.

### Step 2: Set Up Task Locking Directory

Create a locking mechanism to prevent duplicate work:

1. Create a `_tasks/` directory in the working area (or use an existing task tracking system)
2. Create one file per task in `_tasks/available/` with the task description
3. Apply the lock-file protocol: agents claim tasks before starting, release on completion or failure. See `references/lock-protocol.md` for the full protocol (lock file format, claim-before-start, completion, failure, stale lock recovery).

### Step 3: Configure Conflict Avoidance Strategy

Choose an isolation strategy based on the work:

**Option A: Git Worktrees (Recommended for file-heavy work)**
- Create one worktree per agent: `git worktree add ../agent-<n> -b agent-<n>/work`
- Each agent operates in its own worktree on its own branch
- Merge results back to main branch after all agents complete
- Best when agents touch many files across the codebase

**Option B: Branch-Per-Agent (Lightweight)**
- Each agent works on its own branch: `agent-<n>/<task-name>`
- Agents commit and push to their branches independently
- Merge branches sequentially after all agents complete
- Best when tasks are well-scoped to specific directories

**Option C: File-Level Partitioning (Simplest)**
- Assign specific files to each agent upfront — no overlap allowed
- Agents work in the same branch but never touch each other's files
- Best when tasks map cleanly to distinct files

Present options to the user and let them choose.

### Step 4: Set Up the Autonomous Loop

Configure each agent to run the autonomous loop: scan for tasks → claim → execute → complete/fail → repeat. See `references/lock-protocol.md` for the full loop definition.

Launch agents using Claude Code's Task tool with `run_in_background: true` for parallel execution. Each agent gets:
- A clear system prompt describing its role and the task format
- The path to the `_tasks/` directory
- The conflict avoidance strategy from Step 3

### Step 5: Monitor Progress

While agents run:
1. Periodically check `_tasks/` directory for progress
2. Report: `N completed / M total (K in progress, F failed)`
3. IF any agent has been locked on a task for >30 minutes → flag it
4. IF all tasks are complete or failed → proceed to Step 6

### Step 6: Merge and Verify

After all agents finish:
1. If using worktrees or branches → merge all agent branches back
2. Run the project's test suite to verify nothing broke
3. Report final status: what was completed, what failed, any conflicts

## Examples

### Example: Parallelizing Test Fixes

User says: "Fix all failing tests in the auth, billing, and notifications modules"

Result: Three agents launched with file-level partitioning, each owning one module directory. All modules fixed in parallel, merge back cleanly.

### Example: Parallel Code Migration

User says: "Migrate all API endpoints from Express to Fastify"

Result: Shared middleware migrated sequentially first (dependency), then route files parallelized with branch-per-agent strategy.

See `references/examples.md` for detailed walkthroughs of both scenarios.

## Troubleshooting

### Merge conflicts after parallel work
**Cause:** Two agents edited the same file or adjacent lines.
**Solution:** This shouldn't happen if file-level independence was verified in Step 1. Review the dependency analysis. For future runs, use stricter file partitioning.

### Stale lock files
**Cause:** An agent crashed or timed out without releasing its lock.
**Solution:** Check the lock file timestamp. If >30 minutes old and the agent is no longer running, manually move the lock file to `_tasks/failed/` and reassign the task.

### Two agents picked the same task
**Cause:** Race condition — both checked availability simultaneously.
**Solution:** The lock-file protocol should prevent this. If it happens, check that agents are using atomic file operations. The second agent should detect the existing lock and skip to the next task.

### Agent stuck in a loop
**Cause:** Agent keeps failing the same task and reclaiming it.
**Solution:** After 2 failures on the same task, the agent should mark it as `blocked` and move to the next task. Review blocked tasks manually.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/am2rican5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
