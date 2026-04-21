---
name: triage-issue
description: > Use when this capability is needed.
metadata:
  author: waieez
---

# Triage Issue

Turn an objective into a GitHub issue that a contributor can pick up and act on without guesswork.

## Procedure

1. **Understand the objective.** Read the objective and any surrounding context. Identify what needs to change and why.

2. **Check for duplicates.** Search open issues for existing coverage — match on objective, not just title. An issue is a duplicate if it covers the same change for the same reason. If a matching issue exists, comment on it rather than creating a new one.

3. **Write the title.** Use imperative mood. It should complete the sentence: "This issue will ___."

4. **Write the description.** Include:
   - **Context** — what exists today and why it's insufficient.
   - **Objective** — what should be true when this is done.
   - **Acceptance criteria** — specific, verifiable conditions for completion.
   - **Scope boundaries** — what is explicitly out of scope, if anything.

5. **Apply labels.** Use existing repository labels that match the issue type and domain. Do not invent labels.

6. **Review for clarity.** Read the issue as if you have no prior context. If anything requires assumption, tighten it.

7. **Submit the issue.** Confirm the issue is actionable as written.

## When Steps Fail

- **No matching labels exist.** Skip labeling and note "no applicable labels" in the issue. Do not create labels without repository-owner approval.
- **Duplicate is ambiguous.** When an existing issue partially overlaps, link to it in the new issue's context section and explain what is different about this one.
- **Objective is too vague to write acceptance criteria.** Stop. Comment back to the requester with specific questions rather than filing a vague issue.

## Examples

### Example 1: Bug report from a user complaint

**Input:** "Users are seeing a 500 error when they upload CSVs with Unicode characters."

**Title:** Fix 500 error on CSV upload with Unicode characters

**Description:**
- **Context:** The `/api/upload` endpoint returns 500 when a CSV file contains non-ASCII characters. Users in non-English locales hit this on every upload.
- **Objective:** CSV uploads with Unicode characters succeed and are processed correctly.
- **Acceptance criteria:**
  1. Uploading a CSV containing Unicode characters returns 200.
  2. The imported data preserves the original Unicode content.
  3. A regression test covers this case.
- **Scope boundaries:** This does not cover other file formats (XLSX, TSV).

### Example 2: Feature request from a planning discussion

**Input:** "We need retry logic for the webhook delivery system."

**Title:** Add retry logic to webhook delivery

**Description:**
- **Context:** Webhook deliveries fail silently when the recipient endpoint is temporarily unavailable. There is no retry mechanism.
- **Objective:** Failed webhook deliveries are retried with exponential backoff.
- **Acceptance criteria:**
  1. Failed deliveries retry up to 3 times with exponential backoff (1s, 4s, 16s).
  2. Each retry attempt is logged with the HTTP status code.
  3. After all retries are exhausted, the delivery is marked as failed in the database.
  4. A manual retry endpoint exists for operations to re-trigger failed deliveries.
- **Scope boundaries:** Does not include a dead-letter queue or webhook delivery UI. Those are separate issues.

## Troubleshooting

**Issue is too broad to act on.**
Cause: The objective covers multiple independent changes.
Solution: Split into separate issues — one per logical change. Link them with a tracking issue if they share a goal.

**Acceptance criteria are not verifiable.**
Cause: Criteria use subjective language ("should be fast", "works well").
Solution: Replace with measurable conditions ("responds in under 200ms at p95", "passes the existing integration test suite").

**Cannot determine the right labels.**
Cause: Repository label taxonomy is unclear or undocumented.
Solution: Check for a CONTRIBUTING.md or label description in the repository settings. When in doubt, skip labels and let a maintainer apply them.

## Verification

A contributor can read the issue and begin work without asking clarifying questions. The acceptance criteria are specific enough to verify objectively.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/waieez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
