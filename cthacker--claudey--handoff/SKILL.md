---
name: handoff
description: Package the current session state into HANDOFF.md so the next agent (or future you) can resume quickly. Use when ending a session, switching contexts, or handing off to another agent. Use when this capability is needed.
metadata:
  author: cthacker
---

# Handoff

Package the current state so the next agent (or future you) can resume quickly. Write the result to `HANDOFF.md` in the project root.

## Gather (in order)

1. **Scope / status** — what you were doing, what's done, what's pending, and any blockers.

2. **Working tree** — run `git status -sb`, `git log --oneline -10`, and `git diff --stat`. Note whether there are local commits not yet pushed.

3. **Branch / PR** — current branch, relevant PR number/URL, CI status if known.

4. **Tests / checks** — which commands were run, their results, and what still needs to run.

5. **Next steps** — ordered bullets the next agent should do first.

6. **Risks / gotchas** — any flaky tests, credentials, feature flags, or brittle areas.

## Output

Write a concise bullet list to `HANDOFF.md` using this structure:

```markdown
# Handoff

## Scope / Status
- Working on: <what>
- Done: <what's complete>
- Pending: <what remains>
- Blockers: <any>

## Working Tree
<git status -sb output>
- Unpushed local commits: yes/no

## Uncommitted Changes
- `<file>` — <why this file changed>
- `<file>` — <why this file changed>

## Branch / PR
- Branch: `<branch>`
- PR: <url or "none">
- CI: <status or "unknown">

## Tests / Checks
- Ran: <commands and results>
- Still needed: <what hasn't been run>

## Next Steps
1. <first thing to do>
2. <second thing>

## Risks / Gotchas
- <anything the next person should watch out for>
```

Confirm to the user that the handoff document has been written and summarize the key points.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cthacker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
