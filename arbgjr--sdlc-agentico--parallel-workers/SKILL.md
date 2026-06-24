---
name: parallel-workers
description: | Use when this capability is needed.
metadata:
  author: arbgjr
---

# Parallel Workers

Skill para execução paralela de tarefas de implementação usando git worktrees isolados.

## Overview

Esta skill permite que múltiplas tarefas de código sejam desenvolvidas simultaneamente por workers independentes, cada um operando em seu próprio git worktree. Inspirada no claude-orchestrator, foi adaptada para ser multiplataforma e integrar com o ecossistema do SDLC Agêntico.

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Main Repository                      │
│                  (feature/epic-33-*)                    │
└────────────┬────────────────────────────────────────────┘
             │
             ├─→ Worker 1: ~/.worktrees/project/task-001/
             │   Branch: feature/task-001
             │   State: WORKING
             │   Agent: code-author
             │
             ├─→ Worker 2: ~/.worktrees/project/task-002/
             │   Branch: feature/task-002
             │   State: PR_OPEN
             │   Agent: test-author
             │
             └─→ Worker 3: ~/.worktrees/project/task-003/
                 Branch: feature/task-003
                 State: NEEDS_INIT
                 Agent: iac-engineer
```

## Worker State Machine

```
UNKNOWN ──→ NEEDS_INIT ──→ WORKING ──→ PR_OPEN ──→ MERGED
   ↑            ↑             ↓            ↓
   └────────────┴─────────────┴────────────┘
              (error recovery)
```

**States:**
- `UNKNOWN`: Initial or error state
- `NEEDS_INIT`: Worker created, needs initialization
- `WORKING`: Task in progress
- `PR_OPEN`: Pull request created, awaiting review
- `MERGED`: Task completed and merged

## Usage

### Spawning Workers

```bash
# Spawn a single worker
python3 .claude/skills/parallel-workers/scripts/worker_manager.py spawn \
  --task-id "TASK-001" \
  --description "Implement user authentication" \
  --agent "code-author" \
  --base-branch "feature/epic-33-claude-orchestrator"

# Spawn multiple workers from spec
python3 .claude/skills/parallel-workers/scripts/worker_manager.py spawn-batch \
  --spec-file .agentic_sdlc/projects/current/tasks.yml
```

### Managing Worktrees

```bash
# Create worktree
.claude/skills/parallel-workers/scripts/worktree_manager.sh create \
  project-name task-001 feature/epic-33

# List active worktrees
.claude/skills/parallel-workers/scripts/worktree_manager.sh list project-name

# Remove worktree
.claude/skills/parallel-workers/scripts/worktree_manager.sh remove \
  project-name task-001
```

### Monitoring State

```bash
# Check worker state
python3 .claude/skills/parallel-workers/scripts/state_tracker.py get worker-001

# List all workers
python3 .claude/skills/parallel-workers/scripts/state_tracker.py list

# Update state
python3 .claude/skills/parallel-workers/scripts/state_tracker.py set \
  worker-001 WORKING
```

## Security Considerations

### Secrets Isolation

Workers operate with sanitized environments:

```bash
# Sanitized variables (kept)
- PATH
- HOME
- USER
- LANG

# Removed from worker env
- *_KEY
- *_SECRET
- *_TOKEN
- *_PASSWORD
```

### Validation Before Merge

All workers must pass quality gates before merge:

```yaml
# From security-gate.yml
- no_hardcoded_secrets
- input_validation_present
- sast_scan_passed
```

### Audit Trail

All worker operations logged to Loki:

```json
{
  "skill": "parallel-workers",
  "phase": 5,
  "worker_id": "worker-001",
  "task_id": "TASK-001",
  "state": "WORKING",
  "timestamp": "2026-01-21T10:30:00Z",
  "correlation_id": "abc-123-def"
}
```

## Integration with SDLC Phases

### Phase 4 → 5 Transition

```python
# delivery-planner generates tasks
tasks = [
  {"id": "TASK-001", "agent": "code-author", "desc": "..."},
  {"id": "TASK-002", "agent": "test-author", "desc": "..."},
]

# Spawn workers in parallel
for task in tasks:
    spawn_worker(task)
```

### Phase 5 → 6 Transition

```python
# Wait for all workers to complete
all_merged = wait_for_workers(timeout=3600)

if all_merged:
    # Run phase-5-to-6 gate
    gate_result = evaluate_gate("phase-5-to-6.yml")
    if gate_result.passed:
        transition_to_phase(6)
```

## Observability

### Loki Queries

```logql
# All parallel-workers activity
{skill="parallel-workers"}

# Errors by worker
{skill="parallel-workers", level="error"} | json | line_format "{{.worker_id}}: {{.message}}"

# Task completion timeline
{skill="parallel-workers", state="MERGED"} | json | line_format "{{.task_id}} completed at {{.timestamp}}"
```

### Grafana Dashboard

New panels added to `.claude/config/logging/dashboards/sdlc-overview.json`:

- **Active Workers** (gauge)
- **Worker State Distribution** (pie chart)
- **Task Completion Rate** (timeseries)
- **Worker Errors** (logs panel)

## Comparison with Sequential Execution

| Metric | Sequential | Parallel (3 workers) | Improvement |
|--------|-----------|----------------------|-------------|
| Total time (3 tasks) | 90 min | ~35 min | **61% faster** |
| Resource usage | 1x CPU | 3x CPU | Scalable |
| Context switching | None | Minimal | Isolated |
| Merge conflicts | N/A | **Zero** (worktrees) | Safe |

## Limitations

- **Max workers**: Recommended 3-5 (CPU-bound)
- **Disk usage**: Each worktree ~= repo size
- **Network**: Parallel git operations may hit rate limits
- **Complexity**: Level 2+ only (Level 0/1 remain sequential)

## Future Enhancements

- [ ] Distributed workers across machines
- [ ] Dynamic task rebalancing
- [ ] Real-time worker chat/coordination
- [ ] GPU-accelerated task scheduling

## References

- Source: [claude-orchestrator](https://github.com/reshashi/claude-orchestrator)
- Analysis: `.project/corpus/nodes/learnings/LEARN-claude-orchestrator-patterns.yml`
- Epic: Issue #33
- Task: Issue #35

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arbgjr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
