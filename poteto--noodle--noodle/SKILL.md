---
name: schedule
description: Reads backlog and produces multi-stage work orders including test and deploy stages. Use when this capability is needed.
metadata:
  author: poteto
---

# Schedule

Read `brain/todos.md` for backlog items. Write `.noodle/orders-next.json` with orders.

Build multi-stage pipelines. A typical order:

1. `execute` — implement the change
2. `test` — run the test suite against the change
3. `deploy` — deploy if tests pass

```json
{
  "orders": [
    {
      "id": "1",
      "title": "the backlog item title",
      "rationale": "why this order was scheduled",
      "status": "active",
      "stages": [
        {"task_key": "execute", "skill": "execute", "provider": "claude", "model": "claude-sonnet-4-6", "runtime": "process", "status": "pending"},
        {"task_key": "test", "skill": "test", "provider": "claude", "model": "claude-sonnet-4-6", "runtime": "process", "status": "pending"},
        {"task_key": "deploy", "skill": "deploy", "provider": "claude", "model": "claude-sonnet-4-6", "runtime": "process", "status": "pending"}
      ]
    }
  ]
}
```

When no unchecked items remain, write `{"orders": []}`.

---
> Source: [poteto/noodle](https://github.com/poteto/noodle) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
