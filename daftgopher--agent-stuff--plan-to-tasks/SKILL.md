---
name: plan-to-tasks
description: Convert a software feature plan into a structured JSON task list. Use when the user wants to transform a project plan or PRD into implementable tasks, break down a feature into actionable steps, or generate a prd.json file from a plan document. Triggers include "convert plan to tasks", "create tasks from plan", "generate task list", "break down this plan", or providing a path to a plan file in .agent/projects/. Use when this capability is needed.
metadata:
  author: daftgopher
---

# Plan to Tasks

Convert software feature plans into structured JSON task lists for agent implementation.

## Workflow

1. Read the plan file at `.agent/projects/<PROJECT_NAME>/<PLAN_FILE>.md`
2. Analyze the plan and break into implementable tasks
3. Identify any prerequisites requiring human action
4. Write task list to `.agent/projects/<PROJECT_NAME>/prd.json`
5. Write prerequisites (if any) to `.agent/projects/<PROJECT_NAME>/prerequisites.md`

## Task Format

See [references/task-creation-guidelines.md](references/task-creation-guidelines.md) for the complete JSON schema.

Each task includes:

- `id`: `<FEATURENAME>-<INCREMENTING_ID>`
- `branchName`: `<NOTICKET-featureName>` or `linear/<TICKETNAME>`
- `title`: Brief task description
- `implementationSteps`: Array of steps to implement
- `acceptanceCriteria`: Array of verification checks
- `passes`: Boolean (default false)

## Guidelines

**Tasks should be completable via code and terminal commands** - writing/editing files (JS, k8s YAML, Dockerfiles) and running commands.

**Frontend code requires UI validation tasks** - when React, Svelte, etc. are involved, include tasks for verifying work in the browser. Space these roughly every 5 tasks throughout the list.

**Include unit tests** - add vitest tests for functions and components that would benefit from testing.

**Human prerequisites go in prerequisites.md** - actions requiring human intervention (provisioning API tokens, setting up external infrastructure, creating VMs/k8s clusters) should not be tasks. Document these in `.agent/projects/<PROJECT_NAME>/prerequisites.md` as instructions for humans to complete first.

## Resources

### references/

- `task-creation-guidelines.md` - JSON schema and examples for task format

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daftgopher) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
