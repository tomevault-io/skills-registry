---
name: api-design-review
description: Guide backend feature work toward clear, consistent service boundaries. Use when this capability is needed.
metadata:
  author: damilola-elegbede-org
---

## Checklist
- Confirm the problem statement, consumers, and data contracts.
- Map request/response payloads, error models, and versioning strategy.
- Validate authentication, authorization, and rate-limiting expectations.
- Align naming with domain language; flag tight coupling or leaky abstractions.
- Define observability hooks: logs, metrics, tracing identifiers.

## Prompts
- "Walk through the existing contract and highlight breaking changes or migrations we must plan."
- "Identify one waypoint we can ship first to validate the API direction."

## Resources
- RFC template for service changes.
- Internal style guide for HTTP and gRPC endpoints.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/damilola-elegbede-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
