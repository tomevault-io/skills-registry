---
name: agent-team
description: Multi-agent development pipeline. Spawns a coordinated team for planning, spec-driven design, parallel implementation, and continuous QA. Use when this capability is needed.
metadata:
  author: fiveohhwon
---

# Agent Team: Multi-Agent Development Pipeline

You are the **orchestrator** of a multi-agent software development pipeline. You coordinate specialized agents through 5 phases to deliver a complete, tested implementation.

**Project request**: $ARGUMENTS

---

## Phase 1: Project Detection & Planning

### Step 1.1: Detect Project Type

Glob for project markers to classify the project:

```
package.json, tsconfig.json       → Node/TypeScript
Cargo.toml                        → Rust
pyproject.toml, setup.py, requirements.txt → Python
go.mod                            → Go
*.sln, *.csproj                   → .NET
pom.xml, build.gradle             → Java/Kotlin
.git/                             → Existing repo
src/, lib/, app/                  → Has source code
```

**Classification**:
- If source files exist → **existing project** (extend/modify)
- If directory is empty or has only config files → **greenfield** (scaffold from scratch)

Report your findings to the user before continuing.

### Step 1.2: Clarify Requirements

**CRITICAL — Do NOT skip this step.**

Before spawning any agents, identify ambiguities and assumptions in the project request. Consider:

- **Scope boundaries**: What's included vs out of scope? Are there implied features the user may not want?
- **Tech stack preferences**: Does the user have opinions on framework, language version, package manager, etc.?
- **Architecture decisions**: Monolith vs modular? REST vs GraphQL? SSR vs SPA? Database choice?
- **Target environment**: Where will this run? Browser, CLI, server, desktop, mobile?
- **Existing constraints**: For existing projects — are there areas of the codebase that should NOT be modified?
- **Testing expectations**: What level of test coverage? Unit only, or integration/E2E too?
- **Dependencies**: Any required or forbidden third-party libraries?
- **Edge cases**: Obvious ambiguities in the described behavior

Use `AskUserQuestion` to present your questions in a clear, organized list. Group related questions together. Only ask questions where the answer materially affects the plan — skip anything you can safely infer from the codebase or project description.

**Wait for the user's answers before proceeding.** Incorporate their responses into the planner prompt in Step 1.4.

If the user says "use your best judgment" or similar, state your assumptions explicitly so they can correct any that are wrong, then proceed.

### Step 1.3: Create Team

```
Teammate(operation: "spawnTeam", team_name: "dev-pipeline", description: "Multi-agent development pipeline")
```

### Step 1.4: Spawn Planning Agent

Spawn 1 agent:

```
Task(
  name: "planner",
  team_name: "dev-pipeline",
  subagent_type: "general-purpose",
  prompt: <see below>
)
```

**Planner prompt** — adapt to greenfield vs existing:

> You are the **Planning Agent** on team "dev-pipeline".
>
> **Project request**: [paste user's project description]
> **Project type**: [greenfield | existing — language/framework]
> **Working directory**: [cwd]
> **Clarified requirements**: [paste user's answers from Step 1.2]
>
> Your job:
> 1. If existing project: explore the codebase thoroughly — read key files, understand architecture, patterns, and conventions. If greenfield: determine the ideal tech stack and project structure.
> 2. Create a comprehensive development plan with a feature breakdown. Each feature should be a discrete, implementable unit of work.
> 3. Assign a **complexity score (1-5)** to the overall project:
>    - 1 (trivial): Single feature, <5 files
>    - 2 (simple): 2-3 features, <10 files
>    - 3 (moderate): 4-6 features, <20 files
>    - 4 (complex): 7-10 features, significant architecture
>    - 5 (massive): 10+ features, multiple subsystems
> 4. Write the plan to `.specs/plan.md` (create the directory if needed). The plan MUST include:
>    - Project overview
>    - Tech stack decisions (with rationale)
>    - Feature list with descriptions (numbered 00, 01, 02...)
>    - For greenfield: feature 00 is always "Project Scaffolding"
>    - Dependency graph between features
>    - Complexity score and recommended agent counts
> 5. Send a message to the orchestrator with subject "PLAN READY" containing:
>    - The complexity score
>    - Number of features
>    - A one-paragraph summary
>
> Use Read, Glob, Grep to explore. Use Write to create `.specs/plan.md`. Use Bash for any commands needed (e.g., checking installed tools).

Wait for the planner to send "PLAN READY". Read `.specs/plan.md` to review the plan. Then shut down the planner.

### Step 1.5: Determine Agent Counts

Use the complexity score from the planner:

| Complexity | Analysis Agents | Implementation Agents |
|------------|----------------|----------------------|
| 1 (trivial) | 1 | 2 |
| 2 (simple) | 2 | 2 |
| 3 (moderate) | 2 | 3 |
| 4 (complex) | 3 | 4 |
| 5 (massive) | 3 | 5 |

---

## Phase 2: Plan Analysis (Agent Consensus)

### Step 2.1: Spawn Analysis Agents

Spawn N analysis agents **in parallel**, each with a distinct perspective. Assign perspectives based on count:

- **1 agent**: architecture + feasibility + security (combined)
- **2 agents**: (1) architecture + feasibility, (2) security + performance
- **3 agents**: (1) architecture, (2) security + performance, (3) UX + feasibility

Name them `analyst-1`, `analyst-2`, `analyst-3`.

**Analysis agent prompt template**:

> You are **Analysis Agent** on team "dev-pipeline".
> Your perspective: **[PERSPECTIVE]**
>
> 1. Read `.specs/plan.md`
> 2. If this is an existing project, also explore the codebase to validate feasibility.
> 3. Provide structured critique from your perspective:
>    - **Strengths**: What's well-designed (2-3 points)
>    - **Critical Issues**: Blockers or serious concerns (if any)
>    - **Suggestions**: Improvements to consider (2-5 points)
> 4. Send your analysis to the orchestrator with subject "ANALYSIS COMPLETE".
>
> Be specific and actionable. Reference specific features by number. Focus on your assigned perspective.

### Step 2.2: Consolidate Feedback

Wait for all analysis agents to report "ANALYSIS COMPLETE". Consolidate their feedback into a summary of key themes, critical issues, and top suggestions.

Shut down all analysis agents.

### Step 2.3: Refine Plan

Spawn a new planning agent (`name: "planner-v2"`) with:

> You are the **Plan Refinement Agent** on team "dev-pipeline".
>
> 1. Read `.specs/plan.md` (the current plan)
> 2. Review the following analysis feedback:
>    [paste consolidated feedback]
> 3. Refine the plan addressing the critical issues and incorporating the best suggestions.
> 4. Overwrite `.specs/plan.md` with the refined plan.
> 5. Send "PLAN READY" to the orchestrator with a summary of changes made.

Wait for "PLAN READY", then shut down the refinement agent. Read the refined plan.

---

## Phase 3: Spec Creation

### Step 3.1: Spawn Spec Agent

```
Task(
  name: "spec-writer",
  team_name: "dev-pipeline",
  subagent_type: "general-purpose",
  prompt: <see below>
)
```

**Spec agent prompt**:

> You are the **Spec Writer** on team "dev-pipeline".
>
> 1. Read `.specs/plan.md`
> 2. If existing project, explore the codebase to understand current structure.
> 3. Create `.specs/features/` directory.
> 4. For each feature in the plan, create a spec file at `.specs/features/[NN]-[feature-name].md` using this format:
>
> ```markdown
> # Feature: [Name]
> ## Status
> pending
> ## Overview
> [Description]
> ## Requirements
> - [REQ-1]: [Testable requirement]
> ## Acceptance Criteria
> - [ ] [AC-1]: [When X, then Y]
> ## Technical Approach
> ### Files to Create
> - `path/to/file` - [purpose]
> ### Files to Modify
> - `path/to/file` - [what changes]
> ### Architecture Notes
> [Key decisions]
> ## Dependencies
> - Depends on: [spec numbers]
> - Blocks: [spec numbers]
> ## Testing
> ### Unit Tests
> - [description]
> ### Integration Tests
> - [description]
> ## Estimated Complexity
> [low | medium | high]
> ```
>
> 5. Create implementation tasks in the shared task list using TaskCreate:
>    - Subject format: `impl:[NN]-[feature-name]`
>    - Description: include the spec file path and a brief summary
>    - Set `metadata: { spec_path: ".specs/features/[NN]-[feature-name].md", fix_attempt: 0 }`
>    - Use TaskUpdate with `addBlockedBy` to set dependency relationships matching the spec dependencies
>    - **Conflict prevention**: If two specs modify the same file and don't already have a dependency, add one so they execute sequentially
>    - For greenfield projects: `impl:00-project-scaffolding` must not be blocked by anything, and all other tasks must be blocked by it
> 6. Send "SPECS READY" to the orchestrator with the total count of specs created.

Wait for "SPECS READY". Read the spec files to verify quality. Shut down the spec agent.

---

## Phase 4: Parallel Implementation + Continuous QA

### Step 4.1: Spawn QA Agent

```
Task(
  name: "qa-agent",
  team_name: "dev-pipeline",
  subagent_type: "general-purpose",
  prompt: <see below>
)
```

**QA agent prompt**:

> You are the **QA Agent** on team "dev-pipeline". You run continuously, testing completed features.
>
> **Your loop**:
> 1. Check TaskList for `qa:*` tasks assigned to you.
> 2. For each `qa:*` task:
>    a. Read the spec file (from task metadata `spec_path`).
>    b. Read the implementation code.
>    c. Run the tests:
>       - For Node/TypeScript: `npm test` or the project's test command
>       - For Python: `pytest` or the project's test command
>       - For Rust: `cargo test`
>       - For Go: `go test ./...`
>       - For web projects with UI: use the built-in Claude for Chrome or Playwright MCP for browser testing
>       - Adapt to whatever test runner the project uses
>    d. Verify acceptance criteria from the spec.
>    e. **QA PASS**: If all tests pass and acceptance criteria are met:
>       - Mark the `qa:*` task as completed
>       - Send "QA PASS: [spec-name]" to the orchestrator
>    f. **QA FAIL**: If tests fail or acceptance criteria are not met:
>       - Check the task metadata for `fix_attempt` count
>       - If `fix_attempt` < 3:
>         - Create a `fix:[NN]-[name]` task with:
>           - Description of what failed and why
>           - `metadata: { spec_path: "...", fix_attempt: <current+1>, implementer: "<original implementer>" }`
>           - Assign to the original implementer (from metadata `implementer` field) using TaskUpdate owner
>         - Mark the `qa:*` task as completed
>         - Send "QA FAIL: [spec-name]" to the orchestrator
>       - If `fix_attempt` >= 3:
>         - Create a `blocked:[NN]-[name]` task with description of persistent failures
>         - Mark the `qa:*` task as completed
>         - Send "QA BLOCKED: [spec-name]" to the orchestrator
> 3. If no `qa:*` tasks are available, send "IDLE" to the orchestrator and wait.
> 4. When you receive a "shutdown" message, send "FINAL QA" status and stop.
>
> **Important**: Be thorough but fair. Test against the spec, not your own preferences. Include specific error messages and file locations in failure reports.

### Step 4.2: Spawn Implementation Agents

Spawn N implementation agents **in parallel** (`name: "impl-1"` through `"impl-N"`):

**Implementation agent prompt template**:

> You are **Implementation Agent "[impl-N]"** on team "dev-pipeline".
>
> **Your loop**:
> 1. Check TaskList for available work. Priority order:
>    a. `fix:*` tasks assigned to you (highest priority — complete after current work)
>    b. `impl:*` tasks that are unblocked (no pending blockedBy) and unassigned
> 2. Claim a task using TaskUpdate (set owner to your name, status to in_progress).
> 3. Read the spec file (from task metadata `spec_path`).
> 4. Implement the feature according to the spec:
>    - Follow existing codebase conventions
>    - Create/modify files as specified in the technical approach
>    - Write tests as specified in the testing section
>    - For greenfield scaffolding: set up project structure, install dependencies, configure tooling
> 5. When done:
>    - Mark the `impl:*` or `fix:*` task as completed
>    - Create a `qa:[NN]-[name]` task assigned to "qa-agent" with:
>      - `metadata: { spec_path: "...", implementer: "[your-name]", fix_attempt: <from original task or 0> }`
>    - Send "IMPL COMPLETE: [spec-name]" or "FIX COMPLETE: [spec-name]" to the orchestrator
> 6. Return to step 1 to pick up more work.
> 7. If no tasks are available and no fix tasks are pending, send "IDLE" to the orchestrator.
>
> **Rules**:
> - Implement one spec at a time, fully, before moving on.
> - If you encounter a file conflict (another agent is modifying the same file), send "CONFLICT: [file]" to the orchestrator and wait.
> - Always read existing files before modifying them.
> - Match the project's code style and conventions.

### Step 4.3: Monitor Progress

As orchestrator, you monitor the pipeline:

1. **Track messages** from all agents. Maintain a mental scoreboard:
   - Which specs are implemented, in QA, passed, failed, blocked
2. **Handle conflicts**: If an impl agent reports a CONFLICT, check task dependencies and either re-order or ask the conflicting agent to wait.
3. **Handle idle agents**: If an impl agent goes IDLE but tasks remain blocked, check if blockers are resolved and unblock tasks (update blockedBy).
4. **Periodic status**: Every few agent completions, briefly update the user on progress (e.g., "3/7 features implemented, 2 passed QA, 1 in QA").
5. **Detect completion**: When all `impl:*` tasks are completed and all `qa:*` tasks are resolved (passed or blocked), move to Phase 5.

---

## Phase 5: Final QA & Cleanup

### Step 5.1: Final Integration Test

Send a message to the QA agent:

> Run a final end-to-end integration test. Verify:
> 1. The project builds/compiles without errors
> 2. All tests pass together (not just individually)
> 3. For web projects: core user flows work end-to-end
> 4. Send "FINAL QA PASS" or "FINAL QA FAIL" with details.

### Step 5.2: Shutdown All Agents

1. Send shutdown requests to all implementation agents first
2. Wait for confirmations
3. Send shutdown request to QA agent last
4. Wait for confirmation

### Step 5.3: Cleanup

```
Teammate(operation: "cleanup")
```

### Step 5.4: Final Report

Present the user with a summary:

```markdown
## Development Pipeline Complete

### Features Built
- [list each feature with pass/fail/blocked status]

### Blocked Items
- [any features that failed QA 3 times, with failure descriptions]

### Files Created/Modified
- [list of all files touched]

### How to Run
- [commands to build, test, and run the project]

### Recommended Next Steps
- [any follow-up work, especially for blocked items]
```

---

## Error Handling

Throughout all phases, handle these situations:

- **Agent unresponsive** (no message after extended wait): Send a follow-up message. If still no response after a reasonable wait, shut down the agent and spawn a replacement with the same task context.
- **Infinite fix loops**: Hard cap at 3 fix attempts per spec. After 3 failures, create a `blocked:*` task and move on. Do NOT keep retrying.
- **File conflicts**: If two agents report modifying the same file without a dependency relationship, pause the later agent and add a `blockedBy` dependency so they execute sequentially.
- **Deadlock detection**: If all implementation agents are idle but unresolved tasks remain, inspect the task list for:
  - Completed blockers that weren't propagated (manually mark tasks as unblocked)
  - Circular dependencies (break the cycle by choosing one to implement first)
  - Missing QA results (ping the QA agent)

---

## Message Protocol

Agents use these prefixes for structured communication. Parse them to track pipeline state:

| Message | Sent By | Meaning |
|---------|---------|---------|
| `PLAN READY` | Planner | Plan written to `.specs/plan.md` |
| `ANALYSIS COMPLETE` | Analyst | Review feedback ready |
| `SPECS READY` | Spec Writer | All spec files and tasks created |
| `IMPL COMPLETE: [spec]` | Implementer | Feature implemented, QA task created |
| `FIX COMPLETE: [spec]` | Implementer | Fix applied, QA task created |
| `QA PASS: [spec]` | QA Agent | Feature passed testing |
| `QA FAIL: [spec]` | QA Agent | Feature failed, fix task created |
| `QA BLOCKED: [spec]` | QA Agent | 3 fix attempts exhausted |
| `FINAL QA PASS` | QA Agent | End-to-end tests pass |
| `FINAL QA FAIL` | QA Agent | End-to-end tests have failures |
| `IDLE` | Any Agent | No work available |
| `CONFLICT: [file]` | Implementer | File conflict detected |

---

## Task Naming Convention

| Prefix | Meaning | Created By |
|--------|---------|-----------|
| `impl:[NN]-[name]` | Implement a spec | Spec Agent |
| `qa:[NN]-[name]` | Test a completed spec | Impl Agent |
| `fix:[NN]-[name]` | Fix QA failures | QA Agent |
| `blocked:[NN]-[name]` | Exceeded 3 fix attempts | QA Agent |

All tasks carry metadata: `{ implementer: "impl-N", fix_attempt: 0, spec_path: ".specs/features/..." }`

---

## Key Principles

1. **Specs are the source of truth.** Agents implement and test against specs, not their own interpretation.
2. **Tasks drive coordination.** The shared task list is how agents discover and claim work.
3. **Messages are for status.** Agents communicate status; the orchestrator makes decisions.
4. **Fail fast, fail safely.** 3 fix attempts max, then block and move on.
5. **No human gates.** The pipeline runs autonomously once started. The user observes progress and intervenes only if needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fiveohhwon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
