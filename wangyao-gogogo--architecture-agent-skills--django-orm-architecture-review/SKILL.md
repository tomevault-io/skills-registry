---
name: django-orm-architecture-review
description: Use when reviewing Django ORM model boundaries, querysets, migrations, managers, transactions, and data access patterns.
metadata:
  author: WangYao-GoGoGo
---

# Django ORM Architecture Review

## When To Use

- The main decision is about Django model design, queryset optimization, migration safety, or transaction boundaries.
- Reviewing N+1 query behavior, model fatness, or data migration strategy.

## Workflow

1. Identify the model structure — field types, relationships, indexes, and constraints.
2. Review queryset patterns — select_related, prefetch_related, and annotate usage.
3. Check for N+1 query patterns in serializers, templates, or DRF views.
4. Review manager and queryset design — custom managers, chaining, and reuse.
5. Check transaction boundaries — `atomic()` blocks and transaction per request.
6. Review migration safety — data migrations, lock risks, and long-running migrations.
7. Recommend the smallest structural change that improves performance or maintainability.

## Output Format

```markdown
Django ORM review:
- Model structure:
- Queryset patterns:
- N+1 analysis:
- Manager design:
- Transaction boundaries:
- Migration safety:
- Recommended change:
- Verification:
```

---
> Source: [WangYao-GoGoGo/architecture-agent-skills](https://github.com/WangYao-GoGoGo/architecture-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
