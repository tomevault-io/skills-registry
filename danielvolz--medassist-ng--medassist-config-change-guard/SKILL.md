---
name: medassist-config-change-guard
description: Validate MedAssist configuration changes across env vars, Docker compose, proxy settings, and runtime defaults, including equivalent requests phrased in German. Use when this capability is needed.
metadata:
  author: danielvolz
---

# Skill Instructions

Use this skill when changes touch `.env`, Docker files, Vite proxy settings, runtime defaults, or app startup behavior.

## Objective

Prevent configuration drift and broken local/CI environments.

## Required Checks

1. New/changed config has safe defaults.
2. Env changes are backward-compatible where feasible.
3. Docker/dev runtime changes remain consistent across services.
4. Frontend/backend URL/proxy conventions remain valid (`/api/*`).
5. Documentation reflects configuration changes.

## Files to Prioritize

- `.env.example`
- `docker-compose.yml`
- `docker-compose.dev.yml`
- `frontend/vite.config.ts`
- Relevant package scripts and startup files

## Anti-Patterns

- Hidden required env vars with no defaults.
- Inconsistent host/port/proxy settings across environments.
- Config changes without doc updates.

## Response Format

Report:

- Config files reviewed
- Compatibility impact (none/low/high)
- Required follow-up updates
- Final readiness recommendation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danielvolz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
