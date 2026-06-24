---
name: go-navigator-test-coverage
description: Use Go Navigator MCP to find and assess Go tests and coverage around a symbol/package (test presence, related test files, table-driven patterns, gaps); do not modify code Use when this capability is needed.
metadata:
  author: zfullio
---

# Go Navigator — Test & Coverage (READ)

## Purpose
Answer:
- Are there tests for this symbol/package?
- Which files/functions cover it directly or indirectly?
- What risk/gaps remain?

## Scope rules
- READ-ONLY: never call `renameSymbol` or `rewriteAst`.
- Prefer semantic tools over raw text scanning.

## Defaults
- Use `"dir": "."` unless user specifies another root.
- Use exact package path from `listPackages`.
- Always include tests unless explicitly asked otherwise:
  - default `"testScope": "include"`
  - use `"testScope": "only"` for test-only reports
  - use `"testScope": "exclude"` only for implementation-only baselines
- Modes:
  - `symbol` (default)
  - `package`
  - `module` (summary only)

## Quick decision matrix
- One symbol quickly: `getSymbolContext`
- Exact usage in tests: `getReferences`
- Indirect test reachability and blast radius: `callGraph`
- Confirm ownership/definition: `getDefinitions`
- Deep inspect test file/function: `getFileInfo` / `getFunctionSource`
- Risk by complexity: `getComplexityReport`

## Core workflow

### A) Symbol-level test mapping
1) `getSymbolContext { "dir": ".", "ident": "<Ident>", "kind": "<optional>", "testScope": "include", "maxUsages": 5, "maxTestUsages": 10, "maxDependencies": 8 }`
2) If needed: `getDefinitions { "dir": ".", "ident": "<Ident>", "testScope": "include" }`
3) `getReferences { "dir": ".", "ident": "<Ident>", "testScope": "include", "limit": 200, "offset": 0 }`
4) `callGraph { "dir": ".", "ident": "<Ident>", "direction": "callers", "maxDepth": 3, "testScope": "include" }` to identify indirect test callers
5) Inspect key tests with actual `getFileInfo` schema, for example:
   `getFileInfo { "dir": ".", "file": "<file_test.go>", "options": { "withComments": true }, "filter": { "symbolKinds": ["func"], "nameContains": "Test" } }`
6) Optional for specific test function: `getFunctionSource`

### B) Package-level coverage signal
1) `listSymbols { "dir": ".", "package": "<pkg>", "testScope": "include" }`
2) For important symbols, run workflow A with tighter limits.

### C) Complexity-aware risk
- `getComplexityReport { "dir": ".", "package": "<pkg>", "testScope": "exclude" }`
- Flag: high complexity + no clear test references.

## Coverage heuristics
- Direct coverage: tests call symbol directly.
- Indirect coverage: tests call upper-level function that uses symbol.
- Prefer `callGraph(callers)` to validate indirect chains from `*_test.go` functions.
- Better confidence when tests include:
  - table-driven patterns (`[]struct`, `t.Run`)
  - error path assertions
  - edge/boundary inputs

## Output guidance
Always include:
- concise summary
- evidence (file + line)
- gap list (specific missing branches/inputs)

If user asks briefly, keep response short; do not force full template.

## Non-goals
- Does not execute `go test` or produce real coverage percentages.
- No code modification.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zfullio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
