---
name: redis-config-generator
description: Generate Redis configuration files and connection code for caching and session management. Triggers on "create redis config", "generate redis configuration", "redis setup", "cache config". Use when this capability is needed.
metadata:
  author: ehtbanton
---

# Redis Config Generator

Generate Redis configuration files and TypeScript client setup for caching.

## Output Requirements

**File Output:** `redis.conf`, `redis.ts`
**Format:** Valid Redis configuration and TypeScript
**Standards:** Redis 7.x, ioredis

## When Invoked

Immediately generate Redis configuration and client connection code.

## Example Invocations

**Prompt:** "Create Redis config for production caching"
**Output:** Complete `redis.conf` and `redis.ts` client setup.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ehtbanton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
