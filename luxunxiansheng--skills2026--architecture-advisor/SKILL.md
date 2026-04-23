---
name: architecture-advisor
description: Use when reviewing architecture boundaries, dependency direction, or multi-layer design decisions across modules.
metadata:
  author: luxunxiansheng
---

# Architecture Advisor

## Intent
Use this skill when the user asks to "review architecture", "check DDD compliance", or "plan a new feature" that involves multiple layers. 
This skill ensures the project maintains its structural integrity (Domain, Infrastructure, Application/API).

## DDD Checklist
1. **Domain Layer Integrity**: Ensure `backend/app/domain` contains only business logic (Entities, Value Objects, Domain Services) and no infrastructure details (DB models, API framework code).
2. **Infrastructure Separation**: Check that `backend/app/infrastructure` contains adapters (DB repositories, LLM integrations) and that they implement interfaces defined in the Domain layer.
3. **Dependency Direction**: Verify that dependencies always point inwards toward the Domain layer. Domain should never depend on Infrastructure.
4. **AWorld Integration**: For agent-driven features, ensure the `aworld` SDK is used correctly within the Application layer or specialized adapters.

## Output Format
### Architecture Review
- **Structural Health**: [Score 1-10]
- **Violations Found**:
  - [File Path]: [Description of violation, e.g., "Domain entity depending on SQLAlchemy model"]
- **Recommendations**:
  - [Actionable steps to refactor or improve the design]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luxunxiansheng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
