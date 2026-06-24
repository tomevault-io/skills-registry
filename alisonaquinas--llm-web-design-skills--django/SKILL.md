---
name: django
description: > Use when this capability is needed.
metadata:
  author: alisonaquinas
---

# Django

Use this skill to keep Django work grounded in maintainable architecture, predictable delivery, and framework-appropriate conventions.

## Intent Router

| Need | Load |
| --- | --- |
| core conventions, structure, review priorities, and common red flags for Django | `references/best-practices.md` |
| setup, migration, testing, build, delivery, and day-two workflow guidance for Django | `references/workflows.md` |

## Quick Start

1. Confirm Django and Python versions, template-driven, API-driven, or hybrid delivery, database backend, and background-task or async posture.
2. Identify whether the task is greenfield scaffolding, incremental refactoring, troubleshooting, migration planning, or code review.
3. Apply the defaults in `references/best-practices.md` before proposing custom architecture.
4. Prefer the smallest change set that improves clarity, safety, operability, and long-term maintainability.

## Workflow

- start from the business capability that owns the code
- separate settings and infrastructure wiring from domain logic
- treat migrations and permissions as high-impact design choices
- verify manage.py test, migrations, and static handling after changes

## Typical Focus Areas

- Django and Python versions
- template-driven, API-driven, or hybrid delivery
- database backend
- background-task or async posture

## Outputs to Prefer

- summarize delivery model, database, and deployment constraints first
- group findings by app boundaries, models, validation, settings, migrations, and tests
- protect auth and migration safety during refactors

## First Response Pattern

- restate Django version, delivery model, database backend, and async or task posture before suggesting changes
- anchor the plan at the owning app, view, serializer, form, or migration boundary
- name the verification loop up front: Django tests, migration safety checks, and one real request path through the changed behavior

## Common Requests

```text
Review this Django feature for app boundaries, model design, form or serializer flow, and test gaps.
```

```text
Help refactor a Django app toward cleaner service boundaries and safer configuration.
```

## Safety Notes

- preserve externally visible behavior unless the user explicitly requests a redesign or a breaking change
- avoid style-only churn when the current repository already has working conventions and automation

---
> Source: [alisonaquinas/llm-web-design-skills](https://github.com/alisonaquinas/llm-web-design-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
