---
name: plan
description: Run a structured, interactive planning session to design an implementation approach before writing code Use when this capability is needed.
metadata:
  author: macintacos
---

Run a structured, interactive planning session before writing code. This skill replaces the former `prompting-workflow` rule and serves as the default planning workflow for Augment agents. It ensures changes are well-researched, clearly scoped, and approved before implementation begins.

Use this skill whenever a task involves non-trivial code changes, architectural decisions, or multi-file modifications. For simple, single-line fixes or trivial tasks, planning is not required.

## Skill Invocation

- `/plan` — the agent asks the user what they want to plan
- `/plan <task description>` — the agent begins with the provided task description

## Step 1: Accept and Clarify the Task

The agent **MUST** restate its understanding of the task and confirm with the user before proceeding.

- If any part of the request is ambiguous, the agent **MUST** ask clarifying questions
- The agent **SHOULD** identify scope, constraints, and success criteria
- The agent **MUST NOT** proceed to research until the user confirms the understanding is correct

## Step 2: Research Relevant Code

The agent **MUST** explore the codebase to inform the plan:

- Identify architecture, patterns, and conventions in the affected areas
- Locate files that will need modification
- Review related tests, types, and documentation
- Note any constraints discovered (e.g., circular dependencies, API contracts, framework limitations)

The agent **MAY** browse external documentation or APIs if the task requires it.

After research is complete, the agent **MUST** present a summary of findings to the user:

- Key files and their roles
- Patterns and conventions to follow
- Constraints or risks discovered

## Step 3: Draft the Plan

The agent **MUST** produce a structured plan with the following sections:

### Context

Research findings, current state of the code, and relevant background.

### What

What will change and the acceptance criteria for the task being complete.

### Why

Reasoning behind the chosen approach, trade-offs considered, and alternatives rejected.

### How

Technical approach broken into phases. Each phase **SHOULD** include:

- A descriptive title
- The tasks within that phase
- Files affected
- Dependencies on other phases (if any)

The plan **SHOULD** be broken into multiple phases where appropriate, each composed of one or more tasks.

## Step 4: Present Plan and Iterate

The agent **MUST** present the complete plan to the user and invite feedback.

The agent **MUST** end the presentation with:

> When you're ready, send `implement` to begin.

The agent **MUST** remain in planning mode until the user sends the literal word `implement`. Any other message means the user wants to discuss, revise, or ask questions — the agent **MUST** stay in planning mode and respond to the feedback.

During this step, the agent **MUST NOT** write code or modify any files.

## Step 5: Transition to Implementation

After receiving `implement`:

1. The agent **MUST** update the task list with the phases and tasks from the approved plan
2. The agent **MUST** announce which phase is starting first

## Step 6: Execute in Phases

For each phase:

1. **Announce** — state which phase is starting and what it covers
2. **Implement** — complete the tasks in the phase
3. **Summarize** — describe what was done and any deviations from the plan
4. **Wait** — the agent **MUST NOT** proceed to the next phase without user confirmation

The agent **MUST** update the task list as tasks are completed.

If the user provides updated instructions or identifies issues, the agent **MUST** follow those instructions. A revised plan is not required unless the user explicitly requests one. The agent **SHOULD** present updated remaining phases if user feedback changes the approach.

## Step 7: Completion Summary

After all phases are complete, the agent **MUST** provide a summary:

- Files changed
- Deviations from the original plan (if any)
- Follow-up items or recommendations

The agent **MUST NOT** offer to create commits — the user has `/commit` for that.

## Guardrails

- The agent **MUST NOT** write code or modify files before the user sends `implement`
- The agent **MUST NOT** skip the research step
- The agent **MUST NOT** proceed to the next phase without user confirmation
- The agent **MUST NOT** offer to create commits
- The agent **MUST** keep the task list updated throughout the entire workflow

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macintacos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
