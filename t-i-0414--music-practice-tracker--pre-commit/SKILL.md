---
name: pre-commit
description: Quick quality gate before committing that checks code quality and error handling. Use before git commit, when asked to "check before commit", "pre-commit check", or "quality gate" on staged/unstaged changes. Use when this capability is needed.
metadata:
  author: t-i-0414
---

# Pre-Commit Quality Check

Run a quick quality gate on unstaged/staged changes before committing.

## Workflow

1. **Identify Changes**
   - Run `git diff --name-only` for unstaged changes
   - Run `git diff --cached --name-only` for staged changes
   - Combine both lists as the review scope

2. **Launch Sub-Agents in Parallel**

   Run these two sub-agents simultaneously on the changed files:

   **code-reviewer**:
   - Check project guideline compliance
   - Detect bugs and logic errors
   - Verify coding standards

   **silent-failure-hunter**:
   - Find silent failures in error handling
   - Check catch blocks and fallback logic
   - Verify error logging

3. **Aggregate Results**

   Combine findings from both sub-agents:
   - **Blockers** (must fix before commit): Critical bugs, silent failures, guideline violations
   - **Warnings** (should fix): Important issues, missing error context
   - If no issues found, confirm code is ready to commit

4. **Summary**

   ```
   ## Pre-Commit Check

   Checked X files.

   ### Blockers (X)
   - [issue] [file:line]

   ### Warnings (X)
   - [issue] [file:line]

   ### Verdict
   Ready to commit / Fix blockers before committing
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t-i-0414) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
