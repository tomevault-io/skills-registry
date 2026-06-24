---
name: ragsharp-build-code-graph
description: | Use when this capability is needed.
metadata:
  author: managedcode
---
## Steps
1. Run `ragsharp graph doctor --root .`.
2. Run `ragsharp graph index --root . --db .ragsharp/graph/index.db --state .ragsharp/graph/state.json`.
3. For incremental updates, run `ragsharp graph update --root . --db .ragsharp/graph/index.db --state .ragsharp/graph/state.json`.

## Expected Results
- `.ragsharp/graph/index.db` exists.
- `.ragsharp/graph/state.json` is updated.
- Output goes to stderr, JSON is emitted only for query commands.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/managedcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
