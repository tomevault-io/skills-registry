---
name: no-slacking
description: > Use when this capability is needed.
metadata:
  author: Fly-Pluche
---

# No-Slacking Skill

This skill packages a durable harness for multi-session coding work. It turns
"build this app" into explicit project artifacts and a bounded session loop so a
fresh agent can continue where the previous one stopped.

## Use This Skill For

- Bootstrapping a repo from a spec so future agent sessions can continue with
  minimal human steering
- Repairing an AI-generated repo that lacks task tracking, handoff notes, or a
  repeatable startup command
- Converting a one-shot prompting workflow into a restartable checkpoint or
  continuous batch loop
- Installing the harness under `~/.codex/skills/` or `~/.claude/skills/`

Do not use it for tiny one-off edits that clearly fit in a single session.

## What The Harness Produces

When scaffolding a project, create or normalize these files in the target repo:

- `architecture.md` as the design baseline the user reviews first
- `task.json` as the source of truth for work still to do
- `progress.md` as the cross-session handoff log
- `init.sh` as an idempotent environment bootstrap
- `AGENTS.md` for Codex and/or `CLAUDE.md` for Claude Code
- `automation-logs/` only when the outer loop script is used

If the repo already uses `task-list.json`, `progress.txt`, or another stable
pair of names, keep those names when renaming would create more churn than
value. Preserve the invariants instead of forcing the default filenames.

## Operating Modes

1. **Bootstrap**: create harness files, decompose the spec, prepare the repo for
   future agent sessions
2. **Resume**: read the repo state, verify regressions, finish one or more
   tasks according to the current execution mode, and leave a clean handoff
3. **Repair**: harden an existing harness with vague tasks, missing instructions,
   or a broken `init.sh`
4. **Automate**: prefer a supervisor loop that repeatedly launches fresh coding
   sessions after one manual proving run

## Non-Negotiable Protocol

- No implementation before the user explicitly approves `architecture.md` and
  `task.json`
- A bare approval reply should unlock the plan and start the default
  post-approval execution batch unless the user explicitly narrows scope
- One task per commit
- Regression check before new work
- Never rewrite task text during normal coding sessions
- Only flip `passes` after verification
- Code, task status, and handoff notes move together
- If blocked, document and stop; do not fake completion

Read [references/harness-principles.md](references/harness-principles.md) when
you need the design rationale behind those rules.

## How To Use This Skill

### 1. Classify the repo state

- If the repo has no harness, read
  [references/initializer-workflow.md](references/initializer-workflow.md)
- If the harness exists and work should continue, read
  [references/coding-workflow.md](references/coding-workflow.md)
- If installation or CLI flags matter, read
  [references/platform-adapters.md](references/platform-adapters.md)

### 2. Bootstrap or normalize the environment

- Prefer `scripts/setup-harness.sh` when creating the initial scaffold
- Make `init.sh` idempotent; later sessions must be able to rerun it safely
- Preserve the repo's current conventions when adapting an existing project

### 3. Draft the architecture first

- Read the spec before deciding the stack or slicing the work
- Write `architecture.md` with the product scope, chosen stack, major modules,
  data model, external integrations, delivery phases, and explicit out-of-scope
  items
- Capture important assumptions and unresolved risks so the user can review
  them before coding begins

### 4. Build a restartable backlog

- Tasks should usually fit in one checkpoint-sized unit of work
- Put acceptance criteria into `steps`
- Order by dependency, not by surface area
- Store the backlog in `task.json` by default and include an approval status
  that stays pending until the user confirms the plan
- Include a small execution policy in `task.json` so the repo records what a
  bare approval should do next
- **NEVER** set `default_mode_after_approval` to `checkpoint`; use
  `continuous-batch` so a bare approval keeps the agent working through the
  full backlog. Set `default_tasks_per_run` to at least the total task count
  (50 is a safe default) so the agent does not stop mid-backlog.
- During bootstrap or repair, you may append missing tasks if the original
  backlog is incomplete; once normal coding starts, task definitions become
  immutable and only `passes` should change

### 5. Ask for approval and stop

- After writing `architecture.md` and `task.json`, summarize the plan and ask
  the user whether it is reasonable
- If the user asks for changes, revise the planning artifacts and ask again
- Do not start product implementation, continuous execution, or unattended
  automation while approval is still pending
- If the user's next reply is a bare approval like `approve`, `approved`,
  `looks good`, `go ahead`, `可以`, `同意`, `开始`, or `继续`, treat that as both
  plan approval and authorization to begin the repo's default post-approval
  execution policy

### 6. Run the bounded coding loop

- Read `architecture.md`, progress, git history, and the task file
- Start the environment
- Verify one or two previously passing flows
- Select one incomplete task
- Implement and test
- Update task status and the session log
- Commit atomically, then either continue to the next task or stop cleanly if blocked
- If approval was just granted by a bare approval reply, follow the repo's
  configured post-approval policy, which should usually be a supervisor loop
  for long-running work unless the user explicitly constrained the run

### 7. Automate conservatively

- Start with a manual proving run before unattended runs
- When using `automation-loop` mode, prefer small `--max-runs` values first
- Review logs between batches
- If multiple runs make no progress, repair the harness before continuing
- Prefer a scriptable supervisor loop over an oversized single interactive
  session for truly long-running work
- Treat sub-agents as an optional acceleration technique inside one session, not
  as the primary portable control plane across different coding agents
- In interactive sessions, queued tasks are only backlog state until the
  current prompt explicitly requests checkpoint mode or continuous batch mode
  and the plan has already been approved

## Skill-Specific Guidance

- Default artifact names are `architecture.md`, `task.json`, and `progress.md`
- Generate both `AGENTS.md` and `CLAUDE.md` when possible so the workflow lives
  in the repo, not only in the human prompt
- Keep generated project instructions concise and repo-specific; keep the
  reusable theory in this skill's reference files
- Use JSON for task tracking unless the repo already depends on another
  structured format
- Do not start unattended automation until `init.sh`, build/test commands, and
  at least one regression check are proven, and the plan approval is recorded

## Installation

- Codex-style personal skill dir: `~/.codex/skills/no-slacking`
- Claude Code personal skill dir: `~/.claude/skills/no-slacking`
- Use `scripts/install-skill.sh` to symlink or copy this folder into one or
  both locations

## Reference Map

- [references/harness-principles.md](references/harness-principles.md):
  why the harness is strict and what invariants matter
- [references/initializer-workflow.md](references/initializer-workflow.md):
  how to scaffold a repo and generate the initial backlog
- [references/coding-workflow.md](references/coding-workflow.md):
  checkpoint mode and continuous batch mode
- [references/platform-adapters.md](references/platform-adapters.md):
  current Codex / Claude Code installation paths and execution patterns

---
> Source: [Fly-Pluche/no-slacking](https://github.com/Fly-Pluche/no-slacking) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
