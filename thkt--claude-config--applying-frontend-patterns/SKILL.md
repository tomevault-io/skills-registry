---
name: applying-frontend-patterns
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# Frontend Patterns

## Core Patterns

| Pattern                  | When to Use             |
| ------------------------ | ----------------------- |
| Container/Presentational | Data fetching + display |
| Custom Hooks             | Shared behavior         |
| Composition              | Flexible components     |
| State Management         | Local → Shared → Global |

## Container/Presentational

| Container (Logic) | Presentational (UI)     |
| ----------------- | ----------------------- |
| Fetches data      | Receives data via props |
| Manages state     | Stateless (ideally)     |
| Handles events    | Calls callback props    |
| No styling        | All styling lives here  |

## State Management

| Scope  | Tool          | Example            |
| ------ | ------------- | ------------------ |
| Local  | useState      | Form input, toggle |
| Shared | Context       | Theme, auth status |
| Global | Zustand/Redux | App-wide cache     |

## When NOT to Use

Simple one-off components, prototypes (YAGNI), no reuse expected.

## References

| Topic                  | File                                                         |
| ---------------------- | ------------------------------------------------------------ |
| Container/Presentation | `${CLAUDE_SKILL_DIR}/references/container-presentational.md` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
