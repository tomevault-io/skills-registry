---
name: perf-code-paths
description: Use when mapping code paths, entrypoints, and likely hot files before profiling.
metadata:
  author: composiohq
---

# perf-code-paths

Identify likely implementation paths for a performance scenario.

Follow `docs/perf-requirements.md` as the canonical contract.

## Required Steps

1. Use repo-map if available; otherwise use grep for entrypoints and handlers.
2. List top candidate files/symbols tied to the scenario.
3. Include imports/exports or call chains when relevant.

## Output Format

```
keywords: <comma-separated list>
paths:
  - file: <path>
    symbols: [<symbol1>, <symbol2>]
    evidence: <short reason>
```

## Constraints

- Focus only on supported languages (Rust, Java, JS/TS, Go, Python).
- Keep to the most relevant 10-15 files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/composiohq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
