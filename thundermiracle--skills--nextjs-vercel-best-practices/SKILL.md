---
name: nextjs-vercel-best-practices
description: Apply official Next.js and Vercel best practices for architecture, rendering, caching/revalidation, performance, security, deployment environments, and production readiness. Use when building, reviewing, or deploying Next.js applications on Vercel, creating launch checklists, auditing pull requests, or fixing production-readiness gaps. Use when this capability is needed.
metadata:
  author: thundermiracle
---

# Next.js Vercel Best Practices

## Overview

Use only official Next.js and Vercel documentation as the source of truth. Convert documentation guidance into actionable code changes, review findings, and rollout checklists.

## Workflow

1. Identify context: Next.js version, App Router or Pages Router, deployment environment, runtime, and data store location.
2. Select references by task:

| Task | Reference |
| --- | --- |
| Rendering strategy, caching, security, app performance | `references/nextjs-official.md` |
| Preview/production workflow, environment setup, deployment hardening | `references/vercel-official.md` |
| Full launch or production audit | Read both files |

3. Produce guidance in this order:
- Critical reliability or security fixes first
- Performance and cost optimizations second
- Developer experience and maintainability improvements last

4. Attach an official source URL to every non-trivial recommendation.
5. End with a verification plan (`next build`, preview deployment checks, promotion or rollback criteria).

## Output Format

- `Findings`: concrete risks or gaps in the current implementation.
- `Recommended changes`: minimal patch plan, ordered by impact.
- `Performance plan`: baseline metrics (`LCP/INP/CLS/TTFB`), suspected bottleneck, concrete fix, expected metric impact.
- `Verification`: how to confirm behavior in preview and production.
- `Sources`: direct official links used for each recommendation.

## Guardrails

- Prefer official documentation over memory whenever there is ambiguity.
- Prefer Server Components and platform defaults before adding custom infrastructure.
- Keep secrets server-side and avoid client exposure of sensitive values.
- Use preview deployments for risky changes before production promotion.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thundermiracle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
