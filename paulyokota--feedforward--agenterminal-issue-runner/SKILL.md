---
name: issue-runner
description: Process a single GitHub issue through the full dev lifecycle. When running under the orchestrator, you receive one issue at a time with a fresh session. Use when this capability is needed.
metadata:
  author: paulyokota
---

# Issue Runner

Process a single GitHub issue through the full development lifecycle: branch, plan, implement, test, review, PR, and merge.

## issue-progress.json

Write `issue-progress.json` to the **exact** path in `$AGENTERMINAL_PROGRESS_PATH` (absolute). Falls back to `./issue-progress.json` if the env var is not set. The orchestrator polls this file to detect success or failure, so **you must update it after every phase transition**.

Use this **exact** flat JSON shape — the orchestrator parser depends on `"phase"` being a top-level key:

```json
{
  "issue_number": 95,
  "phase": "planned",
  "error": null
}
```

Do NOT nest it under an `"issues"` key or use the issue number as a key. Always keep `"phase"` at the top level.

Valid phases: `pending`, `branched`, `planned`, `implemented`, `tested`, `reviewed`, `merged`, `complete`, `skipped`.

## Per-Issue Workflow

### Phase 1: Read & Branch

1. Read issue details: `gh issue view <number>`
2. Verify you are on the correct branch (`git branch`). The orchestrator pre-creates your branch via git worktree. Do not run `git checkout -b`.
   - If the env var `$AGENTERMINAL_PROGRESS_PATH` is **not** set, you are running standalone — create a branch yourself: `git checkout -b fix/<number>-<slug>` (prefix `fix/<number>-`, slug: lowercase, non-alphanumeric → hyphens, max 40 chars).
3. Update issue-progress.json: `phase: "branched"`

### Phase 2: Plan

1. Analyze the issue and the codebase
2. Write a plan file: `plan-issue-<number>.md`
3. Submit for plan review (the tool blocks until review completes):

```
agenterminal.request {
  type: "plan_review",
  description: "Implementation plan for issue #<number>: <title>",
  plan_path: "plan-issue-<number>.md",
  issue_body: "<full GitHub issue body text>",
  auto_dispatch: true
}
```

**Important**: Always pass `issue_body` (the full issue description) so the plan reviewer can verify the plan covers all issue requirements.

4. The tool returns `{ "review_approved": true/false, "feedback": "..." }`.
5. If `review_approved` is false: revise the plan based on feedback, then submit a **new** `agenterminal.request` with the same `conversation_id` and `auto_dispatch: true`.
6. Max 3 review rounds
7. When `review_approved` is true, update issue-progress.json: `phase: "planned"`
8. Do NOT use `agenterminal.conversation` tools — review results are returned inline.

### Phase 3: Implement

1. Implement the fix according to the approved plan
2. Update issue-progress.json: `phase: "implemented"`

### Phase 4: Test

1. Run the project's test suite (detect test command from package.json scripts, Makefile, etc.)
2. If tests fail, attempt to fix (max 2 attempts)
3. If tests pass, update issue-progress.json: `phase: "tested"`

### Phase 5: Code Review

1. Commit all changes with a descriptive message referencing the issue number
2. Submit for code review (the tool blocks until review completes):

```
agenterminal.request {
  type: "code_review",
  description: "Fix for issue #<number>: <title>",
  ref: "main..HEAD",       # ALWAYS use "main..HEAD" — never a branch name
  plan_path: "plan-issue-<number>.md",
  issue_body: "<full GitHub issue body text>",
  auto_dispatch: true
}
```

**Important**: Always pass `plan_path` (the approved plan file) and `issue_body` (the full issue description) so the reviewer can verify the implementation covers all requirements.

3. The tool returns `{ "review_approved": true/false, "feedback": "..." }`.
4. If `review_approved` is false: fix the MUST-FIX items, commit, then submit a **new** `agenterminal.request` with the same `conversation_id` and `auto_dispatch: true`.
5. Max 3 review rounds
6. When `review_approved` is true, update issue-progress.json: `phase: "reviewed"`
7. Do NOT use `agenterminal.conversation` tools — review results are returned inline.

### Phase 6: Create PR & Merge

1. Push branch: `git push -u origin HEAD`
2. Create PR: `gh pr create --title "Fix #<number>: <title>" --body "<description>"`
3. Poll for CI completion (check every 30s, max 10 min):

```
agenterminal.github.poll { pr_number: <pr_number> }
```

4. Merge with gated tool:

```
agenterminal.merge {
  conversation_id: "<reviewConversationId>",
  pr_number: <pr_number>,
  merge_method: "squash"
}
```

5. If merge blocked, address blockers (update branch, wait for CI, etc.)
6. Update issue-progress.json: `phase: "merged"`
7. If not running under the orchestrator, switch back to main: `git checkout main && git pull`

### Phase 7: Cleanup

1. Close the issue if not auto-closed: `gh issue close <number> --comment "Fixed in PR #<pr>"`
2. Delete the plan file: `rm -f plan-issue-<number>.md` (development artifact, not a deliverable)
3. Update issue-progress.json: `phase: "complete"`

## Error Handling

### Retry Limits

- Plan review rejection: max 3 attempts, then set `phase: "skipped"`, `error: "plan_rejected"`
- Test failure: max 2 fix attempts, then set `phase: "skipped"`, `error: "tests_failing"`
- Code review rejection: max 3 revision rounds, then set `phase: "skipped"`, `error: "review_rejected"`
- Merge failure: 1 retry after addressing blockers, then set `phase: "skipped"`, `error: "merge_failed"`

### Review Unavailability

If `agenterminal.request` returns `review_approved: false` with feedback indicating the reviewer was unavailable (e.g., "Reviewer unavailable", "timed out", or empty feedback), the review never happened. This is not a rejection — and it is not approval either.

- Retry the review request once.
- If the retry also returns unavailable, set `phase: "skipped"`, `error: "reviewer_unavailable"` and stop working. The orchestrator will detect this and move on to the next issue.
- Do not continue to the next workflow phase without an actual review result (approved or rejected with substantive feedback).

### Recovery

- On any unrecoverable error, update `issue-progress.json` with `phase: "skipped"` and `error: "<reason>"`
- If not running under the orchestrator (no `$AGENTERMINAL_PROGRESS_PATH`), switch back to main branch: `git checkout main && git pull`

## Tips

- Always ensure you are on the correct branch before making changes
- Commit frequently with descriptive messages referencing the issue number
- Do NOT commit plan files (`plan-issue-*.md`) — they are gitignored development artifacts
- If the issue is ambiguous, ask clarifying questions directly in conversation text before planning
- For the CI polling in Phase 6, use `agenterminal.github.poll` to also catch any new review comments that arrive during the wait
- Update issue-progress.json after EVERY phase transition — the orchestrator depends on it

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/paulyokota) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
