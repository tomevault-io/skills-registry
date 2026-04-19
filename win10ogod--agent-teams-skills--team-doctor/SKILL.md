---
name: team-doctor
description: Diagnostics, debugging, and recovery toolkit for Claude Code agent teams. Detects stalled tasks, circular dependencies, orphaned assignments, overloaded agents, and other team health issues. Provides automated diagnosis, repair commands, and prevention best practices. Use when this capability is needed.
metadata:
  author: win10ogod
---

# Team Doctor

A diagnostic and recovery skill for Claude Code agent teams. When a team is underperforming, stuck, or exhibiting unexpected behavior, Team Doctor provides the tools and knowledge to identify the root cause and fix it.

## Quick Diagnosis

Run the diagnostic script against any team:

```bash
python team-doctor/scripts/diagnose_team.py <team-name>
```

This produces a health report with a score from 0 to 100 and color-coded status:

| Score   | Status | Meaning                                      |
|---------|--------|----------------------------------------------|
| 80-100  | GREEN  | Team is healthy, no action needed             |
| 50-79   | YELLOW | Issues detected, intervention recommended     |
| 0-49    | RED    | Critical problems, immediate action required  |

Add `--verbose` for detailed per-task and per-agent breakdowns:

```bash
python team-doctor/scripts/diagnose_team.py <team-name> --verbose
```

The script checks:

1. **Configuration validity** -- Is the team config well-formed JSON with all required fields?
2. **Member completeness** -- Does a lead exist? Are there duplicate agent names? Are all roles valid?
3. **Task health** -- Are all task files valid JSON? Do they have valid statuses? Are assignments pointing to real agents?
4. **Circular dependencies** -- Does the dependency graph contain cycles that would deadlock progress?
5. **Stalled tasks** -- Are any in-progress tasks suspiciously old based on file modification time?
6. **Workload balance** -- Is any single agent overloaded relative to others?
7. **Dependency chain depth** -- Are there excessively long chains that create fragile pipelines?

Exit codes match severity: `0` for green, `1` for yellow, `2` for red.

## Common Issues and Fixes

### Stalled Tasks

**Symptoms:** A task stays `in_progress` for an unusually long time. The assigned agent appears idle or is producing no output.

**Cause:** The agent may have crashed, hit a tool error loop, or be waiting on a resource that will never arrive.

**Fix:**
```bash
python team-doctor/scripts/repair_tasks.py <team-name> --fix-stalled
```
This resets stalled in-progress tasks back to `pending` so they can be reassigned. The output provides TaskUpdate commands you can execute.

**Prevention:** Set reasonable timeout expectations. Monitor task file modification times. Use the `--verbose` flag during diagnosis to catch early signs.

### Circular Dependencies

**Symptoms:** Multiple tasks are blocked, none can proceed. The team appears deadlocked.

**Cause:** Task A depends on Task B, which depends on Task C, which depends on Task A (or similar cycle).

**Fix:**
```bash
python team-doctor/scripts/repair_tasks.py <team-name> --fix-circular
```
This detects all cycles and outputs commands to break them by removing the last edge in each cycle.

**Prevention:** Before adding `blockedBy` relationships, mentally trace the dependency chain. Keep dependency graphs shallow and wide rather than deep and narrow.

### Orphaned Tasks

**Symptoms:** Tasks are assigned to agents that do not exist in the team configuration.

**Cause:** An agent was removed from the team config but its tasks were not reassigned. Or a task was created with a typo in the assignee field.

**Fix:**
```bash
python team-doctor/scripts/repair_tasks.py <team-name> --fix-orphaned
```
This identifies orphaned tasks and outputs commands to unassign them so the lead can redistribute.

**Prevention:** Always reassign tasks before removing an agent from the team. Validate agent names when creating tasks.

### Overloaded Agents

**Symptoms:** One agent has many tasks while others sit idle. Overall team throughput is low despite available capacity.

**Cause:** The team lead assigned work unevenly, or task completion patterns created an imbalanced backlog.

**Fix:** Manually redistribute tasks from the overloaded agent to idle ones. Use the diagnosis report's workload section to identify the imbalance:
```bash
python team-doctor/scripts/diagnose_team.py <team-name> --verbose
```

**Prevention:** The lead should distribute tasks round-robin or by capability match, not pile them onto a single agent. Monitor workload balance regularly.

## Troubleshooting Decision Tree

Use this text-based tree to quickly navigate to the right fix:

```
START: What is the problem?
|
+-- "Team is not making progress"
|   |
|   +-- Are all tasks done?
|   |   YES --> Team is actually finished. Check if the lead needs to report completion.
|   |
|   +-- Are all remaining tasks pending (none in_progress)?
|   |   YES --> No agent is picking up work. Check if the lead is alive and assigning.
|   |           Run: diagnose_team.py <name> --verbose
|   |           Look at: "Member Completeness" section
|   |
|   +-- Are tasks blocked?
|   |   YES --> Check blockedBy references.
|   |           Run: diagnose_team.py <name> --verbose
|   |           Look at: "Circular Dependencies" and "Dependency Chains"
|   |           If circular: repair_tasks.py <name> --fix-circular
|   |
|   +-- Are tasks in_progress but not advancing?
|       YES --> Tasks are stalled.
|               Run: repair_tasks.py <name> --fix-stalled
|
+-- "A specific agent is stuck"
|   |
|   +-- Is the agent idle (no tasks assigned)?
|   |   YES --> Lead needs to assign work. Check lead health.
|   |
|   +-- Is the agent in a message loop (sending/receiving repeatedly)?
|   |   YES --> Message storm. The agent may need a shutdown_request
|   |           and fresh restart with clearer instructions.
|   |
|   +-- Is the agent repeatedly failing tool calls?
|       YES --> Check the agent's task scope. It may be assigned work
|               outside its capability. Reassign to a better-suited agent.
|
+-- "Agents are conflicting with each other"
|   |
|   +-- Are they editing the same files?
|   |   YES --> Task boundaries are unclear. Reassign with explicit
|   |           file ownership per agent.
|   |
|   +-- Are they taking contradictory approaches?
|   |   YES --> The lead needs to set architectural direction before
|   |           agents proceed. Pause conflicting tasks.
|   |
|   +-- Are they duplicating work?
|       YES --> Duplicate tasks exist. Deduplicate and cancel extras.
|               Run: diagnose_team.py <name> --verbose
|
+-- "Team is too slow"
    |
    +-- Is workload balanced?
    |   NO --> Redistribute tasks. Check diagnosis workload section.
    |
    +-- Are dependency chains too deep?
    |   YES --> Restructure tasks for more parallelism.
    |           Break long chains into independent sub-tasks.
    |
    +-- Are agents using appropriate models?
    |   NO --> Match model to task complexity. Use faster models
    |          for simple tasks, more capable models for complex ones.
    |
    +-- Is the team too small for the workload?
        YES --> Add more agents to the team configuration.
```

## Repair Operations

The repair script never modifies task files directly. It analyzes the current state and outputs TaskUpdate tool commands that you can review and execute:

```bash
python team-doctor/scripts/repair_tasks.py <team-name> --fix-all
```

Available repair flags:

| Flag              | Action                                                        |
|-------------------|---------------------------------------------------------------|
| `--fix-circular`  | Break circular dependencies by removing the last edge in each cycle |
| `--fix-orphaned`  | Unassign tasks assigned to non-existent agents                |
| `--fix-stalled`   | Reset long-running in-progress tasks to pending               |
| `--fix-all`       | Run all of the above                                          |

Without any `--fix-*` flag, the script runs in read-only diagnostic mode and just reports what it finds.

Example output:

```
=== Repair Plan for team: my-project ===

[CIRCULAR] Breaking cycle: task-3 -> task-7 -> task-12 -> task-3
  Action: Remove task-3 from blockedBy of task-12
  Command: TaskUpdate(task_id="task-12", status="pending", blockedBy=["task-7"])

[ORPHANED] Task task-5 assigned to "agent-x" (not in team config)
  Action: Unassign task
  Command: TaskUpdate(task_id="task-5", assignee=None)

[STALLED] Task task-9 in_progress since 2h 15m ago
  Action: Reset to pending
  Command: TaskUpdate(task_id="task-9", status="pending")

Total repairs: 3
```

Review each command before executing. The script is deliberately conservative -- it prefers minimal changes over aggressive restructuring.

## Prevention Best Practices

### Design Tasks for Independence

Structure tasks so they can proceed in parallel whenever possible. Prefer wide, shallow dependency graphs. A task that blocks five others is a bottleneck; five independent tasks that can run simultaneously maximize throughput.

### Validate Before Launch

Run the diagnostic script on a newly configured team before starting work:

```bash
python team-doctor/scripts/diagnose_team.py <team-name>
```

Catch configuration errors, missing members, and impossible dependency chains before they cause runtime failures.

### Monitor Continuously

Periodically re-run diagnostics during long-running team sessions. Problems compound over time -- a single stalled task can cascade into a full deadlock if it blocks other tasks.

### Keep Teams Small

Teams of 3-5 agents are easier to manage and debug than teams of 10+. If the workload requires more agents, consider splitting into sub-teams with clear interfaces.

### Document Agent Responsibilities

Each agent should have a clear, non-overlapping scope. When two agents might touch the same files, explicitly define ownership boundaries in their instructions.

### Use Dependency Chains Sparingly

Every dependency is a potential failure point. Only add `blockedBy` when there is a true data or ordering dependency. If tasks merely relate to each other but do not strictly require sequential execution, leave them independent.

## When to Rebuild a Team

Sometimes repair is not enough. Consider rebuilding the team from scratch when:

- **Health score is consistently below 30** despite multiple repair attempts.
- **More than half the tasks are stalled or orphaned** -- the task graph is too corrupted to salvage.
- **The team config has been manually edited** into an inconsistent state that repair scripts cannot reconcile.
- **The original task decomposition was fundamentally wrong** -- tasks do not map to the actual work needed.
- **Agent roles have drifted** so far from their original assignments that responsibilities overlap extensively.

Rebuilding means: archive the current team state, create a fresh configuration, decompose the remaining work into new tasks, and launch new agents. Preserve any completed work by committing it before teardown.

To rebuild:

1. Run diagnosis to understand what was accomplished: `diagnose_team.py <name> --verbose`
2. Note all tasks with status `done` -- their work products are already committed.
3. Archive the team directory (move or rename it).
4. Create a new team configuration addressing the structural problems identified.
5. Launch the new team and redistribute only the incomplete work.

## Related Skills

- **team-monitor** -- Use `team_status.py` and `bottleneck_detector.py` for ongoing monitoring before issues become critical
- **team-lifecycle** -- Manage graceful shutdown when a team is beyond repair
- **team-communicator** -- Fix communication breakdowns with proper protocols

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/win10ogod) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
