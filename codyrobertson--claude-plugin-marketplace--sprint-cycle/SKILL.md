---
name: sprint-cycle
description: Run a complete sprint cycle with planning, implementation, quality gates, code review, and retrospective. Coordinates dev-plan-architect, code critics, and retrospective analysts. Use when this capability is needed.
metadata:
  author: codyrobertson
---

# Sprint Cycle

Execute a complete sprint for: **$ARGUMENTS**

## Sprint Phases

### 1. Sprint Planning

Break down the work into concrete tasks:

```python
Task(
    subagent_type="dev-plan-architect",
    description="Create sprint plan",
    prompt="""
    Create a detailed sprint plan for: [FEATURE]

    Break down into:
    1. Epics (major work streams)
    2. Tasks (implementable units)
    3. Dependencies (what blocks what)
    4. Estimates (relative sizing)

    Format as actionable todo items.
    """
)
```

Create TodoWrite items for each task.

### 2. Implementation Sprint

For each task in priority order:

1. Mark task as in_progress
2. Read relevant files
3. Implement the change
4. Write/update tests
5. Mark task as completed
6. Commit with clear message

Use Task tool with `general-purpose` agent for complex subtasks.

### 3. Quality Gate

Before completing sprint, run quality checks:

```python
# Run tests
Bash(command="npm test" or "pytest" or appropriate)

# Run linting
Bash(command="npm run lint" or "ruff check" or appropriate)

# Type checking
Bash(command="npm run typecheck" or "mypy" or appropriate)
```

If any fail, fix before proceeding.

### 4. Code Review

Launch review agents:

```python
Task(subagent_type="brutal-code-critic", ...)
Task(subagent_type="production-code-validator", ...)
```

Address critical/high issues before sprint completion.

### 5. Sprint Retrospective

```python
Task(
    subagent_type="sprint-retrospective-analyst",
    description="Sprint retrospective",
    prompt="""
    Conduct sprint retrospective for [FEATURE]:

    1. Goals vs Delivered
    2. Code Quality Assessment
    3. Architecture Evaluation
    4. Testing Coverage
    5. Technical Debt
    6. Process Improvements
    7. Next Sprint Recommendations
    """
)
```

### 6. Sprint Completion

1. Create commit with all changes (if not already committed)
2. Push to feature branch
3. Create PR with sprint summary
4. Present retrospective findings

## Sprint Velocity Tracking

Track in todo list:
- Tasks planned
- Tasks completed
- Tasks deferred
- Blockers encountered

## Begin Sprint

Start sprint planning for: **$ARGUMENTS**

If no feature specified, ask what to build this sprint.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/codyrobertson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
