---
name: temporal-placement
description: Decide whether logic belongs in Workflow, Activity, Worker bootstrap, or Client, and give a minimal skeleton. Use when this capability is needed.
metadata:
  author: rebeccapowell
---

# Placement Decision: Workflow vs Activity vs Worker vs Client

## When to use
Use when the user describes logic and asks where it belongs, or when code boundaries are unclear.

## Decision rules
- **Workflow**: orchestration, durable state, timers, waiting, child workflows, calling activities.
- **Activity**: side effects, IO (DB/network/files), CPU-heavy non-deterministic work, external services.
- **Worker**: registrations, task queues, interceptors, converters/codecs, DI/host configuration.
- **Client**: start/signal/query/update workflows; idempotency keys; request shaping.

## Required output
Return:
- A 4-row table (Workflow/Activity/Worker/Client) with **Yes/No**, **Why**, and **Minimal snippet**
- A single “recommended boundary” diagram in text (e.g., bullets)

## Guardrails
- If anything touches time/randomness/IO: default to **Activity** unless proven deterministic in workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rebeccapowell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
