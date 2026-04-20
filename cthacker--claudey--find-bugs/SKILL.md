---
name: find-bugs
description: Find bugs, security vulnerabilities, and code quality issues in local branch changes. Use when asked to review changes, find bugs, security review, or audit code on the current branch. Use when this capability is needed.
metadata:
  author: cthacker
---

# Find Bugs

Review changes on the current branch for bugs, security vulnerabilities, and code quality issues.

**Important:** Gather any relevant context (project conventions, related code, what the changes are meant to do), then delegate the full review below to a subagent. Pass the subagent the diff, the list of changed files, and any context that would help it distinguish intentional behavior from bugs.

## Phase 1: Complete Input Gathering

1. Determine the base branch:
   - Try `gh repo view --json defaultBranchRef --jq '.defaultBranchRef.name'`
   - If that fails, fall back to `main` or `master` (whichever exists)
2. Gather all changes to review:
   - Committed branch diff: `git diff <base>...HEAD`
   - Staged local diff: `git diff --cached`
   - Unstaged local diff: `git diff`
   - Untracked files: `git ls-files --others --exclude-standard`
3. If any output is truncated, read each changed file individually until you have seen every changed line.
4. Build one unified file list from committed, staged, unstaged, and untracked changes before proceeding.

## Phase 2: Attack Surface Mapping

For each changed file, identify and list:

- All user inputs (request params, headers, body, URL components)
- All database queries
- All authentication/authorization checks
- All session/state operations
- All external calls
- All cryptographic operations

## Phase 3: Data Flow Analysis

Trace untrusted data across function and file boundaries:

- For each user input identified in Phase 2, follow it through the code until it is consumed (rendered, queried, stored, returned, logged).
- Flag any path where the data is used without validation or sanitization.
- Note cases where data crosses a trust boundary (e.g., user input passed to a different module that assumes it's safe).

## Phase 4: Security Checklist (check EVERY item for EVERY file)

- [ ] **Injection**: SQL, command, template, header injection
- [ ] **XSS**: All outputs in templates properly escaped?
- [ ] **Authentication**: Auth checks on all protected operations?
- [ ] **Authorization/IDOR**: Access control verified, not just auth?
- [ ] **CSRF**: State-changing operations protected?
- [ ] **Race conditions**: TOCTOU in any read-then-write patterns?
- [ ] **Session**: Fixation, expiration, secure flags?
- [ ] **Cryptography**: Secure random, proper algorithms, no secrets in logs?
- [ ] **Information disclosure**: Error messages, logs, timing attacks?
- [ ] **DoS**: Unbounded operations, missing rate limits, resource exhaustion?
- [ ] **Business logic**: Edge cases, state machine violations, numeric overflow?

## Phase 5: Verification

For each potential issue:

- Check if it's already handled elsewhere in the changed code
- Search for existing tests covering the scenario
- Read surrounding context to verify the issue is real

## Phase 6: Pre-Conclusion Audit

Before finalizing:

1. List every file you reviewed and confirm you read it completely
2. List every checklist item and note whether you found issues or confirmed it's clean
3. List any areas you could NOT fully verify and why
4. Only then provide your final findings

## Output Format

**Prioritize**: security vulnerabilities > bugs > code quality

**Skip**: stylistic/formatting issues

For each issue:

- **File:Line** — Brief description
- **Severity**: Critical / High / Medium / Low
- **Problem**: What's wrong
- **Evidence**: Why this is real (not already fixed, no existing test, etc.)
- **Fix**: Concrete suggestion
- **References**: OWASP, RFCs, or other standards if applicable

If you find nothing significant, say so — don't invent issues.

Do not make changes — just report findings.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cthacker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
