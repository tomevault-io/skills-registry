---
name: handover
description: > Use when this capability is needed.
metadata:
  author: s-hiraoku
---

# Handover

Generate a `HANDOVER.md` file in the project root that captures the full session context so the next Claude (or developer) can pick up exactly where this session left off.

## Workflow

1. **Analyze the conversation** — Review the entire session to identify:
   - Tasks attempted and their outcomes (completed, in-progress, blocked)
   - Bugs encountered and how they were resolved
   - Key technical decisions and their rationale
   - Unexpected issues, workarounds, and gotchas
   - Files created, modified, or deleted

2. **Identify next steps** — Determine what remains to be done:
   - Incomplete tasks with clear continuation points
   - Known issues that need attention
   - Follow-up work that was discussed but not started

3. **Map important files** — List files that are central to the work:
   - Files modified during this session
   - Configuration files relevant to the task
   - Test files related to changes

4. **Write HANDOVER.md** — Use the template below. Write in the project root directory.

5. **Confirm** — Tell the user the handover file has been created and summarize the key points.

## Output Template

Use this structure for the HANDOVER.md file. Omit any section that has no relevant content.

```markdown
# Handover

> Session: YYYY-MM-DD
> Branch: `<current-branch>`

## Summary

< 1-3 sentence overview of what this session accomplished >

## What Was Done

- < completed task 1 >
- < completed task 2 >
- ...

## What Worked / What Didn't

### Worked
- < approach or solution that succeeded >

### Didn't Work
- < approach that failed and why >
- < bug encountered > → < how it was fixed >

## Key Decisions

| Decision | Rationale |
|----------|-----------|
| < decision 1 > | < why > |
| < decision 2 > | < why > |

## Lessons Learned & Gotchas

- < non-obvious finding or pitfall >
- < important context the next session needs >

## Next Steps

- [ ] < actionable next task 1 >
- [ ] < actionable next task 2 >
- [ ] ...

## File Map

| File | Role |
|------|------|
| `<path>` | < what it does / why it matters > |
```

## Rules

- **Be concise**: Each bullet should be a single clear sentence. Avoid walls of text.
- **Be specific**: Reference actual file paths, function names, error messages, and commit hashes.
- **Be actionable**: Next steps should be concrete enough to start immediately.
- **Capture the "why"**: Decisions without rationale are not useful.
- **Include failure context**: What didn't work is often more valuable than what did.
- **Use the current date and branch**: Get these from the system, not from memory.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/s-hiraoku) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
