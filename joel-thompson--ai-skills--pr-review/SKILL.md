---
name: pr-review
description: Review GitHub pull requests by fetching PR metadata and diffs via GitHub tools, then producing a structured markdown review with file/line-referenced comments. Use when the user asks for a PR review, code review of a pull request, or shares a GitHub PR URL/number. Use when this capability is needed.
metadata:
  author: joel-thompson
---

# PR Review

Review a GitHub pull request and produce a **written review artifact** the author can act on.

## Strict rules

- Use the GitHub MCP server tools as the **source of truth** for PR data (`user-github-*` tools).
- Do not rely on pasted summaries, local git state, or assumptions when MCP data can be fetched.
- If required PR metadata/diff/comments/status cannot be fetched from GitHub MCP, explicitly state what is missing and why.
- All action items must go in a single `Action items` section as unchecked checkboxes (`- [ ] ...`).
- Under `Action items`, include exactly two subsections: `Code changes` and `Test changes`.
- Within each subsection, sort items by severity: **BLOCKER → IMPORTANT → NIT**.
- Every action item must include at least one file reference (`<path:line>` preferred, otherwise `<path>` + hunk identifier).
- Every testing-related action item must be labeled with severity (`[BLOCKER]`, `[IMPORTANT]`, or `[NIT]`), just like code changes.
- Put open questions in `Open questions` directly under `Summary`.
- Evidence must go in a separate `Evidence observed` section (no checkboxes).
- Testing-related TODOs must be action-only (outstanding work). If tests/checks/validation are already done, record them as evidence (not TODOs).

## Inputs

The user should provide one of:
- PR URL, or
- `owner/repo#number`, or
- `owner`, `repo`, and `PR number`

If the user has a specific focus (performance, accessibility, security, tests, etc.), prioritize that in addition to the baseline checklist below.

## Workflow (do this in order)

1. **Fetch PR context**
   - Parse user input (PR URL, `owner/repo#number`, or discrete `owner`, `repo`, `number`) and normalize to `owner`, `repo`, `number`.
   - Fetch data via GitHub MCP tools (not manual inference):
     - `user-github-get_pull_request`
     - `user-github-get_pull_request_files`
     - `user-github-get_pull_request_diff`
     - `user-github-get_pull_request_comments`
     - `user-github-get_pull_request_reviews`
     - `user-github-get_pull_request_status`
   - PR title/description, base/head, author
   - Files changed list
   - Full diff/patch
   - Existing review comments and CI/check status (if available)

2. **Triage**
   - Identify “high-risk” areas (auth, payments, data migrations, concurrency, permissions, infra).
   - Identify change type (feature/fix/refactor/chore) and expected test depth.

3. **Review changes**
   - Start with architecture and correctness, then move to maintainability/readability.
   - Review file-by-file, focusing on “what breaks later” more than style.
   - Prefer actionable feedback with a suggested direction over vague advice.

4. **Testing assessment**
   - Verify the PR has tests where it should.
   - Check tests for reliability (deterministic, minimal mocking, meaningful assertions).
   - Call out missing coverage and propose the smallest useful additions.
   - In the final report, keep testing TODOs in `Action items` and record completed validation as evidence.

5. **Produce a markdown report**
   - Create a markdown file containing the review (see template below).
   - Include **references** for each comment:
     - Prefer `path:line` when line numbers are available.
     - Otherwise use `path` + a short “hunk identifier” (function name / snippet).

## What to look for (core checklist)

- **Correctness**
  - Logic matches intent; handles edge cases and error paths.
  - Failure modes are safe (timeouts, retries, partial failures, null/undefined).
  - No accidental behavior changes in unrelated areas.

- **Maintainability**
  - Clear boundaries and responsibilities; avoids “god” functions.
  - Naming makes intent obvious; types/interfaces are coherent.
  - Duplicated logic is minimized (or justified).
  - Public APIs are stable and documented by usage, not comments.

- **Readability**
  - Straight-line code where possible; early returns over deep nesting.
  - Consistent patterns and conventions within the codebase.
  - Minimal comments; only add when the code’s intent isn’t otherwise clear.

- **Testing patterns**
  - Tests match the layer (unit vs integration vs e2e) and assert behavior, not implementation.
  - Avoid brittle selectors/time-based sleeps in UI tests.
  - Coverage for critical logic and regressions implied by the PR.

- **Security & privacy (when relevant)**
  - Input validation, authorization checks, and safe defaults.
  - No secrets in code, logs, screenshots, fixtures.

## When applicable: use related skills as “lenses”

- **React/Next.js performance**: apply `vercel-react-best-practices` when reviewing React/Next.js changes.
- **UI/UX & accessibility**: apply `web-design-guidelines` when reviewing UI changes.

## Output: write the review file

Default filename: `pr-review-<repo>-<number>.md` (unless the user requests a different name/location).

Notes:
- Use the **repo name** (not full `owner/repo`) to match the default pattern.
- Slugify if needed (lowercase; replace spaces and `/` with `-`).

Use this template:

```markdown
# PR Review: <owner>/<repo>#<number> — <title>

## Summary
- <1–3 bullets describing what changed and overall assessment>

## Open questions
- <question 1>

## Action items
### Code changes
- [ ] **[BLOCKER]** <concise action> — `<path:line>`
- [ ] **[IMPORTANT]** <concise action> — `<path:line>`
- [ ] **[NIT]** <concise action> — `<path:line>`

### Test changes
- [ ] **[BLOCKER]** <concise action> — `<test-path:line>`
- [ ] **[IMPORTANT]** <concise action> — `<test-path:line>`
- [ ] **[NIT]** <concise action> — `<test-path:line>`

## Evidence observed
- Existing coverage: <what already exists + where>
- CI/check status: <passed/failed jobs>
- Prior manual verification noted in PR: <brief note>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joel-thompson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
