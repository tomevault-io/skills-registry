---
name: code-review
description: Review staged git changes with hands-on verification. Use when the user asks to review code, review changes, do a code review, or invokes /code-review. Checks out staged changes in an isolated git worktree, runs tests/linters to verify concerns rather than guessing, then cleans up. Grades findings using an Eisenhower matrix (urgent/important). Use when this capability is needed.
metadata:
  author: mpawlak2
---

# Code Review

Review staged changes with hands-on verification. Do not guess — confirm every concern by reading surrounding code, running tests, or checking behavior.

## Phase 1 — Set up isolated worktree

Run `scripts/worktree-setup.sh` from the skill directory. It creates a detached worktree at HEAD with the staged patch applied and prints `CR_PATCH=...` and `CR_WORKTREE=...`. Capture both values — you need them for Phase 3. The script exits non-zero if there are no staged changes; inform the user and stop.

The original working directory (staged, unstaged, untracked files) remains untouched throughout the entire review. All work happens inside `$CR_WORKTREE`.

## Phase 2 — Review with verification

1. Read the patch file (`$CR_PATCH`) to identify all changed files and hunks.
2. Read the **full files** that were changed — not just the diff — to understand surrounding context.
3. For each concern you identify, **verify it** before reporting:
   - Logic bug? Trace the code path. Read callers and callees. Run the relevant test suite.
   - Performance issue? Check actual data structures and sizes. Look for benchmarks.
   - Security concern? Find where inputs originate. Check sanitization layers.
   - Style violation? Check the project's linter config and existing conventions.
   - Missing error handling? Read the API/library docs or type definitions to confirm failure modes.
4. You may freely edit files, run scripts, and execute tests inside the worktree — it will be deleted in Phase 3.
5. Only report findings you have **confirmed**. If you cannot verify a concern, state what you checked and that it remains unconfirmed.

### Review Checklist

Evaluate against these areas. Only comment where there is something actionable.

- **Correctness** — Logic errors, off-by-ones, null/undefined access, unhandled edge cases, race conditions
- **Security** — Injection, hardcoded secrets, missing input validation at boundaries, unsafe deserialization
- **Performance** — Unnecessary allocations in hot paths, O(n^2) where O(n) suffices, missing indexes, redundant work
- **Naming & clarity** — Misleading names, overly generic names, unclear abbreviations
- **Style & consistency** — Deviations from the project's established conventions
- **Best practices** — Missing error handling for external calls, resource leaks, deprecated API usage

## Phase 3 — Clean up

Run `scripts/worktree-teardown.sh "$CR_WORKTREE" "$CR_PATCH"` from the original repo directory. It removes the worktree and temp files, with fallback cleanup if `git worktree remove` fails.

**You MUST complete this phase even if the review is interrupted or encounters errors.**

## Output Format

### Eisenhower Matrix Grading

Classify each finding into one of four quadrants:

| | **Urgent** | **Not Urgent** |
|---|---|---|
| **Important** | **Q1 — Fix before merge** | **Q2 — Plan to fix** |
| **Not Important** | **Q3 — Quick win** | **Q4 — Consider** |

- **Q1 (Urgent + Important)** — Blocks merge. Confirmed bugs, security vulnerabilities, data loss risks, broken builds.
- **Q2 (Important + Not Urgent)** — Should be addressed but can be a follow-up. Design issues, missing test coverage for critical paths, tech debt that will compound.
- **Q3 (Urgent + Not Important)** — Cheap to fix right now. Typos, minor style inconsistencies, easy naming improvements.
- **Q4 (Not Urgent + Not Important)** — Optional. Subjective preferences, bikeshed items, nice-to-haves.

### Comment Format

```
**file_path:line_number** — [Q1|Q2|Q3|Q4] summary

> quoted code line(s)

What was verified (command run, file checked, test result) and what was found. Suggested fix if applicable.
```

Group comments by file. Use line numbers from the worktree files (which reflect the post-patch state).

### Summary

End with a matrix summary and decision:

```
| | Urgent | Not Urgent |
|---|---|---|
| Important | N findings | N findings |
| Not Important | N findings | N findings |

Decision: approve / request changes
```

Request changes if there are any Q1 findings. Otherwise approve (Q2-Q4 items are non-blocking).

## Example

**src/auth.ts:42** — [Q1] `getSession()` returns null, causing runtime TypeError

> `const token = getSession().token;`

Verified: checked `src/session.ts:18` — return type is `Session | null`. Ran `npm test -- --grep auth` in worktree and confirmed failure: `TypeError: Cannot read properties of null (reading 'token')`.

```ts
const token = getSession()?.token;
```

---

| | Urgent | Not Urgent |
|---|---|---|
| Important | 1 | 0 |
| Not Important | 0 | 0 |

Decision: request changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mpawlak2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
