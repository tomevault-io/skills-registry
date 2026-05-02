---
name: spec-driven-dev
description: Feature planning with 4 phases - Specify requirements, Design architecture, break into granular Tasks, Implement and Validate. Creates atomic tasks that agents can implement without errors. Triggers on "plan feature", "design", "new feature", "implement feature", "create spec". Use when this capability is needed.
metadata:
  author: willsantos
---

# Spec-Driven Development

Plan and implement features with precision. Granular tasks. Clear dependencies. Right tools.

```
┌──────────┐   ┌──────────┐   ┌─────────┐   ┌───────────────────┐
│ SPECIFY  │ → │  DESIGN  │ → │  TASKS  │ → │ IMPLEMENT+VALIDATE│
└──────────┘   └──────────┘   └─────────┘   └───────────────────┘
```

## Phase Selection

| User wants to... | Load reference |
|------------------|----------------|
| Define what to build | [specify.md](references/specify.md) |
| Design architecture | [design.md](references/design.md) |
| Break into tasks | [tasks.md](references/tasks.md) |
| Implement a task | [implement.md](references/implement.md) |
| Verify it works | [validate.md](references/validate.md) |

## Commands

| Command | Action |
|---------|--------|
| `specify [feature]` | Define requirements |
| `design [feature]` | Design architecture |
| `tasks [feature]` | Create task breakdown |
| `implement T1` | Implement task |
| `validate` | Verify implementation |

## Output

```
.specs/[feature-slug]/
├── spec.md
├── design.md
└── tasks.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willsantos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
