---
name: tanstack-start
description: | Use when this capability is needed.
metadata:
  author: ovachiever
---

# TanStack Start Skill [DRAFT - NOT READY]

⚠️ **Status: Release Candidate - Monitoring for Stability**

This skill is prepared but NOT published. Waiting for:
- ⏸️ v1.0 stable release (currently RC v1.136.9 as of 2025-11-18)
- ❌ GitHub #5734 resolved (memory leak with TanStack Form - OPEN as of 2025-11-02)
- ⏸️ Critical bugs stabilization period
- ⏸️ Template/reference content creation

**Current Package:** `@tanstack/react-start@1.136.9` (Nov 18, 2025)

**DO NOT USE IN PRODUCTION YET** - RC status, active memory leak issue

---

## Skill Overview

TanStack Start is a full-stack React framework with:
- Client-first architecture with opt-in SSR
- Built on TanStack Router (type-safe routing)
- Server functions for API logic
- Official Cloudflare Workers support
- Integrates with TanStack Query

---

## When v1.0 Stable

This skill will provide:
- Cloudflare Workers + D1/KV/R2 setup
- Server function patterns
- SSR vs CSR strategies
- Migration guide from Next.js
- Known issues and solutions

---

## Monitoring

Track stability at: `planning/stability-tracker.md`

**Check weekly:**
- Package: `npm view @tanstack/react-start version`
- [TanStack Start Releases](https://github.com/TanStack/router/releases)
- [Issue #5734](https://github.com/TanStack/router/issues/5734) - Memory leak blocker

---

## Installation (When Ready)

```bash
npm create cloudflare@latest -- --framework=tanstack-start
```

---

**Last Updated:** 2025-11-18
**RC Announced:** September 22, 2025
**Expected Stable:** Pending issue #5734 resolution + final RC feedback

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ovachiever) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
