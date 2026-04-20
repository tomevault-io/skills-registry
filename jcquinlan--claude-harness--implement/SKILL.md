---
name: implement
description: Execute one cycle of the PRD-driven development loop. Picks the next incomplete feature, implements it, tests it, and commits. Use when this capability is needed.
metadata:
  author: jcquinlan
---

# Implement Next Feature

Execute one iteration of the development loop. If a feature ID is provided, work on that feature. Otherwise, pick the highest-priority incomplete feature from `prd.json`.

Target feature: $ARGUMENTS

## Workflow

Follow these steps **in order**. Do not skip steps.

### 1. Orient

- Read `project.json` to understand the tech stack, test command, and project structure
- Read `progress.md` to understand what happened in previous sessions
- Read `prd.json` to find the target feature (either the one specified in $ARGUMENTS or the highest-priority feature where `passes` is `false`)
- If all features pass, report that and stop

### 2. Validate Environment

- Run `init.sh` if it exists
- If it reports failures, fix them before starting new work
- Run the test command from `project.json` to confirm the baseline is green

### 3. Plan the Implementation

Before writing any code:
- Read the feature's `steps` array carefully
- Identify which files need to be created or modified
- Check the test files to see what assertions exist for this feature
- If the feature depends on other features, verify those are already passing

### 4. Implement

- Write the minimum code needed to make the feature's tests pass
- Do NOT implement other features while working on this one
- Do NOT refactor unrelated code
- If you discover a bug in an existing feature, note it in progress.md but stay focused on the current feature

### 5. Verify

- Run the full test suite using the `test_command` from `project.json` (not just the new feature's tests)
- All tests must pass, including previously passing features
- If tests fail, fix the issue and re-run until green

### 6. Commit

- Stage only the files related to this feature
- Write a commit message that references the feature ID:
  ```
  Implement F00X: <feature description>
  ```

### 7. Update PRD

- Set the feature's `passes` field to `true` in `prd.json`
- Do NOT modify any other fields in the PRD

### 8. Log Progress

Append a new entry to `progress.md` with this structure:

```markdown
## Session N - YYYY-MM-DD

**Worked on**: FXXX - <feature description>

**Completed**:
- <what was built>

**Test results**: X/Y passing

**Discovered**:
- <anything unexpected, blockers, patterns found>

**Next session should**: <what to work on next>
```

### 9. Report

Summarize what was done: which feature, what was built, test results, what's next.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcquinlan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
