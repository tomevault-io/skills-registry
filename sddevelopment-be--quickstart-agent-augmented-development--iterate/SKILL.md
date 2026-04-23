---
name: iterate
description: Execute a complete iteration cycle: planning → batch execution → documentation → review setup. Follows file-based orchestration with TDD/ATDD, work logs, and quality gates. Use when this capability is needed.
metadata:
  author: sddevelopment-be
---

# Iterate: Multi-Agent Iteration Orchestration

Execute a complete iteration cycle following the file-based orchestration approach with Planning Petra coordinating specialist agents through TDD/ATDD workflow.

## Instructions

Acting as orchestrator in file-based agent collaboration, execute a batch:

1. **Planning Petra: Assess current state**
   - Read `work/collaboration/inbox/` for pending tasks
   - Identify next batch (check `NEXT_BATCH.md` if exists)
   - Priority order: critical > high > medium > low

2. **Execute batch (specialist agents):**

   For each task in batch (priority descending):

   a. Initialize as assigned agent (backend-dev, architect, frontend, etc.)

   b. **Start task using script:**
      ```bash
      python tools/scripts/start_task.py TASK_ID
      ```

   c. Follow TDD/ATDD cycle:
      - **RED:** Write failing test first
      - **GREEN:** Implement minimum code to pass
      - **REFACTOR:** Improve code quality

   d. Run all tests: ensure passing before proceeding

   e. **Complete task using script:**
      ```bash
      python tools/scripts/complete_task.py TASK_ID
      ```
      (Validates result block exists and moves to done/{agent}/)

   f. Create work log (directive 014) in `work/reports/logs/<agent>/`

   **If blocked:** Use freeze script instead:
   ```bash
   python tools/scripts/freeze_task.py TASK_ID --reason "Specific blocking reason"
   ```

3. **Planning Petra: Update artifacts**
   - Update roadmap if exists (`docs/architecture/roadmap-*.md`)
   - Update `NEXT_BATCH.md` with next priorities
   - Document blockers or decisions

4. **Provide executive summary:**
   - Tasks completed (time: estimated vs actual)
   - Tests passing (coverage %)
   - Decisions made or deferred
   - Blockers identified
   - Next recommended batch

5. **Review gate (conditional):**
   - If significant architectural changes: create Alphonso review task
   - If routine implementation: ready for next iteration

## Quality Gates

**Before marking batch complete:**
- ✅ All tests passing
- ✅ Coverage >80% (target)
- ✅ Work logs created (directive 014)
- ✅ Tasks completed using `complete_task.py` script (moved to `done/<agent>/`)
- ✅ Planning artifacts updated
- ✅ Any blocked tasks frozen using `freeze_task.py`

## Directives Applied

- **Directive 014:** Work Log Creation
- **Directive 016:** Acceptance Test-Driven Development (ATDD)
- **Directive 017:** Test-Driven Development (TDD)
- **Directive 018:** Traceable Decisions (ADRs if needed)
- **Directive 019:** File-Based Orchestration

## Agent Handoff Pattern

```
Planning Petra
    ↓ (identifies batch)
Specialist Agent(s)
    ↓ (executes with TDD)
Planning Petra
    ↓ (updates artifacts)
Architect Alphonso (if needed)
    ↓ (reviews & approves)
[Ready for next /iterate]
```

## Example Usage

```
User: /iterate

Agent (Petra): Starting M2 Batch 2.3 (2 tasks: GenericYAMLAdapter + ENV support)...

Agent (Backend-Dev):
  - Task 1: GenericYAMLAdapter [HIGH]
    - Started: `python tools/scripts/start_task.py 2026-02-05-generic-yaml-adapter`
    - RED: 24 failing tests written
    - GREEN: Implementation complete, all tests pass
    - REFACTOR: Extracted common patterns
    - Completed: `python tools/scripts/complete_task.py 2026-02-05-generic-yaml-adapter`
    - Result: 82% coverage, 3.5h (vs 5h estimated)

  - Task 2: ENV variable support [HIGH]
    - Started: `python tools/scripts/start_task.py 2026-02-05-env-support`
    - RED: 20 failing tests written
    - GREEN: Template expansion implemented
    - REFACTOR: Security validations added
    - Completed: `python tools/scripts/complete_task.py 2026-02-05-env-support`
    - Result: 100% coverage, 1.5h (vs 2h estimated)

Agent (Petra):
  ✅ M2 Batch 2.3 COMPLETE (2/2 tasks, 5h actual vs 7h estimated)
  ✅ 44 tests passing (92% coverage)
  ✅ Work logs: work/reports/logs/backend-dev/2026-02-05-m2-batch-2.3.md
  ✅ Roadmap updated: M2 Tool Integration 100% complete
  ✅ Tasks properly completed using scripts (validation passed)
  📊 Overall progress: 45% (M1-M2 done, M3-M4 remaining)

  Next recommended batch: M3 Batch 3.1 - Telemetry Infrastructure (3 tasks)

  Review gate: Significant architectural change (generic adapter approach)
  ✅ Created task: work/collaboration/inbox/2026-02-05-architect-m2-review.yaml
```

## Related Skills

- `/status` - Check current planning state before iterating
- Use `agents/prompts/iteration-orchestration.md` for detailed reference and advanced workflows

## References

- **Prompt Template:** `agents/prompts/iteration-orchestration.md` (comprehensive reference)
- **Approach:** `.github/agents/approaches/work-directory-orchestration.md`
- **Directive 014:** `.github/agents/directives/014_work_log_creation.md`
- **Directive 016:** `.github/agents/directives/016_acceptance_test_driven_development.md`
- **Directive 017:** `.github/agents/directives/017_test_driven_development.md`
- **Directive 019:** `.github/agents/directives/019_file_based_collaboration.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sddevelopment-be) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
