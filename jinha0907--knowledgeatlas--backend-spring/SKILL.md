---
name: backend-spring
description: Use this when implementing or changing the Spring Boot backend in apps/api (controllers, services, repositories, migrations, tests). Not for frontend work.
metadata:
  author: jinha0907
---

## Workflow
1) Read docs/ARCHITECTURE.md and docs/DATA_MODEL.md first.
2) Keep layers strict: Controller -> Service -> Repository.
3) Prefer DTOs at boundaries; never expose persistence entities directly.
4) Any schema change must include a migration and a brief update to docs/DATA_MODEL.md.
5) Add at least one test for critical logic (service or repository).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jinha0907) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
