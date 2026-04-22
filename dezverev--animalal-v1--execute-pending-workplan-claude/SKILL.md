---
name: execute-pending-workplan-claude
description: Picks one workplan from .claude/workplans/Pending/ (skipping plans for issues tagged AIIGNORE or FUTUREWORK), moves it to .claude/workplans/Inprogress/, executes each step via the referenced skills, runs tests and logical-response check. On full success moves to .claude/Done/ and sets the GitHub issue as complete. On partial success (significant progress made, tests pass, but work remains), creates a continuation GitHub issue and progress report. Use when the user says "execute pending workplan", "work from pending", "pick and do a workplan", or "run the next pending plan". Use when this capability is needed.
metadata:
  author: dezverev
---

You are an execute-pending-workplan specialist. When invoked, you pick one workplan from Pending, move it to Inprogress, run implementation steps using the skills referenced in the plan, and verify everything still works (tests + logical-response check). Depending on outcome: (1) Full success → move to Done and close issue as completed, (2) Partial success → create continuation issue and progress report, leave in Inprogress, (3) Failure → leave in Inprogress and report errors.

## When to Run This Workflow

- User says: "execute pending workplan", "work from pending", "pick and do a workplan", "run the next pending plan", or "do a plan from Pending"
- User wants one pending issue/plan executed end-to-end using skills and verified with tests

## Workflow (6 Steps)

Execute these in order. Workplans are markdown files from the **pick-issue-and-plan** skill: `.claude/workplans/Pending/<slug>.md` with sections **Issue**, **Scope**, **Implementation steps**, and **Skills to use (in order)**.

### Step 1: List Pending and pick one

1. List `.claude/workplans/Pending/*.md` (create `.claude/workplans/Pending/` if missing). If the list is empty, report "No workplans in Pending" and stop.
2. **Ignore rule**: Exclude from consideration any workplan whose content indicates the source issue had labels or tags **AIIGNORE** or **FUTUREWORK** (e.g. in the Issue section, labels, References, or "Why this issue"). Skip such files and do not pick them.
3. **Choice rule**: Among remaining plans, if the plan body contains "**Priority**: maint" vs "**Priority**: feat", prioritize maint over feat; then break ties by filename (e.g. alphabetical). Take the first in that order. If none have that line, take the first file by name.
4. **User override**: If the user named a file or issue (e.g. "do issue-42-fix-disposal…"), pick that file in Pending if it exists (and it is not excluded by the ignore rule).

### Step 2: Move to Inprogress

1. Ensure `.claude/workplans/Inprogress/` exists.
2. Move the chosen file from `.claude/workplans/Pending/<slug>.md` to `.claude/workplans/Inprogress/<slug>.md` (no content change).
3. State which workplan is now in progress (issue title/slug from the plan).

### Step 3: Execute each implementation step

1. Parse the plan's **Implementation steps** and **Skills to use (in order)**.
2. For each numbered step:
   - Resolve the skill from "Use skill: **X**" or the Skills table.
   - Read `.claude/skills/<skill-name>/SKILL.md` and follow that skill's workflow (discovery, edits, commands, etc.).
   - Perform the step's edits and commands; do not skip or relax the skill's checklist.
   - Use context7 to fetch relevant docs for the edit.
3. If a step has no skill, treat it as a manual instruction and perform it (e.g. "Run tests" → use smart-test-runner when that's the stated skill for that step).
4. If a step fails (e.g. test run fails, or skill workflow cannot be completed), record the failure, leave the plan in Inprogress, and report; do not move to Done.

### Step 4: Ensure everything still works

After all implementation steps (or as specified in the plan):

1. **Run smart-test-runner**: Scope from the plan's "Scope / Affected areas" or "Key files" → map to tags (or "run all" if kernel/wide). Read `.claude/skills/smart-test-runner/SKILL.md` and follow its workflow. Execute from repo root: `.\run-tests.ps1` with `-t <tags>` when filtered, or no `-t` for full suite.
2. **Run check-test-logical-resp** on the latest test-results JSON in `src/IntegrationTesterApp/test-results/`. Read `.claude/skills/check-test-logical-resp/SKILL.md` and follow its workflow.
3. If tests fail or logical check flags issues: treat as "everything does not work"; do not move to Done; report failures and suggested fixes (from analyze-test-json / check-test-logical-resp if useful).

### Step 5: Finish, leave in progress, or create continuation

Determine outcome based on work completed:

#### **Full Success** (All steps completed, tests pass)
1. Ensure `.claude/workplans/Done/` exists. Move `.claude/workplans/Inprogress/<slug>.md` → `.claude/workplans/Done/<slug>.md`.
2. **Set the GitHub issue as complete**: If the plan has an **Issue** section with **Number** (e.g. `#42`), close the issue as completed so the issue reflects that the work was done.
   - Parse **Number** from the plan (integer after `#`). Resolve **owner** and **repo** from the plan's **Link** (e.g. `https://github.com/owner/repo/issues/42` → owner, repo) or from the current repo (e.g. `git remote get-url origin`).
   - **Prioritize GitHub MCP**: Always use **issue_write** on server **user-github** first with: **method** `"update"`, **owner**, **repo**, **issue_number** (the parsed number), **state** `"closed"`, **state_reason** `"completed"`. Tool schema: `mcps/user-github/tools/issue_write.json` (read if needed).
   - **Fallback**: Only if the GitHub MCP server is unavailable or the call fails, run from repo root: `gh issue close <number> --reason completed`. If the plan has no Issue section or no number, skip this step and report that the issue was not updated.
3. Summarize what was done, that the workplan is done, and that the issue was set complete (or that it was skipped if no issue number was in the plan).

#### **Partial Success** (Significant progress made, tests pass, but more work remains)
Use this outcome when:
- Multiple phases/steps were completed successfully (e.g. completed Phases 1-2 of a 7-phase plan)
- All tests pass with no regressions
- Work stopped due to complexity, time, or natural breakpoint (not due to errors)
- Remaining work is clearly defined and can be continued later

**Actions**:
1. **Leave plan in Inprogress**: Do NOT move to Done.
2. **Create continuation GitHub issue**:
   - Parse original issue number, title, owner, repo from the plan's **Issue** section.
   - Create a new GitHub issue with:
     - **Title**: `feat: Complete [original title] (Phases X-Y)` or similar continuation title
     - **Body**: Markdown that includes:
       - Link to original issue
       - Summary of what was completed (phases, steps, with ✅ checkmarks)
       - Summary of what remains (phases, steps, with ❌ or 🚧)
       - Link to progress report (`.claude/workplans/DoneReport/<slug>_PROGRESS.md`)
       - Link to in-progress workplan (`.claude/workplans/Inprogress/<slug>.md`)
       - References to key files created/modified
       - Success criteria checklist for remaining work
     - **Label**: `enhancement` (or same labels as original)
   - Use `gh issue create --title "..." --body "$(cat <<'EOF' ... EOF)" --label "enhancement"` from repo root.
   - Capture new issue number and URL from output.
   - **Continuation issue body template**:
     ```markdown
     # Complete [Feature Name] ([Remaining Phases])

     ## Context
     This issue continues work from #[original-issue-number] ([original-title]).

     **Original workplan**: `.claude/workplans/Inprogress/[slug].md`
     **Progress report**: `.claude/workplans/DoneReport/[slug]_PROGRESS.md`

     ## What's Already Done ✅
     [List completed phases/steps with checkmarks]

     ## What Remains 🚧
     [List remaining phases/steps with details]

     ## Technical Foundation Already In Place
     [Describe what's ready to build upon]

     ## Estimated Effort
     **Remaining**: [X] steps across [Y] phases
     **Duration**: [estimate if known]

     ## Success Criteria
     [Checklist of goals for remaining work]

     ## References
     - Original issue: #[original-number]
     - In-progress workplan: `.claude/workplans/Inprogress/[slug].md`
     - Progress report: `.claude/workplans/DoneReport/[slug]_PROGRESS.md`
     - [Key files/directories created]
     ```
3. **Close original issue as completed** (if significant progress warrants):
   - If >50% of work is done and foundation is solid, close original issue with reason `completed`.
   - Add comment: "Partially completed. Continuation tracked in issue #[new-issue-number]."
   - If <50% done, consider leaving original open and linking continuation issue in a comment instead.
4. Report: What was completed, what remains, new issue number and URL, progress report location.

#### **Failure** (Step failed, tests failed, or error prevented progress)
1. **Leave plan in Inprogress**: Do NOT move or close original issue.
2. Report what failed, which step, and next actions (e.g. fix code and re-run tests, then "execute pending workplan" again to continue or re-verify).
3. Do NOT create a continuation issue for failures - the original issue remains the tracking mechanism.

### Step 6: Create completion or progress report

1. Ensure `.claude/workplans/DoneReport/` exists. Create it if it does not.
2. Create report based on outcome:
   - **Full Success**: Create `.claude/workplans/DoneReport/<slug>_Report.md` - Summary of what was done, references used, decisions made.
   - **Partial Success**: Create `.claude/workplans/DoneReport/<slug>_PROGRESS.md` - Detailed progress report including:
     - What was completed (with ✅)
     - What remains (with ❌)
     - Test results (pass/fail)
     - Technical decisions made
     - Files created/modified
     - Next steps for continuation
     - References to continuation issue created
   - **Failure**: Create `.claude/workplans/DoneReport/<slug>_FAILURE.md` - Summary of what was attempted, what failed, error details, recommended fixes.

## Paths

| Purpose        | Path |
|----------------|------|
| Pending        | `.claude/workplans/Pending/` |
| Inprogress     | `.claude/workplans/Inprogress/` |
| Done           | `.claude/workplans/Done/` |
| Skills         | `.claude/skills/` |
| Test results   | `src/IntegrationTesterApp/test-results/` |
| Repo root      | Project root for `.\run-tests.ps1` and git |
| GitHub MCP     | `mcps/user-github/tools/issue_write.json` (server **user-github**) for closing issue as completed |

## Skill references

The agent executes these by reading their SKILL.md and following their workflow; it does not invoke them as separate processes.

| Step / need                    | Skill / tool | Path / Command |
|--------------------------------|--------|----------------|
| Run tests                      | smart-test-runner | `.claude/skills/smart-test-runner/SKILL.md` |
| Audit passed responses         | check-test-logical-resp | `.claude/skills/check-test-logical-resp/SKILL.md` |
| Set issue complete (full success) | **issue_write** (user-github) — prioritize GitHub MCP; use gh only if MCP unavailable | `mcps/user-github/tools/issue_write.json` — on full success, after moving plan to Done |
| Create continuation issue (partial success) | `gh issue create` from repo root | Create new issue with continuation details, references to progress report |
| Close original issue (partial)  | **issue_write** or `gh issue close` | Close with `completed` reason if >50% done, add comment linking to continuation |

Implementation steps in the plan may reference any project skill (e.g. review-plugin, add-plugin-test, add-plugin, sync-providers). Resolve each step's "Use skill: **X**" to `.claude/skills/<X>/SKILL.md` and run that skill's workflow.

## Outcome Decision Criteria

When deciding between Full Success, Partial Success, and Failure:

### Full Success
- ✅ All implementation steps completed
- ✅ All tests pass
- ✅ Logical response check passes
- ✅ No remaining work in the plan
- **Action**: Move to Done, close original issue as completed

### Partial Success
- ✅ Multiple phases/steps completed (e.g. 2+ of 7 phases)
- ✅ All tests pass with no regressions
- ✅ Foundation is solid and ready for next phase
- ❌ Significant work remains (but clearly defined)
- **Reason for stopping**: Complexity, time constraints, natural breakpoint (NOT errors)
- **Action**: Create continuation issue, leave in Inprogress, optionally close original

### Failure
- ❌ Step failed due to error
- ❌ Tests failed
- ❌ Could not complete current step
- **Action**: Leave in Inprogress, report error, do NOT create continuation issue

### Examples
- **Full Success**: "Add plugin X" - plugin created, tested, documented, all steps done.
- **Partial Success**: "Create React app" - backend complete (5 steps), React scaffolded (3 steps), but 15+ frontend steps remain. Tests pass. Natural breakpoint between backend and frontend work.
- **Failure**: "Add plugin X" - plugin created but tests fail with compilation errors.

## Constraints

- Only one workplan is moved into Inprogress per run (the one picked in Step 1). Do not pull a second from Pending until the current one is moved to Done or explicitly abandoned.
- When picking from Pending, skip workplans whose content indicates the source issue had labels **AIIGNORE** or **FUTUREWORK** (see Step 1 ignore rule).
- Always run from repo root for tests and git.
- Do not relax tests or change expectations to force success; follow project rules (fix code first, then re-run).
- If Pending is empty, exit cleanly with a clear message; do not create or assume a plan.
- **Partial Success is valid**: For large, multi-phase plans, stopping at a natural breakpoint with tests passing is acceptable. Create a continuation issue to track remaining work.

## Optional: resume and abandon

- **Resume**: If the user says "continue the workplan" and there is exactly one file in `.claude/workplans/Inprogress/`, skip Step 1–2 and proceed from Step 3 using that plan.
- **Abandon**: If the user says "abandon this workplan" or "put it back to Pending", move the file from Inprogress back to Pending and report.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
