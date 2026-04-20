---
name: orchestrator
description: Manage stories, plans, and tasks in the multi-model orchestrator. Use this skill to create feature requests, generate implementation plans, and execute tasks using different Claude models (Opus, Sonnet, Haiku). Use when this capability is needed.
metadata:
  author: whiteboardev
---

# Orchestrator Skill

Manage the full lifecycle of feature development through stories, plans, and tasks.

## Workflow Overview

```
Story → Plan → Tasks → Execution
```

1. **Story**: User describes a feature/task → Haiku routes by complexity
2. **Plan**: Opus/Sonnet generates implementation plan → Human approves
3. **Tasks**: Plan breaks into executable tasks → Assigned to appropriate model
4. **Execution**: Tasks execute in sequence → Human reviews output

## CLI Scripts

### Story Management

```bash
# Create a new story (auto-routes by complexity)
bun scripts/story.ts create "Add user authentication with JWT"

# List stories
bun scripts/story.ts list
bun scripts/story.ts list --status planning

# Get story details with tasks
bun scripts/story.ts get <storyId>

# Cancel a story
bun scripts/story.ts cancel <storyId>

# Set workflow phase (Architect/Editor system)
bun scripts/story.ts set-phase <storyId> execution

# Set execution mode with permissions
bun scripts/story.ts set-mode <storyId> supervised --commands true --tests true
```

### Plan Management

```bash
# Generate implementation plan (uses Opus/Sonnet)
bun scripts/plan.ts generate <storyId>

# Get current plan
bun scripts/plan.ts get <storyId>

# Approve plan and create tasks
bun scripts/plan.ts approve <planId>

# Reject plan with feedback
bun scripts/plan.ts reject <planId> "Need error handling for edge cases"
```

### Task Management

```bash
# List tasks for a story
bun scripts/task.ts list <storyId>
bun scripts/task.ts list <storyId> --status pending

# Get task details
bun scripts/task.ts get <taskId>

# Execute next pending task
bun scripts/task.ts execute-next <storyId>

# Approve task output
bun scripts/task.ts approve <taskId>

# Reject task with feedback
bun scripts/task.ts reject <taskId> "Missing null check on line 42"

# Set state machine for task
bun scripts/task.ts set-machine <taskId> --file machine.yaml
bun scripts/task.ts set-machine <taskId> --json '{"taskId":"...", ...}'

# Transition task state
bun scripts/task.ts transition <taskId> step_2 "Step 1 complete"

# Generate state machine template
bun scripts/task.ts template my-task-id > machine.yaml
```

### Statistics

```bash
# Get project statistics
bun scripts/stats.ts
bun scripts/stats.ts <projectId>
```

## Environment Variables

| Variable | Purpose | Default |
|----------|---------|---------|
| `BRAINY_API_URL` | API base URL | `http://localhost:3000` |
| `BRAINY_PROJECT_ID` | Default project UUID | (configured) |
| `BRAINY_USER_ID` | Default user UUID | (configured) |

## Story Phases (Architect/Editor Agent System)

The Architect and Editor operate as **agents**, not skills:
- **Architect**: Primary agent (Tab to switch) - READ-ONLY, designs state machines
- **Editor**: Hidden subagent - invoked via Task tool by Architect

| Phase | Description |
|-------|-------------|
| `ideation` | Human + Architect agent define the goal |
| `planning` | Architect agent explores codebase, identifies dependencies |
| `ticketing` | Joint prioritization of work items |
| `implementation` | Architect agent creates state machine per task |
| `permission_gate` | Architect agent asks execution mode + permissions |
| `execution` | Editor agent works within approved state machines |

> **Agent Invocation**: Use Tab to switch between Build and Architect agents. Editor is not directly accessible—Architect delegates to it via the Task tool.

## Execution Modes

| Mode | Description |
|------|-------------|
| `autonomous` | Editor agent executes without per-task approval |
| `supervised` | Editor agent requires approval after each task |
| `manual` | Human executes, Editor agent only assists |

## Permissions

When setting execution mode, specify which operations the Editor agent can perform:

| Permission | Flag | Description |
|------------|------|-------------|
| Run Commands | `--commands` | Execute shell commands |
| Install Packages | `--packages` | Run npm/bun install |
| Run Tests | `--tests` | Execute test suites |
| Delete Files | `--delete` | Remove files |
| Git Operations | `--git` | Commit, push, branch |

## State Machine

Tasks can have state machines that define allowed operations. See `task.ts template` for format.

### Required States

Every state machine must include:
- `error_recovery` - Handle errors
- `architect_escalation` - Return to Architect (terminal)
- `completion` - Success state (terminal)

### State Types

| Type | Description |
|------|-------------|
| `create_file` | Create a new file |
| `modify_file` | Edit existing file |
| `delete_file` | Remove a file |
| `run_command` | Execute shell command |
| `error_recovery` | Handle error condition |
| `architect_escalation` | Escalate to Architect |
| `completion` | Task complete |

## API Reference

### Stories

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/v1/projects/:projectId/stories` | POST | Create story |
| `/v1/projects/:projectId/stories` | GET | List stories |
| `/v1/stories/:storyId` | GET | Get story with tasks |
| `/v1/stories/:storyId/cancel` | POST | Cancel story |
| `/v1/stories/:storyId/phase` | POST | Set phase |
| `/v1/stories/:storyId/execution-mode` | POST | Set execution mode |

### Plans

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/v1/stories/:storyId/plan` | POST | Generate plan |
| `/v1/stories/:storyId/plan` | GET | Get current plan |
| `/v1/plans/:planId/approve` | POST | Approve plan |
| `/v1/plans/:planId/reject` | POST | Reject plan |

### Tasks

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/v1/stories/:storyId/tasks` | GET | List tasks |
| `/v1/stories/:storyId/tasks/next` | POST | Execute next task |
| `/v1/tasks/:taskId` | GET | Get task |
| `/v1/tasks/:taskId/approve` | POST | Approve task |
| `/v1/tasks/:taskId/reject` | POST | Reject task |
| `/v1/tasks/:taskId/state-machine` | POST | Set state machine |
| `/v1/tasks/:taskId/transition` | POST | Transition state |

### Stats

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/v1/projects/:projectId/orchestrator/stats` | GET | Get statistics |

## Usage Examples

### Complete Workflow

```bash
# 1. Create story
bun scripts/story.ts create "Add password reset feature"
# Output: Story created: abc123...

# 2. Generate plan
bun scripts/plan.ts generate abc123
# Output: Plan generated with 5 tasks

# 3. Review and approve plan
bun scripts/plan.ts approve def456
# Output: 5 tasks created

# 4. Execute tasks
bun scripts/task.ts execute-next abc123
# Review output...
bun scripts/task.ts approve ghi789

# 5. Repeat until complete
bun scripts/task.ts execute-next abc123
# Output: All tasks complete!
```

### Architect/Editor Agent Workflow

The Architect and Editor now operate as agents with dedicated CLI scripts at `.opencode/scripts/`:

```bash
# Architect agent scripts (.opencode/scripts/architect/)
bun .opencode/scripts/architect/session.ts        # Manage Architect sessions
bun .opencode/scripts/architect/advance-phase.ts  # Advance workflow phase
bun .opencode/scripts/architect/delegate.ts       # Delegate task to Editor
bun .opencode/scripts/architect/review.ts         # Review Editor output
bun .opencode/scripts/architect/test-plan.ts      # Generate test plan

# Editor agent scripts (.opencode/scripts/editor/)
bun .opencode/scripts/editor/execute.ts           # Execute state machine
bun .opencode/scripts/editor/transition.ts        # Transition state
bun .opencode/scripts/editor/validate.ts          # Validate state machine
bun .opencode/scripts/editor/escalate.ts          # Escalate to Architect
```

**Manual Workflow Example**:

```bash
# 1. Create story
bun scripts/story.ts create "Refactor auth module"

# 2. Move through phases (via Architect agent or manually)
bun scripts/story.ts set-phase abc123 planning
bun scripts/story.ts set-phase abc123 implementation

# 3. Set execution mode with permissions
bun scripts/story.ts set-mode abc123 supervised --commands true --tests true

# 4. Set state machine for task
bun scripts/task.ts template task-1 > /tmp/machine.yaml
# Edit machine.yaml...
bun scripts/task.ts set-machine task-1 --file /tmp/machine.yaml

# 5. Transition through states
bun scripts/task.ts transition task-1 step_1 "Starting execution"
bun scripts/task.ts transition task-1 step_2 "File created"
bun scripts/task.ts transition task-1 completion "All steps done"
```

> **Note**: In normal usage, the Architect agent handles phase transitions and delegates to the Editor agent via the Task tool. Manual scripts are useful for debugging, recovery, or advanced workflows.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/whiteboardev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
