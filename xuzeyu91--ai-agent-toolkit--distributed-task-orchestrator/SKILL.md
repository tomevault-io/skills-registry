---
name: distributed-task-orchestrator
description: Decompose complex tasks into parallel sub-agents. Use for multi-step operations, batch processing, or when user mentions "parallel", "agents", "orchestrate", "subtasks", or "concurrent". Supports simulated parallel execution and real Claude CLI sub-agent launching. Use when this capability is needed.
metadata:
  author: xuzeyu91
---

# Distributed Task Orchestrator

Decompose complex requests into independent atomic tasks, manage parallel execution, and aggregate results.

## Quick Decision

```
Is task complex? (3+ independent steps, multiple files, parallel benefit)
├── NO → Execute directly, skip orchestration
└── YES → Use orchestration
    ├── Simulated mode (default) → Present as parallel batches
    └── CLI mode (user requests) → Launch real Claude CLI sub-agents
```

**Skip orchestration for:** single-file ops, simple queries, < 3 steps, purely sequential tasks.

## Core Workflow

### Phase 1: Decompose

Analyze request → Break into atomic tasks → Map dependencies → Create `.orchestrator/master_plan.md`

```markdown
# Task Plan

## Request
> [Original request]

## Tasks
| ID | Task | Deps | Status |
|----|------|------|--------|
| T-01 | [Description] | None | 🟡 |
| T-02 | [Description] | T-01 | ⏸️ |
```

**Status:** 🟡 Pending · 🔵 Running · ✅ Done · ❌ Failed · ⏸️ Waiting

### Phase 2: Assign Agents

Create `.orchestrator/agent_tasks/agent-XX.md` for each task:

```markdown
# Agent-XX: [Task Name]
**Input:** [parameters]
**Do:** [specific instructions]
**Output:** [expected format]
```

### Phase 3: Execute

**Simulated Mode (Default):**

```
═══ Batch #1 (No Dependencies) ═══
🤖 Agent-01 [T-01: Task Name]
   ⚙️ [Execution steps...]
   ✅ Completed

═══ Batch #2 (After Batch #1) ═══
🤖 Agent-02 [T-02: Task Name]
   ⚙️ [Execution steps...]
   ✅ Completed
```

**CLI Mode (When Requested):** See [cli-integration.md](references/cli-integration.md)

### Phase 4: Aggregate

Collect results → Merge by dependency order → Generate `.orchestrator/final_output.md`

## Dependency Patterns

- **Parallel:** T-01, T-02, T-03 → T-04 (first three run together)
- **Serial:** T-01 → T-02 → T-03 (each waits for previous)
- **DAG:** Complex graphs use topological sort

## Error Handling

| Strategy | When to Use |
|----------|-------------|
| Retry (3x, exponential backoff) | Timeouts, transient failures |
| Skip and continue | Non-critical tasks |
| Fail-fast | Critical dependencies |

## Best Practices

1. **Granularity:** Target 1-5 min per task; split large, merge trivial
2. **Parallelism:** Minimize dependencies; use file-based data passing
3. **State:** Update `master_plan.md` on every status change

## Reference Files

- [workflow.md](references/workflow.md) - Detailed workflow specification
- [templates.md](references/templates.md) - Complete templates for all files
- [cli-integration.md](references/cli-integration.md) - Claude CLI integration guide
- [examples.md](references/examples.md) - Practical examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xuzeyu91) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
