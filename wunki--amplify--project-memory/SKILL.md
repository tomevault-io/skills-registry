---
name: project-memory
description: Reads, writes, and applies durable project-specific memory stored in Use when this capability is needed.
metadata:
  author: wunki
---

# Project Memory

Store and apply project-specific memory from `MEMORY.md` in the repository root.

## Initial session behavior

At the start of a session in a project directory:

1. Check for `MEMORY.md` at the repository root (the directory containing `.git`).
2. If it exists, read it and acknowledge with a short summary: "Loaded project memory: [key points]"
3. State explicitly how the loaded memory affects this session.
4. If `MEMORY.md` does not exist, continue without comment.

## Capturing new memory

When the user asks to save memory or corrects recurring behavior:

1. Determine whether the information is worth persisting:
   - Yes: repeated mistakes, workflow preferences, project conventions, architectural decisions, environment-specific notes
   - No: one-off task details, temporary context, information already in code comments or docs
2. If `MEMORY.md` does not exist, create it using the format in the "Memory file format" section below.
3. Choose the most appropriate category:
   - **User Preferences** — how the user wants the agent to behave (style, workflow, tone)
   - **Project Conventions** — code patterns, naming rules, tooling choices specific to this project
   - **Past Mistakes** — errors that happened and should not repeat
   - **Environment Notes** — local setup, external services, infrastructure specifics
   - If none fit, create a new category heading that accurately describes the content.
4. Append the entry under the chosen category using the entry format below.
5. Confirm: "Saved to project memory: [what was saved]"

## Memory file format

```markdown
# Project Memory

## User Preferences
### [YYYY-MM-DD] Short title
Context: What triggered this entry
Memory: What to remember
Action: How to behave next time

## Project Conventions
### [YYYY-MM-DD] Short title
Context: ...
Memory: ...
Action: ...

## Past Mistakes
### [YYYY-MM-DD] Short title
Context: ...
Memory: ...
Action: ...

## Environment Notes
### [YYYY-MM-DD] Short title
Context: ...
Memory: ...
Action: ...
```

## Applying memory

Before starting substantial work in a session where `MEMORY.md` has been loaded:

1. Scan all entries relevant to the current task.
2. Call out applicable lessons before acting, e.g. "Memory note: [lesson] — applying by [behavior]."
3. Let the lessons guide decisions throughout the task.
4. If a memory entry conflicts with the current user request, surface the conflict and ask for clarification before proceeding.
5. If two memory entries conflict with each other, flag both and ask the user which takes precedence.

## Pruning memory

When the user asks to prune or clean up memory, or when an entry is clearly obsolete:

1. Identify entries that no longer apply (superseded decisions, resolved issues, outdated environment notes).
2. Confirm with the user before deleting any entry: "Removing: [entry title] — reason: [why it's obsolete]. OK?"
3. Delete only after explicit user confirmation.

## Constraints

- Never store secrets, tokens, credentials, API keys, or personal data in `MEMORY.md`.
- Keep entries concise and actionable — record durable behavior rules, not long transcripts.
- Prefer concrete, specific rules over vague advice.
- `MEMORY.md` is always at the repository root. In a monorepo, use the root of the specific sub-project being worked on, not the monorepo root, unless the memory applies repo-wide.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wunki) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
