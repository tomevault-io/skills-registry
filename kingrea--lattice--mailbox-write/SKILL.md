---
name: mailbox-write
description: Append validated NDJSON messages to role mailboxes and update index. Use when this capability is needed.
metadata:
  author: kingrea
---

# Mailbox Write

Message format (single JSON object per line):

- `ts` (RFC3339 UTC)
- `from`
- `to`
- `loop` (integer)
- `kind`
- `summary`
- `beads` (array)
- `refs` (array)
- `thread` (optional)

Steps:

1. Validate required fields.
2. Append to `mailbox/<from>.ndjson`.
3. Optionally append to `mailbox/chatroom.ndjson`.
4. Update `mailbox/index.json` counters and last-message timestamps.
5. Return success with target file and line count.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kingrea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
