---
name: phoenix-client-development
description: > Use when this capability is needed.
metadata:
  author: Arize-ai
---

# Phoenix Client Development

TypeScript SDK for Phoenix AI observability: datasets, experiments, prompts, sessions, spans, traces.

Read existing code in the directory you're working in before writing new code.

## Rule Files

| Rule file              | When to read                                              |
| ---------------------- | --------------------------------------------------------- |
| `rules/experiments.md` | Experiment execution, task runners, evaluator wiring      |
| `rules/tracing.md`     | OpenTelemetry tracer providers, span export, global state |
| `rules/testing.md`     | Unit tests, integration tests, test fixtures              |

## Build and Test

```bash
cd js/
pnpm --filter phoenix-client test
```

Tests use **vitest**. Test files live in `test/` named `*.test.ts`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/Arize-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
