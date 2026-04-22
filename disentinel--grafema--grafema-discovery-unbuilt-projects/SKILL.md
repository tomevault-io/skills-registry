---
name: grafema-discovery-unbuilt-projects
description: | Use when this capability is needed.
metadata:
  author: disentinel
---

# Grafema Discovery for Unbuilt Projects

## Problem
Grafema's auto-discovery finds 0 source files when a project's `package.json` entry points
(`main`, `module`, `exports`) point to built output directories (`dist/`, `build/`) that
either don't exist or contain stale code. This is common in SWE-bench tasks where the
project is checked out at a specific commit without running the build step.

## Context / Trigger Conditions

- `grafema analyze` completes but reports only ~29 nodes (all plugin metadata)
- `grafema overview` shows: Modules: 0, Functions: 0, Variables: 0
- Diagnostics log shows only `grafema:plugin` type nodes as disconnected
- Source files exist in `src/`, `hooks/src/`, `compat/src/`, etc. but aren't indexed
- `package.json` has entries like `"main": "dist/preact.js"` pointing to unbuilt output
- Common in: Preact, React, Vue, Babel, and other framework codebases

## Solution

Add explicit `services` array to `.grafema/config.yaml`:

```yaml
services:
  - name: project-core
    path: "src"
    entrypoint: "src/index.js"
  - name: project-hooks
    path: "hooks/src"
    entrypoint: "hooks/src/index.js"
  - name: project-compat
    path: "compat/src"
    entrypoint: "compat/src/index.js"
```

**Key rules:**
1. Services must be an **array** (not object/dict) — `services: [{...}, {...}]`
2. Each service needs `name`, `path`, and `entrypoint`
3. `entrypoint` must be a file path (e.g., `src/index.js`), not a directory
4. `path` is the source directory for that service
5. You can combine with `include` patterns for additional filtering

**Common error:** Using object syntax produces:
```
Error: Config error: services must be an array, got object
```

## Verification

After updating config:
```bash
grafema analyze --auto-start --clear
```

Expected: Node count should be in hundreds/thousands (not 29). `grafema overview` should
show non-zero Modules, Functions, Variables.

## Example: Preact Configuration

```yaml
services:
  - name: preact-core
    path: "src"
    entrypoint: "src/index.js"
  - name: preact-hooks
    path: "hooks/src"
    entrypoint: "hooks/src/index.js"
  - name: preact-compat
    path: "compat/src"
    entrypoint: "compat/src/index.js"
  - name: preact-debug
    path: "debug/src"
    entrypoint: "debug/src/index.js"

plugins:
  discovery: []
  indexing:
    - JSModuleIndexer
  analysis:
    - JSASTAnalyzer
  enrichment:
    - MethodCallResolver
    - ArgumentParameterLinker
    - AliasTracker
    - ClosureCaptureEnricher
    - ImportExportLinker
    - PrefixEvaluator
  validation:
    - GraphConnectivityValidator
```

Result: 3799 nodes, 5190 edges, 30 modules, 186 functions (vs 29 nodes without services).

## Notes

- The `discovery: []` in plugins is normal — it means no auto-discovery plugins run,
  which is fine when using explicit services
- For SWE-bench tasks, check `package.json` entry points first. If they point to `dist/`,
  you'll need manual services config
- Each SWE-bench repo needs its own pre-computed `.grafema/` directory
- Strip unnecessary plugins (Express, Socket.IO, Database analyzers) for frontend libraries
  to speed up analysis
- The `include` pattern alone does NOT fix discovery — it only filters already-discovered
  files. You need `services` to tell Grafema WHERE to look.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/disentinel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
