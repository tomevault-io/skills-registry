---
name: error-boundary-generator
description: Generates error boundaries and error UI for routes and components. Use when creating new routes/components or user says "add error handling".
metadata:
  author: mouayadakel
---

# Error Boundary Generator

## When to Trigger

- Creating new routes/components
- User says "add error handling"

## What to Do

1. **Route error.tsx**: In app router, add error.tsx that catches errors, logs (e.g. to Sentry), and renders fallback UI with "Try again" (reset). In production show generic message; in dev can show error.message.
2. **Global error**: Ensure app/layout or root has error boundary for uncaught errors.
3. **Component-level**: Wrap critical sections in error boundaries so one failure doesn’t take down the whole page.

Use 'use client' for error boundaries. Provide reset callback and optional report-to-monitoring. Match project UI (Button, layout).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mouayadakel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
