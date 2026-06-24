---
name: review-pull-request
description: > Use when this capability is needed.
metadata:
  author: waieez
---

# Review Pull Request

Evaluate a pull request and provide feedback that helps the contributor improve or confirms the work is ready.

## Procedure

### Phase 1 — Orientation

Gather context before making judgments.

1. **Read the diff first.** Form your own understanding of what the change does before reading the PR description.

2. **Read the description and linked issue.** Compare your understanding to what was claimed. Flag any mismatch between the description and the actual change.

3. **Check contribution standards.** Look for the repository's contributing guide, style rules, and CI requirements. If the repository doesn't define its own, use [ours](../../CONTRIBUTING.md) as a reference.

### Phase 2 — Deep Review

Select the dimensions relevant to the scope and risk of the change. For each, state a verdict: **pass**, **concern** (with specifics), or **not applicable**.

#### 1. Problem Fit

Does this change actually solve the stated problem?

- Compare the diff to the linked issue or stated goal.
- A change can be well-written but solve the wrong problem.

#### 2. Correctness

Is the solution logically and technically correct?

- Trace the logic for boundary cases, error paths, and concurrency concerns.
- Verify objective evidence — test results, CI output, before/after comparisons. If evidence is missing, request it. If evidence *cannot* be provided, note the gap and suggest a mitigation.

#### 3. Minimality

Is this the smallest change that solves the problem?

- Flag unrelated changes, unnecessary refactors, or feature creep.
- A minimal change is easier to review, revert, and reason about.

#### 4. Idiomaticity

Does the code follow the idioms and conventions of its language and framework?

- Look for non-idiomatic patterns: fighting the type system, reimplementing standard library functionality, ignoring language-specific error handling conventions.
- Idiomatic code is easier for the next contributor to maintain.

#### 5. Project Conventions

Does the change follow the project's own standards?

- Check against the contributing guide, style guide, linter configuration, and architectural patterns already in the codebase.
- Standards include naming, file organization, commit message format, and documentation expectations.

#### 6. Test Coverage

Are tests present, minimal, and high-quality?

- Confirm tests cover acceptance criteria from the issue.
- Check for meaningful edge cases — not just the happy path.
- Tests should be the smallest set that proves the change works, not exhaustive enumeration.

#### 7. Documentation

Are changes to behavior reflected in documentation?

- Check commit messages, code comments, README updates, and inline docs.
- If the change alters public-facing behavior, external documentation must be updated.
- The *why* matters as much as the *what*.

#### 8. Security

Does the change introduce or overlook security concerns?

- Look for: unsanitized input, leaked credentials, broken auth, exposed internals, new attack surface.
- If no security surface is touched, state "not applicable" and move on.

#### 9. Performance

Does the change introduce or overlook performance concerns?

- Look for: unnecessary allocations, missing indexes, N+1 queries, blocking calls in hot paths.
- If no performance-sensitive path is touched, state "not applicable" and move on.

### Phase 3 — Feedback & Recommendation

4. **Write feedback.** Be specific and actionable. Reference exact lines or files. Use inline comments for precise issues and a summary comment for high-level observations. Categorize feedback by severity:
   - **Blocking** — must be fixed before merge (bugs, incorrect logic, missing tests, standard violations).
   - **Suggestion** — would improve the change but does not block merge (style, naming, minor refactors).
   - **Observation** — context or questions for the contributor, no action required (design alternatives, future considerations).

5. **State your recommendation.** Either:
   - **Approve** — all dimensions pass or have only non-blocking suggestions, evidence confirmed, no outstanding blocking issues.
   - **Request changes** — specify exactly what needs to change and why, limited to blocking items.
   - **Comment** — observations that don't block merge but should be considered.

## When Steps Fail

- **PR description is missing or inadequate.** Request a proper description before reviewing the code. Without context on *why*, you cannot assess problem fit.
- **Evidence cannot be provided.** Some changes (UI, infrastructure, environment-specific behavior) are hard to prove in CI. Note the gap explicitly, suggest a mitigation (manual test script, screenshot, screencast), and assess the rest of the PR normally.
- **Contributor disagrees with feedback.** If the disagreement is on standards, cite the specific standard. If it's on approach, present your reasoning with evidence and be open to being wrong. If you cannot converge, recommend involving a third party.

## Examples

### Example 1: Approving a clean PR

**Deep review verdicts:**
> 1. **Problem fit** — pass. Change matches the acceptance criteria in #42.
> 2. **Correctness** — pass. Logic handles empty input and error paths. CI green.
> 3. **Minimality** — pass. No unrelated changes.
> 4. **Idiomaticity** — pass. Follows standard Python patterns.
> 5. **Project conventions** — pass. Matches style guide and commit format.
> 6. **Test coverage** — pass. Tests cover acceptance criteria and the empty-input edge case.
> 7. **Documentation** — pass. Docstring updated to reflect the new parameter.
> 8. **Security** — not applicable. No user input or auth surface involved.
> 9. **Performance** — not applicable. No hot path affected.

**Summary comment:**
> Clean implementation. All review dimensions pass.
>
> One minor suggestion on naming (inline), but it doesn't block merge.
>
> **Recommendation: Approve**

**Inline comment (suggestion):**
> `src/client.py` line 34: Consider naming this `request_timeout_seconds` instead of `timeout` to avoid ambiguity with connection timeouts. Not blocking.

### Example 2: Requesting changes

**Deep review verdicts:**
> 1. **Problem fit** — pass. Addresses the upload failure described in #87.
> 2. **Correctness** — concern. The encoding fallback in `upload.py` silently swallows `UnicodeDecodeError` — this should log a warning at minimum.
> 3. **Minimality** — pass. Scoped to the upload path.
> 4. **Idiomaticity** — pass.
> 5. **Project conventions** — pass.
> 6. **Test coverage** — concern. The regression test doesn't cover the empty-input edge case from the acceptance criteria.
> 7. **Documentation** — pass.
> 8. **Security** — not applicable.
> 9. **Performance** — not applicable.

**Summary comment:**
> The approach is sound, but there are two blocking issues:
>
> 1. The regression test doesn't cover the empty-input edge case from the acceptance criteria.
> 2. The encoding fallback in `upload.py` silently swallows `UnicodeDecodeError` — this should log a warning at minimum.
>
> One suggestion inline on error message clarity.
>
> **Recommendation: Request changes**

## Troubleshooting

**Diff is too large to review effectively.**
Cause: The PR bundles multiple logical changes.
Solution: Request the contributor split the PR into smaller, reviewable units. Each PR should represent one logical change.

**CI is failing but the contributor says "it's unrelated".**
Cause: Pre-existing CI failures or flaky tests.
Solution: Verify by checking the CI failure on the default branch. If it fails there too, note it and review the PR normally. If it only fails on the PR branch, it's the contributor's responsibility to fix.

**No linked issue.**
Cause: The PR was opened without an associated issue.
Solution: Ask the contributor to link an issue or explain the motivation directly in the description. Without a "why", problem fit assessment is guesswork.

## Verification

Every piece of feedback references a specific part of the change. The contributor can act on each comment without asking for clarification. The recommendation is grounded in per-dimension verdicts, and feedback is categorized by severity.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/waieez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
