---
name: workon-comment
description: Add a comment to the current ClickUp ticket. Use when the user says "workon comment" or wants to add a comment to the ticket. Use when this capability is needed.
metadata:
  author: evansoderberg
---

# Add Comment to Ticket

Add a comment to the current ClickUp ticket:

```bash
workon comment "$ARGUMENTS"
```

If the user provides comment text, use it directly. If not, ask what they'd like to comment.

## Alternative: Pipe content

For longer comments or content from files:
```bash
echo "Comment content" | workon comment
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/evansoderberg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
