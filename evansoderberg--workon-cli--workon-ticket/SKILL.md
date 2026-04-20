---
name: workon-ticket
description: Get context on the current ClickUp ticket. Use when the user asks "what am I working on?", needs ticket context, or says "workon ticket". Use when this capability is needed.
metadata:
  author: evansoderberg
---

# Get Ticket Context

Run this command to get information about the current task from ClickUp:

```bash
workon ticket
```

This outputs:
- Ticket title
- Status
- URL
- Description (including acceptance criteria)

## When to Use

- Starting a new coding session on an existing branch
- The user asks "what am I working on?" or similar
- You need to understand requirements before implementing
- The user explicitly says "workon ticket"

Use the ticket information to understand what the user is trying to accomplish and any acceptance criteria or requirements.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/evansoderberg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
