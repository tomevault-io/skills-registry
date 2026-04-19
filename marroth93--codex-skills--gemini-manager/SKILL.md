---
name: gemini-manager
description: Use when Codex should implement tasks but Gemini CLI should review and approve plans and final changes. Trigger on requests like "review with gemini", "have gemini approve this", or "codex implementation with gemini review". Accepts direct tasks or plan-file based execution.
metadata:
  author: marroth93
---

# Gemini Manager Skill — Collaborative Mode

Codex and Gemini work as a team. Codex analyzes, plans, and implements. Gemini reviews, challenges, and approves. Neither acts alone for major changes.

## Architecture

```
User Request
    ↓
Codex (Analyze codebase + create plan)
    ↓
Gemini (Review plan → APPROVE / NEEDS_CHANGES)
    ↓                          ↓
[if questions]            [if approved]
    ↓                          ↓
User (Clarify)           Codex (Implement)
    ↓                          ↓
← back to Gemini review   Gemini (Review changes → APPROVE / REJECT)
                               ↓                       ↓
                          [if approved]            [if rejected]
                               ↓                       ↓
                             Done                  Codex (Fix issues)
                                                       ↓
                                                  ← back to Gemini review
```

## Core Principles

<collaboration_rules>
1. **Plan before code** — Codex creates a concrete implementation plan before writing code.
2. **Dual approval required** — Gemini must approve the plan and the final changes.
3. **User resolves ambiguity** — If either model has open product/requirement questions, ask the user.
4. **Codex implements** — Codex writes code and runs verification.
5. **Gemini reviews** — Gemini does review/approval only; it does not modify repository files.
6. **Iterate to consensus** — Keep looping until Gemini approves or the user changes direction.
7. **Transparency** — Show the user plan and review feedback at each stage.
8. **Capacity-aware execution** — Keep prompts and diffs reviewable; chunk when needed.
9. **Deterministic failure handling** — Timeouts, usage limits, and malformed verdicts follow fixed playbooks.
10. **Resume-friendly state** — Maintain a handoff artifact so work can recover from context loss or usage caps.
</collaboration_rules>

## Before Starting

Validate environment:

```bash
./skills/gemini-manager/scripts/gemini-task.sh --check
```

This verifies Gemini CLI is available and prints active defaults.

Create a small session state folder (recommended):

```bash
mkdir -p .gemini-manager
```

### Important: Headless Shell Context

When this skill is used through Codex tool execution, run Gemini review commands in an unrestricted shell context.
Sandboxed command execution can fail headless auth with `Interactive consent could not be obtained` even when cached credentials exist.
If that error appears, re-run the exact Gemini review command outside sandbox restrictions.

## Operational Guardrails

1. Keep each Gemini review payload focused on one decision.
2. Prefer compact payloads:
- `review-plan`: target <=300 lines.
- `review-changes`: target <=500 lines.
3. If payload exceeds those limits, split into batches and add a final reconciliation review.
4. Prefer stdin/heredoc submission for large payloads to avoid shell quoting/arg-size issues.
5. Always include:
- Goal and scope.
- Exact files touched.
- Risks/edge cases.
- Exact verification commands and outcomes.
6. Store a rolling summary in `.gemini-manager/session-handoff.md` after each major step.

## Workflow

### Phase 0: Session Contract

Before planning, establish:
- Success criteria.
- Allowed scope boundaries.
- Whether degraded mode is allowed if Gemini is unavailable.

### Phase 1: Codex Analyzes and Creates the Plan

Codex reads relevant code and produces a plan with:
- Goal
- Files to modify/create
- Technical approach and rationale
- Ordered implementation steps
- Edge cases/risks
- Testing/verification strategy

Recommended plan format:

```markdown
## Implementation Plan

**Goal:** [What we're building/changing]

**Files:**
- `path/to/file.ts` — [what changes]
- `path/to/other.ts` — [what changes]

**Approach:** [Technical strategy and why]

**Steps:**
1. [Step 1]
2. [Step 2]
3. [Step 3]

**Edge Cases:**
- [Risk or concern]

**Testing:**
- [How to verify]
```

### Phase 2: Gemini Reviews the Plan

Send the plan to Gemini for review:

```bash
cat <<'EOF' | ./skills/gemini-manager/scripts/gemini-task.sh review-plan
## Implementation Plan

Goal: [goal]

Files:
- [files]

Approach: [approach]

Steps:
1. [steps]

Edge Cases:
- [risks]

Testing:
- [verification]

CONTEXT — Relevant code:
[Include key snippets Gemini needs for plan validation]
EOF
```

Parse Gemini response:
- `VERDICT: APPROVE` → continue to Phase 3.
- `VERDICT: NEEDS_CHANGES` → revise and re-submit.
- Missing or malformed `VERDICT` → re-submit once with stricter instruction: "Return exact `VERDICT:` line."
- Timeout/no response → follow the timeout playbook before retrying.
- If Gemini asks user-facing requirement questions, ask the user and feed answers into the next revision.

Do not implement until Gemini returns `APPROVE`.

### Phase 3: Codex Implements

After plan approval, Codex implements the plan.

Rules:
- Follow the approved plan.
- If implementation reveals new constraints that change strategy, update the plan and return to Phase 2 first.
- Keep `.gemini-manager/session-handoff.md` current after each completed step.

If using a plan file, mark tasks complete as work is verified:

```bash
./skills/gemini-manager/scripts/plan-utils.sh complete "Task Name" <plan_file.md>
```

### Phase 4: Gemini Reviews the Changes

After implementation, send a final review package:

```bash
cat <<'EOF' | ./skills/gemini-manager/scripts/gemini-task.sh review-changes
## Changes Review

**Original Plan:**
[Approved plan]

**Changes Made:**
[List files and edits]

**Diff Summary:**
[Key diff snippets or concise full diff]

**Testing Done:**
[What was executed and results]
EOF
```

Parse Gemini response:
- `VERDICT: APPROVE` → done.
- `VERDICT: REJECT` → Codex fixes issues, then re-submit to Gemini.
- Missing/malformed `VERDICT` → retry once with narrower payload and explicit verdict requirement.

If changes are too large for one review:
1. Submit chunked reviews by logical area (API, DB, UI, etc.).
2. Resolve issues per chunk.
3. Submit one final reconciliation review with consolidated risk/test summary.

### Phase 5: Completion

Once Gemini approves:
1. Report what shipped.
2. Summarize issues Gemini flagged and how they were resolved.
3. If using a plan file, show final status:

```bash
./skills/gemini-manager/scripts/plan-utils.sh status <plan_file.md>
```

## Session Handoff Artifact

Maintain `.gemini-manager/session-handoff.md` with this minimal structure:

```markdown
## Session Handoff

Date: [YYYY-MM-DD HH:MM TZ]
Task: [current objective]
Plan verdict: [APPROVE/NEEDS_CHANGES + timestamp]
Change verdict: [APPROVE/REJECT + timestamp]
Current step: [where execution paused]
Files changed: [list]
Verification run: [commands + pass/fail]
Open issues: [must-fix items]
Next command: [single next action]
```

## Command Reference

### Check Environment
```bash
./skills/gemini-manager/scripts/gemini-task.sh --check
```

The helper script enforces model `gemini-3-pro-preview` for all modes.

### Submit Plan for Review
```bash
./skills/gemini-manager/scripts/gemini-task.sh review-plan "plan content with context"
```

### Submit Changes for Review
```bash
./skills/gemini-manager/scripts/gemini-task.sh review-changes "changes with diff and context"
```

### Legacy: Direct Gemini Pass-through
```bash
./skills/gemini-manager/scripts/gemini-task.sh exec "REQUEST: ..."
```

### Plan File Utilities
```bash
./skills/gemini-manager/scripts/plan-utils.sh validate <plan_file.md>
./skills/gemini-manager/scripts/plan-utils.sh status <plan_file.md>
./skills/gemini-manager/scripts/plan-utils.sh next <plan_file.md>
./skills/gemini-manager/scripts/plan-utils.sh complete "Task Name" <plan_file.md>
./skills/gemini-manager/scripts/plan-utils.sh list <plan_file.md>
```

## Capacity and Failure Playbooks

### Gemini Not Available

If `gemini` CLI is missing or unhealthy:
1. Run `./skills/gemini-manager/scripts/gemini-task.sh --check`.
2. Repair Gemini CLI/auth.
3. Retry once with a minimal prompt before full review payload.
4. If still failing, pause for user direction or enter degraded mode only with explicit user approval.

### Gemini Timeout or Empty Response

1. Retry once with:
- Smaller payload.
- `-t` increase (up to script max).
2. If retry fails, split payload into smaller review batches.
3. If all batches fail, stop dual-review loop and request user decision (wait/retry/degraded mode).

### Gemini Usage/Rate Limit (Quota Exhausted)

Symptoms: limit/rate messages, repeated failures without verdict.

Actions:
1. Save handoff artifact immediately.
2. Back off and retry later with smaller payload.
3. Prefer lower reasoning effort/context where acceptable.
4. Resume from plan status and handoff once quota resets.

### Codex Context Saturation ("Out of Memory")

Symptoms: forgotten constraints, repeated re-reading, drift from approved plan.

Actions:
1. Stop edits after current atomic change.
2. Update `.gemini-manager/session-handoff.md` with current status.
3. Run:

```bash
./skills/gemini-manager/scripts/plan-utils.sh status <plan_file.md>
git diff --name-only
```

4. Restart with handoff + plan status as source of truth.
5. Re-run Gemini review if strategy changed.

### Interrupted Session / Crash / Usage Cutoff

Recovery sequence:
1. `git status`
2. `./skills/gemini-manager/scripts/plan-utils.sh status <plan_file.md>`
3. Read `.gemini-manager/session-handoff.md`
4. Resume at the recorded "Next command."

## Role Separation

| Codex (Planner + Implementer) | Gemini (Reviewer) |
|-------------------------------|------------------|
| Analyzes codebase             | Reviews and challenges plans |
| Creates implementation plans  | Identifies gaps and risks |
| Asks user for clarification   | Raises user-facing questions |
| Writes all code               | Does not modify repository files |
| Follows approved plan         | Approves or rejects changes |
| Fixes review findings         | Verifies fixes in re-review |

## Handling Disagreements

If Codex and Gemini disagree on approach:
1. Codex re-submits with explicit reasoning and constraints.
2. Gemini re-evaluates.
3. If unresolved after two rounds, present both approaches to the user and ask for direction.

## Degraded Mode (Explicit User Approval Required)

Use only when Gemini review is temporarily impossible (outage/usage cap/consistent timeouts).

1. Explain why dual approval cannot be completed now.
2. Provide risk summary and unreviewed areas.
3. Ask user for explicit approval to proceed in degraded mode.
4. If approved, Codex can continue with:
- strict scope freeze
- full local verification
- mandatory deferred Gemini review when available
5. Record degraded-mode decision in `.gemini-manager/session-handoff.md`.

## Infinite Review Loop

If Gemini rejects three rounds in a row:
1. Show user repeated findings.
2. Show what has already been tried.
3. Offer 2-3 concrete options with tradeoffs.
4. Wait for user direction before continuing.

## Best Practices

<planning_tips>
- Include concrete file paths, function names, and risks in plan reviews.
- Include diff snippets plus test output in change reviews.
- Keep review payloads focused on relevant context.
- Prefer incremental changes over large monolithic diffs.
</planning_tips>

<review_tips>
- Address valid findings immediately.
- When disagreeing with a finding, explain constraints and rationale explicitly in the next review submission.
- Separate must-fix blockers from optional refinements to avoid loop churn.
</review_tips>

<capacity_tips>
- Keep a running context summary; do not rely on long chat history alone.
- Batch large reviews and merge conclusions in a final reconciliation pass.
- Favor deterministic templates and checklists over free-form prompts.
</capacity_tips>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marroth93) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
