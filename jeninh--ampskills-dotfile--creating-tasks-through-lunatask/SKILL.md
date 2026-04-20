---
name: creating-tasks-through-lunatask
description: Creates tasks in Lunatask for todos, reminders, deferred work, and handoffs. Use when the user wants to capture something for later, whether for themselves or for a future agent session.
compatibility: Requires Lunatask MCP or CLI tools
license: AGPL-3.0-or-later
metadata:
  author: Amolith <amolith@secluded.site>
---

**Tasks**: Reminders, todos, bug reports, feature ideas. Self-contained; the note captures what and why for the user.

**Handoffs**: Work a future agent will pick up in a fresh context window. Requires careful framing so the receiving agent knows where to start and what we learned. See [handoff.md](references/handoff.md) before creating these.

If unclear which type, ask the user "is this a task or a handoff?"

## Tone

Always include a warm, friendly, personal, and conversational note from you to the user. Address them by name if you know it. Otherwise, just start with a friendly greeting omitting their name. Task notes are exclusively a friendly note from you to the user, while handoffs _also_ include a thorough prompt for another agent to pick up. See [handoff.md#tone](references/handoff.md#tone) for more detail.

## Formatting

### Title

Titles must be clear:

- Useful and concise
- Conversational
- Include the project name only if goal doesn't exactly match the project name.
  - A task for the go-lunatask client library in area `projects` and goal `lunatask` would require `go-lunatask` in the title.
  - A task for crush in area `projects` and goal `crush` should _not_ include `crush` in the title.

Examples:

- `Accept 'completed' as status alias in lune`
- `Implement ACP event sink in Crush`
- `Investigate why lune habit track silently fails`

### Status

New tasks usually get status `later`. When beginning work on a task, update to `in-progress`, then completed when done (or use `done` shortcut).

### Note structure

**Tasks:**

```markdown
> [Friendly greeting], [context if helpful]
>
> [What needs doing and why]
>
> [Optional: relevant links, files, or commands if they'd help]
```

**Handoffs:**

```markdown
> [Friendly greeting], [context if helpful]
>
> [What needs doing and why]

---

[See references/handoff.md for how to write this section]
```

### References

Include relevant links when they'd help—issues, commits, docs, files. For handoffs, gather these proactively from previous messages; for tasks, include what's useful.

## Appending to existing notes

Only append; never overwrite unless explicitly asked.

Lunatask only supports full replacement, so reproduce the original note verbatim, then add:

```markdown
---

[New content here]
```

## Surfacing tech debt

While working, you may notice code smells, outdated patterns, missing tests, or other improvement opportunities related _or unrelated_ to the current task. We might also intentionally introduce tech debt, aware of the trade-offs. Don't let these observations or introductions disappear with the session; ask the user whether you should mention them.

If the user confirms and it's/they're

- **relevant to the handoff**: mention briefly in context section so the receiving agent is aware
- **worth tracking separately**: suggest capturing as standalone task(s) so the user can pick up or hand off to another agent later. Even as standalone tasks, make sure to include enough context that another agent could pick it up.

## Working around a Lunatask bug

At the very very end of task notes or note contents, include `[editor_v2]::` on its own line.

---

If `lunatask` or `lune` MCP tools are unavailable, see [cli.md](references/cli.md). If the CLI is also unavailable, see [installing-lune.md](references/installing-lune.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeninh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
