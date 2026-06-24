---
name: vue-component-architecture-review
description: Use when reviewing Vue component architecture, Composition API usage, reactivity, component boundaries, router, Pinia/Vuex state management, and component design.
metadata:
  author: WangYao-GoGoGo
---

# Vue Component Architecture Review

## When To Use

- The main decision is about Vue component organization, Composition API design, state management (Pinia/Vuex), reactivity performance, or component boundaries.
- Reviewing composable design, watcher usage, or store organization.

## Workflow

1. Identify component roles — page, feature, shared, and base components.
2. Review Composition API usage — are composables extracting reusable logic or creating hidden state?
3. Map state ownership — local component state, Pinia/Vuex stores, and server state.
4. Check watcher and computed usage — are watchers hiding business workflows?
5. Review store organization — are stores scoped by domain or becoming dumping grounds?
6. Check component boundaries — do components own too much API or domain logic?
7. Recommend the smallest change that clarifies component or state ownership.

## Output Format

```markdown
Vue architecture review:
- Component roles:
- Composition API usage:
- State ownership:
- Watchers & computed:
- Store organization:
- Component boundaries:
- Recommended change:
- Verification:
```

---
> Source: [WangYao-GoGoGo/architecture-agent-skills](https://github.com/WangYao-GoGoGo/architecture-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
