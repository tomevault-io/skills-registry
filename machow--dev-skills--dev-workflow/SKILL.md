---
name: dev-workflow
description: Full development workflow with plan refinement and code review. Creates a plan, refines it twice with Codex, implements, then reviews twice with roborev. Use when this capability is needed.
metadata:
  author: machow
---

# Development Workflow

Execute a structured development workflow: plan, refine, implement, review.

The task is: $ARGUMENTS

**IMPORTANT: Do NOT use EnterPlanMode or ExitPlanMode.** Write the plan directly to a file
so that refinement can proceed without blocking on approval.

## Step 0: Create Task List

Use TaskCreate to create these tasks **exactly as written** (use the subject verbatim):

| # | Subject | Description |
|---|---------|-------------|
| 1 | Write plan | Research codebase, write plan to file |
| 2 | Plan refinement 1: /plan-refine-codex | Run /plan-refine-codex, incorporate feedback |
| 3 | Plan refinement 2: /plan-refine-codex | Run /plan-refine-codex again, incorporate feedback |
| 4 | Approve plan | Present refined plan, wait for user sign-off |
| 5 | Implement (placeholder) | Will be replaced with concrete tasks after plan approval |
| 6 | Code review 1: /roborev-review | Run /roborev-review, address findings |
| 7 | Code review 2: /roborev-review | Run /roborev-review, address remaining findings |
| 8 | Summary | Fill in report-template.md, write to .claude/plans/ |

## Step 1: Plan

- Mark task 1 as in_progress
- Use the Task tool to spawn Explore subagents (subagent_type=Explore) to research the codebase in parallel. For example, launch one agent to find relevant files and another to understand existing patterns. Use multiple subagents when the research has independent parts.
- Synthesize the research into a thorough implementation plan and write it to `.claude/plans/${CLAUDE_SESSION_ID}.md` (create the directory if needed)
- The plan should include: goal, files to modify/create, step-by-step approach, edge cases, testing strategy
- Mark task 1 as completed
- **Proceed immediately to Step 2** — do NOT stop or ask for approval here

## Step 2: Refine Plan (2 rounds)

For each refinement round:

- Mark the refinement task as in_progress
- Run `/plan-refine-codex .claude/plans/${CLAUDE_SESSION_ID}.md`
- Read the Codex output and incorporate improvements back into `.claude/plans/${CLAUDE_SESSION_ID}.md`
- Mark the refinement task as completed

After both rounds are complete, move to the approval gate.

## Step 3: Approval Gate

- Mark task 4 as in_progress
- Present a summary of the final refined plan to the user
- Use AskUserQuestion with these options:
  - **Approve (headless)** — Launch implementation in a fresh headless session (recommended for clean context)
  - **Approve (continue)** — Continue implementation in this session
  - **Request changes** — Go back and revise the plan
- Do NOT proceed to implementation without explicit user approval
- Mark task 4 as completed

## Step 4: Launch Implementation

### If user chose "Approve (headless)":

Generate a UUID for the headless session, then launch it with a fresh context via Bash:

```bash
HEADLESS_ID=$(uuidgen | tr '[:upper:]' '[:lower:]')
claude -p "You are executing the /dev-workflow skill starting from Step 5 (Implement). \
Read the skill definition at ~/.claude/skills/dev-workflow/SKILL.md for full instructions. \
Read the approved plan at .claude/plans/PLAN_SESSION_ID.md. \
Your session ID is $HEADLESS_ID. \
Execute Steps 5, 6, and 7. \
For Step 7, use the report template at ~/.claude/skills/dev-workflow/report-template.md \
and write the filled-in report to .claude/plans/PLAN_SESSION_ID-report.md." \
  --dangerously-skip-permissions \
  --session-id "$HEADLESS_ID"
```

Replace `PLAN_SESSION_ID` with the actual session ID used for the plan file.

Run this command with `run_in_background: true` since implementation may take a while.
Tell the user the headless session ID so they can resume it if needed (`claude -r <id>`).
Periodically check on progress using the TaskOutput tool.

After the headless session finishes, **verify the results** before reporting (see Step 8).

### If user chose "Approve (continue)":

Continue directly to Step 5 in this session. Delete the placeholder task and proceed
with implementation as described below.

---

## Step 5: Implement (headless session)

Create a task list for the remaining work:

1. Read the plan file and create one task per implementation step from the plan
2. Create tasks for: **Code review 1: /roborev-review** and **Code review 2: /roborev-review**
3. Create a **Summary: write report** task
4. Use `addBlockedBy` so code review tasks are blocked by all implementation tasks

Then work through the implementation tasks in order:

- Mark each in_progress → completed
- Commit changes with descriptive messages as you go

## Step 6: Code Review (headless session)

For each review round:

- Mark the review task as in_progress
- Run `/roborev-review` on the committed changes
- If the review finds issues, address them and commit fixes
- Mark the review task as completed

## Step 7: Summary (headless session)

- Mark the summary task as in_progress
- Read the report template at `~/.claude/skills/dev-workflow/report-template.md`
- Fill in every field in the template with actual data from this session
- Write the completed report to `.claude/plans/PLAN_SESSION_ID-report.md` (use the same session ID from the plan file path)
- Also print the completed report to stdout
- Mark the summary task as completed

## Step 8: Verify and Report (interactive session)

After the headless session finishes:

1. Read the report file at `.claude/plans/PLAN_SESSION_ID-report.md`
2. **Verify reviews independently**: run `/roborev-show` or check `git log` to confirm reviews actually ran and the verdicts in the report are accurate
3. Present the report to the user including:
   - Headless session ID (so they can `claude -r <id>` to inspect)
   - Implementation commits
   - Review 1 verdict + findings
   - Review 2 verdict + findings
   - Overall status
4. If reviews did NOT pass, tell the user and suggest next steps (e.g., `/roborev-refine` or manual fixes)

## Rules

- **Never skip steps.** Execute every task in order.
- **Never use plan mode.** Write plans to `.claude/plans/${CLAUDE_SESSION_ID}.md` directly.
- **Update the task list** before and after each step (in_progress → completed).
- **Do not ask** "should I continue?" between steps — just proceed, except at the explicit approval gate (Step 3).
- If a review fails and you fix issues, the fix counts as part of that review round — do not add extra rounds.
- The interactive session handles Steps 0-4 and Step 8. The headless session handles Steps 5-7.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/machow) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
