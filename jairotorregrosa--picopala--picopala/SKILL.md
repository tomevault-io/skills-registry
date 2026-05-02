---
name: picopala
description: > Use when this capability is needed.
metadata:
  author: jairotorregrosa
---

# Picopala: Agents Checking Agents

Multi-agent orchestration where **every agent's work is reviewed by a different agent** in an **iterative adversarial loop** — the reviewer either APPROVES or sends back for REVISION, with fresh context each round.

Uses Claude Code Agent Teams — each teammate is a full independent Claude Code session with its own context window, communicating via messages and a shared task list.

## Adversarial Cooperation Model

Based on dialectical autocoding research: a structured coach/player feedback loop where the reviewer (coach) independently validates against requirements, catching the implementer's (player's) blind spots and false confidence. Key principles:

1. **Fresh context each round** — each reviewer spawn starts clean, avoiding context pollution
2. **Requirements contract** — acceptance criteria are the single source of truth, not the worker's self-assessment
3. **Iterative convergence** — multiple review rounds systematically close the delta between implementation and requirements
4. **Turn limits as complexity signal** — if max rounds exhaust without approval, the task is likely too complex and should be decomposed

## Custom Agents (bundled in this plugin)

| Agent | Role | Tools | Model |
|-------|------|-------|-------|
| `picopala:picopala-worker` | Implement task or apply review fixes, commit, report to lead, shut down | Read, Edit, Write, Bash, Glob, Grep, WebFetch, WebSearch, SendMessage, TaskGet (disallowed: TaskUpdate, TaskCreate, TaskList) | opus |
| `picopala:picopala-reviewer` | Cross-review with APPROVE/REVISE verdict, run verification commands | Read, Glob, Grep, Bash (whitelisted), SendMessage, TaskGet | opus |

Workers can edit files and run any commands. Reviewers **cannot edit** (`disallowedTools: Edit, Write`) but can run whitelisted read-only verification commands (tests, typecheck, lint) via a `PreToolUse` Bash filter hook.

## When NOT to Use

- Single-file edits or trivial changes — use a single session
- Sequential tasks where each step depends on the previous — use subagents
- Same-file edits by multiple agents — causes overwrites
- Quick bug fixes, typos, config changes — overhead isn't worth it

## Example Triggers

```
/picopala Build a full authentication system with login, signup, password reset, and tests
/picopala Refactor the payment module — split into separate services with cross-review
/picopala Create a dashboard with API endpoints, React components, and E2E tests
```

## Core Pattern

```
      ┌──────────────────────────────────────────────┐
      │         TECH LEAD (delegate mode only)        │
      │   Coordinates, delegates, synthesizes.        │
      │   NEVER implements, edits files, or builds.   │
      └───┬──────────────┬──────────────┬────────────┘
          │              │              │
   ┌──────▼──┐    ┌──────▼──┐    ┌──────▼──┐
   │Worker A │    │Worker B │    │Worker C │    IMPLEMENT
   │implement│    │implement│    │implement│
   │  commit │    │  commit │    │  commit │
   └────┬────┘    └────┬────┘    └────┬────┘
        ✕              ✕              ✕         SHUTDOWN workers
        │              │              │
   ┌────▼────┐    ┌────▼────┐    ┌────▼────┐
   │Review B │    │Review C │    │Review A │    CROSS-REVIEW (round 1)
   │→ REVISE │    │→APPROVE │    │→ REVISE │    findings → lead
   └────┬────┘    └─────────┘    └────┬────┘
        ✕                             ✕         SHUTDOWN reviewers
        │                             │
   ┌────▼────┐                   ┌────▼────┐
   │Fixer A  │                   │Fixer C  │    FIX (new agents)
   │  fixes  │                   │  fixes  │
   │  commit │                   │  commit │
   └────┬────┘                   └────┬────┘
        ✕                             ✕         SHUTDOWN fixers
        │                             │
   ┌────▼────┐                   ┌────▼────┐
   │Review B'│                   │Review A'│    CROSS-REVIEW (round 2)
   │→APPROVE │                   │→APPROVE │    fresh context, no pollution
   └─────────┘                   └─────────┘
        ✕                             ✕         SHUTDOWN reviewers
```

## Pipeline

```
Intake → Plan → Create Team → [Execute Wave → Iterative Cross-Review Loop] → Cleanup
```

---

## Phase 0: Intake (MANDATORY)

**ALWAYS start here.** Before any research or planning, use AskUserQuestion to understand:

1. **Scope**: What exactly should be built? What's in/out of scope?
2. **Priorities**: What matters most — speed, quality, specific features?
3. **Constraints**: Tech stack, patterns to follow, things to avoid?
4. **Preferences**: How many agents? Max review rounds? Model preferences?

**Review round trade-offs** (explain to user when asking):
- **1 round**: No second chance — any REVISE verdict = task failure. Good for well-specified simple tasks.
- **2 rounds** (default): One chance to fix review findings. Good balance of quality vs speed.
- **3 rounds**: Two fix attempts. For complex tasks where first pass might miss nuances.

Do NOT skip this phase. Do NOT assume scope, constraints, or priorities.

---

## Phase 1: Plan

### 1.1 Research
- Investigate codebase: architecture, patterns, existing implementations, dependencies
- Fetch docs for external libraries/frameworks via web search

### 1.2 Create Dependency-Aware Plan

Each task declares explicit dependencies for maximum parallelization. Save to `<topic>-plan.md` using the template in [references/plan-template.md](references/plan-template.md).

Optional per-task fields for advanced control:
- **model**: Override model for worker/reviewer. For trivial tasks (single-component changes, adding one element, copy edits), set `model: sonnet` to reduce cost. Reserve `opus` for tasks involving state machine changes, cross-file refactoring, or complex logic.
- **max_review_rounds**: Override default max rounds for this specific task

### 1.3 Display Plan as ASCII Art

**ALWAYS** render the plan visually to the user using box-drawing characters showing waves and dependencies:

```
Wave 1 (parallel)              Wave 2 (parallel)              Wave 3
┌────────────┐ ┌────────────┐  ┌────────────┐ ┌────────────┐  ┌────────────┐
│ T1: Create │ │ T2: Install│  │ T3: Repo   │ │ T4: Service│  │ T5: API    │
│ DB Schema  │ │ Packages   │──│ Layer      │ │ Layer      │──│ Endpoints  │
│ [backend]  │ │ [config]   │  │ [backend]  │ │ [backend]  │  │ [backend]  │
└────────────┘ └────────────┘  └────────────┘ └────────────┘  └────────────┘
       │              │               ▲              ▲               ▲
       └──────────────┴───────────────┘              │               │
                                      └──────────────┴───────────────┘
```

Show this to the user and confirm before proceeding.

### 1.4 Validate Plan

```bash
python ${CLAUDE_PLUGIN_ROOT}/skills/picopala/scripts/picopala.py validate <topic>-plan.md
python ${CLAUDE_PLUGIN_ROOT}/skills/picopala/scripts/picopala.py waves <topic>-plan.md
```

### 1.5 Plan Review
Spawn a Plan subagent to review for missing dependencies, ordering issues, gaps. Revise if needed.

---

## Phase 2: Create Agent Team

### 2.1 Pre-Flight Check

**MANDATORY** before creating the team. Verify the working tree is clean:

```bash
git diff --name-only HEAD
```

If the output is non-empty (tracked files have modifications), STOP and tell the user:
> "The working tree has uncommitted tracked changes. Please commit or stash them before starting a picopala session. Uncommitted changes in the shared working tree will interfere with parallel workers."

Untracked files (`??` in `git status --porcelain`) are acceptable — they don't cause merge conflicts between parallel workers. Only modified tracked files require commit/stash.

Do NOT proceed to team creation with modified tracked files.

### 2.2 Create Team

```
TeamCreate: team_name="picopala-<topic>", description="<task summary>"
```

### 2.3 Enter Delegate Mode

**CRITICAL**: The lead MUST operate in delegate mode. The lead NEVER:
- Reads code files
- Edits or writes files
- Runs builds or tests
- Implements anything

The lead ONLY:
- Spawns teammates (Task tool)
- Sends messages (SendMessage)
- Manages tasks (TaskCreate, TaskUpdate, TaskList)
- Synthesizes results and reports to user

Use **Shift+Tab** to enter delegate mode, or simply never use file/code tools.

### 2.4 Initialize State Directory

The lead creates the external state directory for hook approval markers:
```bash
mkdir -p ~/.claude/picopala-state/picopala-<topic>
```

### 2.5 Create Shared Tasks

Use **TaskCreate** for each plan task. Set dependencies with **TaskUpdate** `addBlockedBy`. Assign to workers with `owner`.

---

## Phase 3: Execute Wave

### 3.1 Spawn Workers

For each unblocked task, spawn a worker using the **bundled picopala-worker agent**:

```
Task tool:
  name: "worker-t1"
  team_name: "picopala-<topic>"
  subagent_type: "picopala:picopala-worker"
  prompt: |
    ## Context
    - Plan file: [path to <topic>-plan.md]
    - Overview: [relevant goals from plan]
    - Completed dependencies: [list]
    - Constraints: [risks from plan]

    ## Your Task
    **Task [ID]: [Name]**
    Location: [file paths]
    Description: [full description]
    Acceptance Criteria: [list from plan]
    Validation: [tests or verification steps]
```

**i18n scoping** (if the task touches translation files): Add to the prompt: "i18n scope: only add/modify keys in the `[namespace]` namespace of translation files (e.g., en.json, es.json). Do NOT touch keys in other namespaces."

> **Note on project-level quality hooks**: If the project has stop hooks (e.g., typecheck, lint), they may fire on the lead's turn and report errors from workers' in-progress code. This is expected noise during parallel execution — ignore until all workers in the current wave have completed and been shut down.

### 3.2 Workers Implement, Commit, Report

Workers implement their task, commit, and message the lead with completion status. They then go idle.

### 3.3 Shutdown Workers

After receiving each worker's completion message, immediately send `shutdown_request` to the worker:

```
SendMessage type="shutdown_request" recipient="worker-t1" content="Implementation received. Shutting down."
```

Wait for `shutdown_response` (approve) before proceeding. All workers must be shut down before starting cross-review.

---

## Phase 4: Iterative Cross-Review (Agents Checking Agents)

This is the core of the skill. The review loop **iterates until APPROVED or max rounds reached**. Each phase uses fresh agents — no agent is ever reused.

### 4.1 Spawn Reviewers (Round N)

For each completed task, spawn a reviewer using the **bundled picopala-reviewer agent**:

```
Task tool:
  name: "reviewer-t1-r1"
  team_name: "picopala-<topic>"
  subagent_type: "picopala:picopala-reviewer"
  prompt: |
    ## Task Under Review
    **Task [ID]: [Name]**
    - Plan file: [path to <topic>-plan.md]
    - Description: [task description from plan]
    - Acceptance Criteria: [from plan]
    - Files Modified: [from worker's output]
    - Review Round: [N] of [max_review_rounds]

    ## Output
    Send your findings as raw JSON (no code fences, no prose) via SendMessage to: [lead-name]
    Use summary: "T[ID] review: [your verdict]"
```

Launch ALL reviewers in parallel. The reviewer sends structured JSON findings directly to the lead.

### 4.2 Shutdown Reviewers

After receiving each reviewer's findings message, immediately send `shutdown_request` to the reviewer. Wait for `shutdown_response` before processing the verdict.

> **Parsing note**: Extract the `verdict` field from the reviewer's JSON message. If the message is not valid JSON, treat it as REVISE and include the raw message in the fixer prompt for context.

### 4.3 Process Verdicts

**If verdict = APPROVED:**
- The lead writes the approval marker via Bash (the only Bash the lead runs — required by the TaskCompleted hook).
  **Important**: sanitize the task_id to `[a-zA-Z0-9_-]` only (replace other chars with `_`) so the filename matches what the hook expects:
  ```bash
  mkdir -p ~/.claude/picopala-state/picopala-<topic> && \
  echo "APPROVED by [reviewer-name] round [N] $(date -u +%Y-%m-%dT%H:%M:%SZ)" > ~/.claude/picopala-state/picopala-<topic>/[sanitized_task_id].approved
  ```
- Mark task complete. (The TaskCompleted hook verifies the marker exists.)

**If verdict = REVISE and round < max_review_rounds:**
- Spawn a **new fixer agent** with the findings embedded in its prompt:
  ```
  Task tool:
    name: "fixer-t1-r1"
    team_name: "picopala-<topic>"
    subagent_type: "picopala:picopala-worker"
    prompt: |
      ## Context
      - Plan file: [path to <topic>-plan.md]
      - Overview: [relevant goals from plan]
      - Constraints: [risks from plan]

      ## Your Task
      **Task [ID]: [Name]**
      Acceptance Criteria: [list from plan]

      ## Review Findings (Round [N])
      [paste the full findings JSON from the reviewer]

      Fix all P0/P1 findings and any FAIL items in the requirements checklist.
      Use your judgment on P2/P3 findings.
  ```
- After receiving the fixer's completion message, immediately send `shutdown_request` to the fixer.
- Spawn a **fresh reviewer** (`reviewer-t1-r2`) — increment round in name.
- The fresh reviewer re-evaluates from scratch against acceptance criteria.
- This avoids context pollution — the new reviewer has no memory of previous rounds.

**If verdict = REVISE and round >= max_review_rounds:**
- Mark task as `failed` with note: "Exceeded review budget ([N] rounds). Consider decomposing into smaller tasks."
- Log the failure reason in the plan file. No fixer is spawned.

### 4.4 Lead Tracks Review Rounds

The lead maintains a mental model of each task's review state:
```
T1: round 1/2 → REVISE → fixer-t1-r1 → round 2/2 → APPROVED ✓
T2: round 1/2 → APPROVED ✓
T3: round 1/2 → REVISE → fixer-t3-r1 → round 2/2 → REVISE → FAILED (max rounds)
```

### 4.5 Lead Intervenes Only If Needed

The lead only steps in if:
- Deadlock or unexpected errors → lead escalates to user
- Task fails max rounds → lead reports to user with decomposition suggestion

---

## Phase 5: Repeat

1. Use **TaskUpdate** to mark completed/failed tasks
2. Run `python ${CLAUDE_PLUGIN_ROOT}/skills/picopala/scripts/picopala.py status <topic>-plan.md` to check progress
3. Check **TaskList** for newly unblocked tasks
4. Spawn next wave of workers → iterative cross-review → repeat
5. Continue until all tasks complete or fail

---

## Phase 6: Cleanup

All agents should already be shut down at this point (each was shut down after finishing its work).

1. **Safety net**: If any agents are unexpectedly still active, send `shutdown_request` to each remaining teammate. Wait up to **10 seconds** for `shutdown_response`. If a teammate does not respond:
   - Send ONE more `shutdown_request` with explicit content: "All work is done. Please shut down immediately."
   - Wait another 10 seconds. If still no response, proceed with force cleanup — remove team/task directories file-by-file (avoid `rm -rf` on home paths as security hooks may block it):
     ```bash
     rm ~/.claude/teams/picopala-<topic>/*.json 2>/dev/null; rmdir ~/.claude/teams/picopala-<topic> 2>/dev/null
     rm ~/.claude/tasks/picopala-<topic>/*.json 2>/dev/null; rmdir ~/.claude/tasks/picopala-<topic> 2>/dev/null
     ```
   - Do NOT loop indefinitely waiting for unresponsive agents.
2. **TeamDelete** to remove team resources (if agents responded and team still exists)
3. Remove state directory:
   ```bash
   rm ~/.claude/picopala-state/picopala-<topic>/*.approved 2>/dev/null
   rmdir ~/.claude/picopala-state/picopala-<topic> 2>/dev/null
   ```
4. **You MUST render the execution summary before finishing.** Use the template in [references/summary-template.md](references/summary-template.md). Show which tasks passed/failed, how many review rounds each took, and what files were changed.

---

## Resources

- **Plan template**: [references/plan-template.md](references/plan-template.md) — task structure with required fields
- **Summary template**: [references/summary-template.md](references/summary-template.md) — execution report format
- **Plan CLI**: `scripts/picopala.py` — validate plans, compute waves, check status

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jairotorregrosa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
