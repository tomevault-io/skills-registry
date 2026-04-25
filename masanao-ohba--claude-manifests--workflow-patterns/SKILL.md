---
name: workflow-patterns
description: Invoke when coordinating multi-agent execution via TeamCreate. Provides workflow templates (team setup, task dependencies, retry loops, failure handling), progress tracking via TaskList, and coordination patterns. Use when this capability is needed.
metadata:
  author: masanao-ohba
---

# Workflow Patterns

A technology-agnostic skill for managing multi-agent workflows via team orchestration.

## Core Patterns

### Team Orchestration Pattern

```yaml
pattern: team_orchestration
description: "Create team, define tasks with dependencies, spawn workers"
use_when:
  - "Non-trivial tasks (small/medium/large)"
  - "Multiple agents needed"
  - "Quality gates required"

implementation:
  1_create_team: "TeamCreate with descriptive name"
  2_create_tasks: "TaskCreate with blockedBy dependencies"
  3_spawn_workers: "Task tool with team_name to spawn teammates"
  4_monitor: "Watch TaskList and teammate messages"
  5_evaluate: "Task(deliverable-evaluator) when chain complete"
  6_cleanup: "SendMessage(shutdown_request) + TeamDelete"
```

### Task Dependency Pattern

```yaml
pattern: task_dependencies
description: "Declare execution order via blockedBy relationships"
use_when:
  - "Steps must execute in order"
  - "Some steps can run in parallel"

example:
  tasks:
    - id: 1
      subject: "Implement feature"
      blockedBy: []
    - id: 2
      subject: "Run tests"
      blockedBy: [1]
    - id: 3
      subject: "Review code"
      blockedBy: [1]
    - id: 4
      subject: "Evaluate deliverables"
      blockedBy: [2, 3]

  # Tasks 2 and 3 run in parallel after task 1
  # Task 4 waits for both 2 and 3
```

### Iteration Pattern

```yaml
pattern: iteration
description: "Retry via new tasks until success or max iterations"
use_when:
  - "Quality gates must pass"
  - "Debugging cycles needed"
  - "Refinement required"

implementation:
  on_test_failure:
    1: "Create debug task (test-failure-debugger)"
    2: "Create fix task (code-developer, blockedBy: debug)"
    3: "Create retest task (test-executor, blockedBy: fix)"

  on_evaluation_fail:
    1: "Create fix tasks from DE recommendations"
    2: "Create retest + re-review tasks"
    3: "Create re-evaluate task (blockedBy: retest, re-review)"

  exit_conditions:
    - "DE returns PASS"
    - "Max iterations (3) reached"
    - "User intervention requested"
```

### Direct Delegation Pattern

```yaml
pattern: direct_delegation
description: "Simple Task call without team overhead"
use_when:
  - "Trivial tasks"
  - "Single agent needed"
  - "No coordination required"

implementation:
  single_task: "Task(code-developer) with full context"
  no_team: true
  no_task_list: true
```

## Workflow Templates

### Standard Implementation Workflow

```yaml
template: standard_implementation
scale: small|medium

phases:
  analysis:
    - Task(goal-clarifier) → acceptance_criteria
    - Task(task-scale-evaluator) → scale, subtasks

  execution:
    - TeamCreate("dev-workflow")
    - TaskCreate: implement (no deps)
    - TaskCreate: test (blockedBy: implement)
    - TaskCreate: review (blockedBy: implement)
    - TaskCreate: evaluate (blockedBy: test, review)
    - Spawn: code-developer, test-executor, quality-reviewer

  evaluation:
    - Task(deliverable-evaluator) when tasks complete
    - On FAIL: create fix tasks, loop
    - On PASS: cleanup team

  cleanup:
    - SendMessage(shutdown_request) to all teammates
    - TeamDelete
```

### Full Feature Workflow

```yaml
template: full_feature
scale: large

phases:
  analysis:
    - Task(goal-clarifier) → acceptance_criteria
    - Task(task-scale-evaluator) → scale, subtasks, parallel_groups

  design:
    - Task(design-architect) → architecture spec

  execution:
    - TeamCreate("dev-workflow")
    - TaskCreate per subtask with dependency graph
    - Spawn: code-developer, test-developer, test-executor, quality-reviewer

  evaluation:
    - Task(deliverable-evaluator)
    - Retry loop (max 3)

  cleanup:
    - Shutdown teammates, TeamDelete
```

### Debug Loop

```yaml
template: debug_loop
trigger: "Test failures during execution"

steps:
  1_analyze:
    - TaskCreate: "Debug test failures"
    - Assign to test-failure-debugger (or use Task delegation)

  2_fix:
    - TaskCreate: "Apply fix" (blockedBy: debug task)
    - Assign to code-developer

  3_verify:
    - TaskCreate: "Re-run tests" (blockedBy: fix task)
    - Assign to test-executor

  4_check:
    - If pass: continue to review/evaluate
    - If fail: loop (max 3 iterations)
```

## Coordination Strategies

### Progress Tracking via TaskList

```yaml
tracking:
  use: "TaskList tool to check status"
  states:
    pending: "Not yet started"
    in_progress: "Being worked on"
    completed: "Done"

  monitoring:
    - "Check TaskList after teammate messages"
    - "Identify blocked tasks"
    - "Assign unblocked tasks to idle teammates"
```

### Inter-Agent Communication

```yaml
communication:
  teammate_updates:
    via: "SendMessage(type: message)"
    when: "Task completion, blocking issues, questions"

  team_announcements:
    via: "SendMessage(type: broadcast)"
    when: "Critical issues only (expensive)"

  shutdown:
    via: "SendMessage(type: shutdown_request)"
    when: "All work complete, DE returns PASS"
```

### Error Handling

```yaml
error_handling:
  test_failure:
    action: "Create debug + fix + retest tasks"
    max_retries: 3

  agent_failure:
    action: "Re-spawn teammate, reassign task"

  blocked_task:
    detection: "Task stuck in pending with unresolved blockedBy"
    action: "Investigate blocking task, escalate if needed"

  evaluation_fail:
    action: "Create targeted fix tasks from DE feedback"
    max_iterations: 3
```

## Anti-Patterns

### Over-Orchestration

```yaml
anti_pattern: over_orchestration
description: "Using team for trivial tasks"
symptom: "TeamCreate for single-line change"
solution: "Use direct Task delegation for trivial work"
```

### Missing Exit Conditions

```yaml
anti_pattern: infinite_loop
description: "Retry without max iteration check"
symptom: "Endless fix-test-fail cycle"
solution: "Always enforce max 3 iterations, escalate to user"
```

### Premature Evaluation

```yaml
anti_pattern: premature_evaluation
description: "Calling DE before implementation chain completes"
symptom: "DE fails because tests haven't run"
solution: "Use blockedBy to ensure evaluate waits for test + review"
```

## Integration

### Used By

```yaml
primary_users:
  - "/dev-workflow command": "Orchestration patterns"

secondary_users:
  - task-scale-evaluator: "Workflow recommendations"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masanao-ohba) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
