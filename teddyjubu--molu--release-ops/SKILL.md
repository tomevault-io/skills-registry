---
name: release-ops
description: Standardizes staging/prod deploy, CI/CD gates, rollback, and monitoring. Invoke when configuring pipelines, releasing, or handling incidents. Use when this capability is needed.
metadata:
  author: teddyjubu
---

# Release Operations

## Purpose

Make deployments safe and repeatable:
- CI checks (typecheck, lint, tests, coverage, build)
- Staging environment parity with production
- Rollback procedures
- Monitoring and alerting for payments/webhooks

## When to Invoke

Invoke this skill when:
- Setting up CI/CD
- Adding new environment variables or secrets
- Promoting staging → production
- Investigating production incidents (failed payments, webhook errors)

## Release Gates

- Tests pass and coverage ≥ 80%
- No TypeScript errors
- Lighthouse baseline meets targets for critical pages
- Webhook endpoints verified and monitored

## Rollback Strategy

- Prefer deployment rollback (platform revert) over emergency code edits.
- Database changes must be additive and reversible.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teddyjubu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
