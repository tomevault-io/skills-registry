---
name: django-app-architecture-review
description: Use when reviewing Django application architecture, app structure, models/views/serializers, middleware, signals, querysets, and migration boundaries.
metadata:
  author: WangYao-GoGoGo
---

# Django App Architecture Review

## When To Use

- The main decision is about Django project layout, app boundaries, model design, or queryset optimization.
- Reviewing signal chains, migration strategies, or serializer/view responsibilities.

## Workflow

1. Identify Django app boundaries and whether they follow domain ownership or technical layers.
2. Review model design — field types, indexes, constraints, and relationship direction.
3. Check view and serializer responsibilities — are they handling HTTP or also domain logic?
4. Review signal chains for ordering, side effects, and failure behavior.
5. Check querysets for N+1 queries and unnecessary database work.
6. Review migration strategy — are squashed migrations and data migrations planned?
7. Recommend the smallest change that clarifies boundaries or reduces risk.

## Output Format

```markdown
Django architecture review:
- App boundaries:
- Model design:
- View & serializer responsibilities:
- Signal risks:
- Query performance:
- Migration strategy:
- Recommended change:
- Verification:
```

---
> Source: [WangYao-GoGoGo/architecture-agent-skills](https://github.com/WangYao-GoGoGo/architecture-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
