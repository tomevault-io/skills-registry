---
name: respond-to-review
description: > Use when this capability is needed.
metadata:
  author: waieez
---

# Respond to Review

Turn review feedback into resolved changes.

## Procedure

1. **Read all feedback.** Read every review comment before acting. Understand the full picture — comments may interact or conflict.

2. **Fix what's clearly wrong.** When a comment identifies a clear problem — a bug, a missing test, a standards violation — fix it. The commit is the response. Don't reply to explain what you fixed unless there's something non-obvious about the approach.

3. **Respond when the path isn't obvious.** Some feedback requires discussion before action:
   - **Clarifications** — the reviewer asks *why*, not *what*. Explain your reasoning with evidence (code, tests, docs, data), not assertions.
   - **Disagreements** — you believe the current approach is correct. State your reasoning clearly and provide evidence. If you can't reach agreement after one exchange, escalate:
     - **Within a team:** Tag the tech lead or a domain expert.
     - **Open source:** Tag a maintainer or reference the project's decision-making process.
     - **No clear escalation path:** State your position, note the disagreement, and defer to the reviewer's judgment. Do not stall the PR.
   - **Conflicting comments** — two comments ask for contradictory changes. Reply to both noting the conflict and ask the reviewer to clarify.

4. **Run verification.** After all changes, re-run the project's test suite, linter, and build. All must pass.

5. **Re-request review.** Signal that the PR is ready for another look. If the reviewer doesn't respond within the project's expected turnaround time, follow up once. If still unresponsive, escalate to a second reviewer or maintainer.

## When Steps Fail

- **Requested change breaks something else.** Implement the change, then show the breakage (failing test, regression) in your reply. Propose an alternative that satisfies the intent without the side effect.
- **Checks fail after addressing feedback.** Bisect the new commits to find which change introduced the failure. Fix it before re-requesting review.
- **Reviewer is unresponsive after re-request.** Wait the project's expected turnaround time (default: 2 business days if not specified). Follow up once with a polite ping. If still no response, request review from a second reviewer.

## Examples

### Example 1: Clear fix — no reply needed

**Reviewer comment:** "The encoding fallback silently swallows UnicodeDecodeError — this should log a warning."

**Action:** Add `logger.warning()` with the filename and error details. Add a test. Commit. No reply necessary.

### Example 2: Disagreement — explain your reasoning

**Reviewer comment:** "This should use a class instead of a dict for the config object."

**Response:**
> I considered a class but chose a dict here because:
> 1. The config is only used in this one function and never passed around.
> 2. The existing codebase uses dicts for similar short-lived config (see `src/api/handlers.py` line 45).
> 3. Adding a class would introduce a new type for a single call site.
>
> Happy to change if you feel strongly — just want to share the reasoning.

### Example 3: Clarification — explain with evidence

**Reviewer comment:** "Why did you change the encoding detection order?"

**Response:**
> UTF-8 is attempted first because it covers >95% of our uploads (checked the last 30 days of production logs). Falling back to `chardet` detection only runs for the rare non-UTF-8 case, which saves the detection overhead on the common path.

## Troubleshooting

**Not sure whether a comment is actionable or a suggestion.**
Cause: The reviewer didn't categorize their feedback.
Solution: If it identifies a bug or standard violation, fix it. Style preferences are suggestions — address them, but note when you've made a judgment call.

**Too many comments to address in one pass.**
Cause: Large PR or thorough review.
Solution: Group related comments and address them together in logical commits. Batch code changes by area rather than by comment order.

**Reviewer keeps requesting new changes each round.**
Cause: Scope creep during review, or issues revealed by prior fixes.
Solution: If new feedback is on code you just changed, address it. If it's on code outside your PR's scope, note that it's a separate concern and offer to file a follow-up issue.

## Verification

Actionable feedback is fixed. Comments that need discussion have responses. The branch passes all checks. The PR is ready for re-review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/waieez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
