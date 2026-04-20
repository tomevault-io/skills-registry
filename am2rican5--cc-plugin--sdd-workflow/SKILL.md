---
name: am2rican5sdd-workflow
description: Orchestrate the full spec-driven development lifecycle through four phases (Specify, Plan, Implement, Validate). Supports three rigor levels and lightweight mode for small changes. Use when user says "spec driven development", "SDD workflow", "specify then implement", "spec first development", "write spec then build", or "develop with specification". Do NOT use for writing specs only (use spec-writer) or for validating existing code against specs (use spec-validator agent directly). Use when this capability is needed.
metadata:
  author: am2rican5
---

# SDD Workflow

## Critical Rules

- ALWAYS start with Phase 1 (Specify) — never skip to implementation without a spec
- NEVER proceed to the next phase until the current phase's artifact exists and is confirmed
- ALWAYS persist phase artifacts to files — memory is files, not conversation
- WHEN resuming an interrupted workflow, detect existing artifacts and resume from the correct phase
- NEVER let Phase 3 (Implement) start without both a spec AND a plan (unless in lightweight mode)
- WHEN the user wants to skip phases, explain the value of each phase but respect their decision if they insist

## Instructions

### Step 0: Detect Existing Workflow State

Before starting, check for existing artifacts from a previous session:

1. Look for `specs/<feature-name>.md` — if exists, Phase 1 is done
2. Look for `specs/<feature-name>.plan.md` — if exists, Phase 2 is done
3. Look for `specs/<feature-name>.validation.md` — if exists, Phase 4 is done

IF existing artifacts found:
- Show the user what exists and which phase to resume from
- Ask: "Found existing artifacts. Resume from Phase N, or start fresh?"

IF no artifacts found → proceed to Step 1.

### Step 1: Determine Rigor Level and Mode

Analyze the user's feature description to select the workflow mode:

**Lightweight Mode** (auto-detected for small changes):
- Change affects 1-2 files with clear scope
- User describes a bug fix, small tweak, or minor addition
- Workflow: Write acceptance criteria → Implement → Validate (skip formal plan)

**Full Mode** (default for everything else):
- Workflow: Specify → Plan → Implement → Validate

**Rigor Level** (see `references/rigor-levels.md` for details):
- **spec-first** (default): Spec guides initial development, may not be maintained
- **spec-anchored**: Spec maintained alongside code, both evolve together
- **spec-as-source**: Spec IS the source, code generated from it

Announce the selected mode and rigor level. If the user mentioned "API", "contract", "long-term", "team", or "shared", recommend spec-anchored and explain why.

If the user explicitly requests a specific rigor level, use it.

### Step 2: Phase 1 — Specify

**Goal:** Produce a spec file at `specs/<feature-name>.md`

**Option A: Delegate to spec-writer skill**
If the feature needs deep elicitation (complex, unclear, many stakeholders):
> "This feature would benefit from a detailed specification. I'll use the spec-writer skill to guide you through it."

Use the Skill tool to invoke `spec-writer` with the feature description.

**Option B: Inline spec creation**
For straightforward features where the user has already described clear requirements:
1. Draft a spec directly using the spec-writer format (Purpose, Rigor Level, Acceptance Criteria, Edge Cases, Constraints)
2. Present it for confirmation
3. Write to `specs/<feature-name>.md`

**Phase 1 is complete when** `specs/<feature-name>.md` exists and the user has confirmed it.

### Step 3: Phase 2 — Plan

**Goal:** Produce an implementation plan at `specs/<feature-name>.plan.md`

Invoke the spec-planner agent using the Task tool:

```
Task tool call:
  subagent_type: "spec-planner"
  prompt: "Read the spec at specs/<feature-name>.md and produce an implementation plan. Scan the codebase for existing patterns and relevant code. Output the plan to specs/<feature-name>.plan.md"
```

Wait for the agent to complete and read the generated plan.

Present the plan to the user:
> "Here's the implementation plan. Review the architecture decisions and task breakdown. Should we proceed, or adjust anything?"

**Phase 2 is complete when** the plan file exists and the user approves it.

**In lightweight mode:** Skip this phase. The acceptance criteria from the spec serve as the implementation guide.

### Step 4: Phase 3 — Implement

**Goal:** Build the feature according to the plan (or spec in lightweight mode)

This phase is user-driven. The skill provides structure, not automation:

1. Display the task list from the plan (or acceptance criteria in lightweight mode)
2. For each task:
   - Announce which task is being worked on
   - Implement the required changes
   - Mark the task complete
   - Briefly verify the change works
3. Track progress: "Completed N/M tasks"

**During implementation, watch for:**
- Requirements that don't match the plan → pause and suggest updating artifacts
- Missing acceptance criteria discovered during coding → note them for the validate phase
- Blockers or unclear requirements → pause and ask the user

**Phase 3 is complete when** all plan tasks (or acceptance criteria) are addressed.

### Step 5: Phase 4 — Validate

**Goal:** Verify implementation matches the spec. Produce a validation report.

Invoke the spec-validator agent using the Task tool:

```
Task tool call:
  subagent_type: "spec-validator"
  prompt: "Read the spec at specs/<feature-name>.md. Validate the implementation against all acceptance criteria and edge cases. Write the report to specs/<feature-name>.validation.md"
```

Wait for the agent to complete and read the validation report.

Present the report to the user with a summary:

**If PASS:** "All acceptance criteria met. Implementation is complete."

**If PARTIAL:** "N/M criteria met. Here's what's missing: [list]. Would you like to address these now?"

**If FAIL:** "Significant gaps found. Here are the issues: [list]. Let's fix these before considering this done."

For spec-anchored rigor: remind the user that the spec should be updated if implementation legitimately diverged from the original spec.

### Step 6: Completion

When all four phases are done:
1. Summarize what was built
2. List all artifacts created (spec, plan, validation report)
3. Note the rigor level and any maintenance implications
4. If spec-anchored: remind user to update spec when behavior changes in the future

## Examples

### Example 1: Full Workflow

```
User: "I want to add OAuth2 login to my app using spec-driven development"

SDD Workflow:
→ Detected: "login" + "OAuth2" = auth feature, multi-file change
→ Mode: Full workflow
→ Rigor: Recommending spec-anchored (auth is security-critical, shared)

Phase 1 (Specify): Delegating to spec-writer...
  [spec-writer guides user through OAuth2 spec creation]
  ✓ Spec written to specs/oauth2-login.md

Phase 2 (Plan): Invoking spec-planner agent...
  [agent analyzes codebase and generates plan]
  ✓ Plan written to specs/oauth2-login.plan.md
  "Here's the plan with 8 tasks. Review and approve?"

Phase 3 (Implement): Working through tasks...
  Task 1/8: Add OAuth2 client configuration ✓
  Task 2/8: Create callback endpoint ✓
  ...
  Task 8/8: Add session management ✓

Phase 4 (Validate): Invoking spec-validator agent...
  ✓ All 12 acceptance criteria met
  ✓ 5/6 edge cases covered (1 marked UNCLEAR - manual verification needed)
  Report: specs/oauth2-login.validation.md
```

### Example 2: Lightweight Mode

```
User: "Fix the off-by-one error in the pagination component"

SDD Workflow:
→ Detected: 1-2 files, bug fix, clear scope
→ Mode: Lightweight (skip formal plan)
→ Rigor: spec-first

Phase 1: Quick acceptance criteria:
  AC1: Given page 1 with 10 items per page, when requesting page 2, then items 11-20 are shown
  AC2: Given the last page, when displayed, then only remaining items shown (no duplicates)
  ✓ Spec written to specs/pagination-fix.md

Phase 3: Implementing fix...
  ✓ Fixed offset calculation in pagination.ts

Phase 4: Validating...
  ✓ Both acceptance criteria met
```

## Troubleshooting

### User wants to skip Specify phase
**Cause:** They feel it's overhead for a "simple" change.
**Solution:** Explain value: "Specs prevent rework by clarifying requirements upfront. Even 2-3 acceptance criteria help." If they insist, let them proceed but note the risk of rework.

### Plan doesn't match spec
**Cause:** The spec-planner agent missed acceptance criteria or misinterpreted constraints.
**Solution:** Phase 2 should be re-run. The plan must address every acceptance criterion in the spec. Review the spec for clarity if the agent consistently misinterprets it.

### Implementation diverges from plan
**Cause:** Reality doesn't match the plan — new constraints discovered during coding.
**Solution:** This is normal. Update the plan if the divergence is intentional. Flag it if accidental. The plan is a guide, not a contract.

### Validation finds gaps late
**Cause:** Acceptance criteria were met but edge cases were missed during implementation.
**Solution:** Better late than never. The gaps inform either code fixes or spec updates (if requirements changed during implementation).

### User doesn't know which rigor level
**Cause:** The three levels seem abstract without project context.
**Solution:** Default to spec-first. It has the lowest overhead and still provides value. Suggest upgrading later if the feature becomes long-lived.

### Session ends mid-workflow
**Cause:** User closes session or context window fills up.
**Solution:** Artifacts on disk allow resumption. Step 0 handles detection automatically on the next invocation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/am2rican5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
