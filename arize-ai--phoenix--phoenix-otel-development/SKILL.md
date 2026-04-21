---
name: phoenix-otel-development
description: > Use when this capability is needed.
metadata:
  author: Arize-ai
---

# Phoenix OTel Development

OpenTelemetry registration and provider lifecycle layer for Phoenix. Single source of truth for OTel configuration — other Phoenix packages delegate here rather than importing OTel packages directly.

Read existing code in the directory you're working in before writing new code.

## Rule Files

| Rule file                            | When to read                                                          |
| ------------------------------------ | --------------------------------------------------------------------- |
| `rules/global-provider-lifecycle.md` | Global provider attachment, detachment, snapshot/restore, mount stack |
| `rules/register-api.md`              | `register()`, span processor setup, public API surface                |
| `rules/testing.md`                   | Tests for provider lifecycle, registration, span export               |

## Build and Test

```bash
cd js/
pnpm --filter phoenix-otel test
```

Dual-module output (CJS + ESM). Tests use **vitest**.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/Arize-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
