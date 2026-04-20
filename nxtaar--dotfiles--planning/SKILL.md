---
name: planning
description: Creates structured implementation plans with numbered tasks, verification gates, and agent assignments. Use after brainstorming completes, when user wants a development plan, or says "plan", "break down", "create tasks". Produces actionable task lists with dependencies.
metadata:
  author: nxtaar
---

# Planning

Comprehensive implementation plans with verification checkpoints and agent assignments.

## When to Activate

- After brainstorming session completes
- User asks for a "plan" or "breakdown"
- Feature requires multiple implementation steps
- User wants to understand scope before starting

## Process

### 1. Analyze Requirements

From brainstorming output or user request:
- Extract concrete deliverables
- Identify dependencies between components
- Estimate complexity of each part
- Note testing requirements
- Map to appropriate agents

### 2. Explore Implementation Context

Before creating tasks:
- Review existing code architecture
- Identify files that need modification
- Check for reusable components
- Note testing patterns in codebase

### 3. Create Task Breakdown

Structure tasks with the Task Ledger pattern:

**Task Properties:**
- Clear, atomic actions (one logical change per task)
- Assigned agent type
- Dependencies on other tasks
- Verification criteria
- Estimated complexity (S/M/L)
- Files to create/modify

### 4. Apply Risk-Based Ordering

1. **Front-load risky tasks** - Uncertain implementations first
2. **Add checkpoint tasks** - Integration verification every 3-4 tasks
3. **Mark parallel-capable tasks** - Tasks without dependencies
4. **Identify critical path** - Longest dependency chain

### 5. Define Verification Gates

Each task includes:
- Commands to run after completion
- Expected outcomes
- Rollback approach if issues arise

## Output Format

```markdown
## Implementation Plan: [Feature Name]

### Overview
- **Total tasks:** [N]
- **Estimated complexity:** [S/M/L/XL]
- **Critical path:** Task 1 → Task 3 → Task 5 → Task 7
- **Parallel opportunities:** Tasks 2,4 can run with Task 3

### Task Breakdown

#### Task 1: [Short Title]
- **Status:** PENDING
- **Agent:** backend-developer
- **Complexity:** S
- **Dependencies:** None

**Action:**
[Specific implementation action]

**Files:**
- Create: `src/services/newService.ts`
- Modify: `src/index.ts`

**Verification Gate:**
```bash
npm run typecheck
npm test -- --grep "newService"
```

**Context for Agent:**
- Reference: `src/services/existingService.ts` for patterns
- Convention: Use dependency injection
- Constraint: No external API calls in this task

---

#### Task 2: [Short Title]
- **Status:** PENDING
- **Agent:** database-administrator
- **Complexity:** M
- **Dependencies:** None (can parallel with Task 1)

**Action:**
[...]

[Continue for all tasks...]

---

### Checkpoint Tasks

#### Checkpoint A (after Task 4): Integration Test
- Run: `npm run test:integration`
- Verify: API endpoints connect to database
- If fails: Debug before proceeding

---

### Final Verification
- [ ] All unit tests pass
- [ ] Integration tests pass
- [ ] Type checking passes
- [ ] Linting passes
- [ ] Manual smoke test: [specific scenarios]

### Risk Notes
| Risk | Mitigation | Contingency |
|------|------------|-------------|
| [Risk 1] | [Prevention] | [If it happens] |

### Progress Tracking
Save progress to `claude-progress.json`:
```json
{
  "feature": "[feature-name]",
  "status": "planned",
  "tasks": {"total": N, "complete": 0}
}
```
```

## Agent Assignment Guide

| Task Type | Recommended Agent | When to Use |
|-----------|-------------------|-------------|
| REST API endpoints | backend-developer | Server-side routes, controllers |
| Database schema | database-administrator | Migrations, queries, models |
| React components | react-specialist | UI components, hooks, state |
| TypeScript types | typescript-pro | Complex types, generics |
| Refactoring | refactoring-specialist | Code restructuring |
| Testing | qa-expert | Test cases, coverage |
| Security | security-engineer | Auth, validation, encryption |
| Performance | architect-reviewer | Optimization, caching |

## Task Complexity Guide

| Size | Description | Scope |
|------|-------------|-------|
| S | Single file, straightforward | < 50 lines changed |
| M | Multiple files, clear path | 50-200 lines changed |
| L | Cross-cutting, needs design | 200-500 lines changed |
| XL | Major feature, multiple systems | > 500 lines, split further |

## Best Practices

- Keep tasks small enough to complete in one session
- Every task must have verification criteria
- Include "checkpoint" tasks for integration testing
- Plan for iterative refinement
- Note existing patterns to follow

## Collaboration

After plan approval, invoke the **developing** skill to begin subagent-driven implementation of each task.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nxtaar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
