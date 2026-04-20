---
name: resume-handoff
description: Resume work from a handoff stored under .ai/shared/handoffs. Use when a user asks to resume a handoff, provides a handoff path, or provides a ticket ID like ENG-1234. Use when this capability is needed.
metadata:
  author: danaidev
---

# Resume Handoff

## Overview
Read a handoff document, validate its context against the current repo state, and propose a concrete next-step plan before changing code.

## Entry Handling

### A) Path provided
- Read the handoff file fully.
- Read any referenced artifacts (plans, research, specs) the handoff calls out.
- Validate file references from “Recent Changes” and “Learnings”.
- Summarize findings and propose next actions; ask for confirmation.

### B) Ticket provided (e.g., ENG-1234)
- Look for handoffs in `.ai/shared/handoffs/ENG-1234/`.
- List files in the directory and select the most recent by the filename timestamp `YYYY-MM-DD_HH-mm-ss`.
- If no files exist, ask the user for the path.
- Then follow the same flow as “Path provided.”

### C) No parameter provided
Respond with a short prompt asking for either a path or a ticket ID, and offer an example.

## Analysis Flow

1) **Ingest handoff content**
- Extract tasks and statuses, recent changes, learnings, artifacts, and action items.

2) **Read referenced artifacts**
- Read any linked documents, especially under:
  - `.ai/shared/plans/`
  - `.ai/shared/research/`
  - other explicit paths mentioned in the handoff

3) **Validate against repo state**
- Confirm file references still exist.
- Check for divergences (missing files, moved code, changed behavior).

4) **Report and confirm**
Present a structured summary and ask for confirmation before making changes.

Use this response pattern:
```
I've analyzed the handoff from [date] by [author].

Original tasks:
- [Task] — [Status in handoff] → [Current verification]

Key learnings (validated):
- [Learning] — [Still valid / changed]

Recent changes (verified):
- [path/to/file.ext:line] — [Present / missing / modified]

Artifacts reviewed:
- [doc] — [key takeaway]

Recommended next actions:
1. [Action]
2. [Action]

Shall I proceed with action 1, or do you want to adjust the approach?
```

## Guidelines
- Be thorough but concise; focus on what enables the next agent to act.
- Do not assume the handoff state is still valid; verify.
- Avoid big code blocks; use file references.
- Ask for confirmation before making code changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danaidev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
