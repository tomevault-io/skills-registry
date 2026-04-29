---
name: queue-patterns
description: BullMQ queue configuration patterns including connection pooling, job options, rate limiting, and TypeScript types. Use when setting up queues or configuring job behavior. Use when this capability is needed.
metadata:
  author: vanman2024
---

# Queue Patterns

Skill for BullMQ queue configuration and management.

## Overview

Configure queues with:

- Redis connection pooling
- Default job options
- Rate limiting
- TypeScript type safety

## Use When

This skill is automatically invoked when:

- Setting up new queues
- Configuring job options
- Adding rate limiting
- Creating typed job definitions

## Available Scripts

| Script                   | Description               |
| ------------------------ | ------------------------- |
| `scripts/init-bullmq.sh` | Initialize BullMQ project |

## Available Templates

| Template                  | Description          |
| ------------------------- | -------------------- |
| `templates/connection.ts` | Redis connection     |
| `templates/queue.ts`      | Queue definition     |
| `templates/types.ts`      | Job type definitions |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vanman2024) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
