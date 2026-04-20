---
name: validate
description: >- Use when this capability is needed.
metadata:
  author: astrosteveo
---

# Validate: Completion Gate

Before any work can be declared complete, it must pass through this validation gate.
Code that compiles is not code that works. Code that works is not code that is verified.
When communicating results or asking the user questions, explain clearly and provide
context — assume they may be learning these practices.

## Automated Validation

1. **Run the full test suite.** Not just the tests you wrote — the entire suite.
   Report the results: total tests, passed, failed, skipped.

2. **If any tests fail**, fix the failures and re-run. Repeat this cycle until all tests
   pass. Do not remove or skip failing tests. Do not declare done with failing tests.
   If you cannot fix a failure after reasonable effort, explain the issue to the user
   and ask for guidance — but do not silently move on.

3. **Check for regressions.** If tests that were passing before your changes are now
   failing, this is a regression. Investigate the root cause and fix it.

## Plan Validation

If a plan document exists (check `docs/plans/*/PLAN.md`):

1. Review every task in the plan. Is each one marked complete?
2. Review the test strategy. Was it followed?
3. Review the verification checklist. Are there manual steps?
4. Update the plan's status section to reflect the current state.

## Manual Verification

Present the user with specific, actionable verification steps for anything that cannot
be covered by automated tests. Be concrete:

<example title="Good: specific, actionable, tells the user exactly what to look for">
"Open the app at localhost:3000/login, enter invalid credentials, and verify
you see the error message 'Invalid email or password' — not a generic 500 error."
</example>

<example title="Bad: vague, the user doesn't know what to actually check">
"Test the login flow manually."
</example>

After presenting the manual verification steps, **wait for the user to report back.**
Do not check items off yourself. The user will tell you what passed, what failed, and
what they observed. Based on their feedback:
- Check off items they confirm as working in PLAN.md's Verification Checklist.
- If something failed, investigate and fix it, then ask the user to re-verify that item.
- Continue this loop until all checklist items are confirmed by the user.

## Report

Present a clear summary:

```
## Validation Results

### Tests
- Total: X | Passed: X | Failed: X | Skipped: X
- Command: `{test command used}`

### Plan Status
- Tasks completed: X/Y
- Outstanding items: [list if any]

### Manual Verification Needed
1. [Specific step with expected outcome]
2. [Specific step with expected outcome]
```

Do not say "done" or "complete" until:

- All automated tests pass.
- The user has confirmed each manual verification step.
- The plan document (if it exists) is updated to reflect reality.
- The session wrap-up (below) has been completed.

## Session Wrap-Up

Once the user has confirmed all manual verification steps and the last task is marked
complete, run the session wrap-up before declaring the work done.

1. **Verify worktree status.** Run `git status` and report one of:
   - **Clean** — working tree is clean, nothing to commit. Proceed to step 2.
   - **Dirty** — there are uncommitted changes, untracked files, or staged changes.
     Present what you find to the user and ask how they want to handle it. If the
     commit-per-task workflow was followed correctly, there should be nothing left
     uncommitted unless a previous session went sideways or non-code files were
     generated. Common options:
     - Stage and commit the remaining changes
     - Add files to `.gitignore`
     - Leave as-is for the user to handle later

2. **Update project memory.** Review what was learned during this session and update
   MEMORY.md with anything future sessions should know.

3. **Prompt for CLAUDE.md refresh.** Tell the user:
   "The project structure or conventions may have changed during this session.
   Run `/init` to refresh CLAUDE.md so future sessions pick up the new context."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/astrosteveo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
