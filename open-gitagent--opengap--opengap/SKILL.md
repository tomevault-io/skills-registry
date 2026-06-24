---
name: packet-factory
description: > Use when this capability is needed.
metadata:
  author: open-gitagent
---

# Packet Factory

The packet factory is the gatekeeper between intent and execution. Nothing with a side effect runs without an APPROVED packet.

## Commands

```bash
# Create a new packet (DRAFT status)
skills/packet-factory/new-packet.sh \
  --intent "Post daily update to Slack #arc-angels" \
  --source "Ludo DM 2026-04-01 09:00" \
  --destination "Slack #arc-angels"

# Review a packet (set APPROVED or REJECTED)
skills/packet-factory/review-packet.sh --id 2026-04-01-001

# Dispatch an APPROVED packet (executes and logs output)
skills/packet-factory/dispatch-packet.sh --id 2026-04-01-001
```

## Packet Queue

All in-flight packets live in `memory/packet-queue.md`.

The heartbeat pre-flight check reads this file. DRAFT packets older than 24h are escalated to Ludo.

## Scope Lock Rule

Once a packet is APPROVED, `scope_locked_at` is set and the scope field is frozen.

If the task grows (new deliverable discovered, Ludo asks for something extra), the current packet is dispatched as-is and a **new packet** is created for the expansion. Never mutate an approved packet's intent or output.

This prevents the single most common failure mode: scope creep → context balloon → session death.

---
> Source: [open-gitagent/opengap](https://github.com/open-gitagent/opengap) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
