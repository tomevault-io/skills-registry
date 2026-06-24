---
name: wiggum-squad
description: Agent team workflow for prd.json task lists. Activate when user says "wiggum squad", "wiggum-squad", or "ralph squad". A lead agent coordinates while spawning task agent teammates to implement tasks sequentially. Requires CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1. Use when this capability is needed.
metadata:
  author: daftgopher
---

# Wiggum Squad (Agent Team Workflow)

Coordinate a team of task agents to execute ALL tasks from a prd.json file sequentially. The lead (you) manages prd.json, progress.txt, and failure-counts.json. Task agents do the implementation work.

## Workflow

### 1. Run Initializer (optional)

- Check for `.agent/scripts/initializers/ralph.sh` in working directory
- If exists, run it to verify dependencies
- If exit code is 0, continue; if non-zero, note failure but continue anyway
- If script doesn't exist, skip this step

### 2. Find prd.json

- If project name provided: `.agent/projects/<PROJECT_NAME>/prd.json`
- Otherwise: find most recently modified prd.json in `.agent/projects/*/`

### 3. Read progress.txt

- Same directory as prd.json
- Note the "Codebase Patterns" section at top — pass relevant patterns to task agents

### 4. Create Agent Team

- Team name: `wiggum-squad-<PROJECT_NAME>`
- Use tmux split-pane mode for teammates (`--teammate-mode tmux`)
- Enter delegate mode so you only coordinate, never implement

### 5. Task Loop

For each task where `passes: false` (in priority order):

**a. Select next task**

Pick the highest priority task where `passes: false`.

**b. Check failure count**

Read `.agent/projects/<PROJECT_NAME>/failure-counts.json`. If the task's failure count >= 3, **STOP immediately** and report to the user:

```
STOPPED: Task [TASK_ID] has failed 3 times and requires human investigation.
```

**c. Read task agent instructions**

Read `references/task-agent-instructions.md` from this skill's directory.

**d. Spawn ONE teammate**

Spawn a single teammate in a new tmux pane. **Always use `model: "sonnet"` when spawning task agents** to conserve costs. The spawn prompt must contain:
- The full contents of `references/task-agent-instructions.md`
- The single task JSON object (from prd.json)
- The path to prd.json (for read-only context)
- Any relevant codebase patterns from progress.txt

**e. Wait for teammate**

Wait for the teammate to finish and send a TASK_RESULT message.

**f. If passed (`passed: true`):**

1. Update prd.json — set `passes: true` for this task
2. Append success record to progress.txt (see format below)
3. Shut down the teammate (tmux pane closes)
4. Loop to next task (step 5a)

**g. If failed (`passed: false`):**

1. Do NOT update prd.json
2. Append failure record to progress.txt with learnings (note it was a failure)
3. Increment failure count for this task in `failure-counts.json`
4. Shut down the teammate (tmux pane closes)
5. The failed agent may have committed partial work to the branch — this is fine, the next agent can build on it
6. Re-attempt the SAME task from step 5a (spawn a new agent with fresh context)

### 6. Clean Up Team

Shut down the agent team when all tasks are complete or stopped.

### 7. Report

If ALL tasks have `passes: true`:

```
<promise>COMPLETE</promise>
```

## Communication Protocol

Task agents send this message when done:

```
TASK_RESULT
task_id: <id>
passed: true|false
summary: <what was implemented>
files_changed:
- <file1>
- <file2>
learnings:
- <learning1>
codebase_patterns:
- <pattern (optional)>
```

## failure-counts.json

Location: same directory as prd.json (`.agent/projects/<PROJECT_NAME>/failure-counts.json`).

Create this file if it doesn't exist. Only the lead writes to it.

```json
{
  "task-id-001": 1,
  "task-id-002": 3
}
```

## Progress.txt Format

**Append to end:**

```markdown
## [Date] - [Task ID] - [PASS|FAIL]

- What was implemented
- Files changed
- **Learnings:**
  - Patterns discovered
  - Gotchas encountered

---
```

**Add reusable patterns to TOP under "Codebase Patterns":**

```markdown
## Codebase Patterns

- Migrations: Use IF NOT EXISTS
- React: useRef<Timeout | null>(null)
```

## Error Handling

| Scenario | Action |
|---|---|
| Task agent reports failure | Do NOT update prd.json. Record failure + learnings in progress.txt. Increment failure count. Shut down agent. Retry same task with new agent. |
| Task has failed 3+ times | STOP immediately. Report to human user for investigation. |
| Task agent unresponsive | Treat as failure (same flow as above). |
| Spawn failure | Report error and stop. |

## File Conflict Prevention

- **Only the lead** writes to prd.json, progress.txt, and failure-counts.json
- Task agents commit implementation code only (never prd.json, progress.txt, or failure-counts.json)
- Sequential execution prevents merge conflicts between task agents

---
> Source: [daftgopher/agent-stuff](https://github.com/daftgopher/agent-stuff) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
