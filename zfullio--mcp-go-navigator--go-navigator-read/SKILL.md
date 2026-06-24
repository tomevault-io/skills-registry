---
name: go-navigator-read
description: Use Go Navigator MCP to explore Go code semantically (packages, symbols, definitions/references, context, metrics, deps) and return file/line/snippets; do not modify code. Use when this capability is needed.
metadata:
  author: zfullio
---

# Go Navigator (READ) — semantic navigation only

## Scope rules
- READ-ONLY: never call `renameSymbol` or `rewriteAst` (or any mutating tool).
- Prefer semantic tools over raw text search.

## Defaults
- Use `"dir": "."` unless user specifies another root.
- For any `"package"` field, use exact import path from `listPackages`.
- Test scope defaults:
  - Use `"testScope": "exclude"` for architecture/implementation reads.
  - Use `"testScope": "include"` when explicitly analyzing tests or caller chains.
  - Use `"testScope": "only"` for test-only inspection.

## Quick decision matrix
- Need quick understanding of one symbol: `getSymbolContext`
- Need exact declaration/usage sites: `getDefinitions` + `getReferences`
- Need callers/callees around one function: `callGraph`
- Need architecture/package view: `listPackages` + `getDependencyGraph`
- Need quality hotspots: `getComplexityReport` + `getDeadCodeReport`
- Need targeted source details: `getFunctionSource` / `getStructInfo` / `getFileInfo`

## Tool notes
- `listPackages`: package discovery, usually first call.
- `getProjectSchema`: broad structural overview (`depth`: `summary|standard|deep`).
- `listSymbols`: grouped symbols by package/file.
- `listImports`: grouped imports by file.
- `listInterfaces`: grouped interfaces by package.
- `getDefinitions`: definition sites (supports limit/offset).
- `getReferences`: usage sites (supports limit/offset).
- `getSymbolContext`: fastest context bundle before deep dive.
- `callGraph`: directional function/method call graph (`callers`, `callees`, `both`) with `maxDepth` and `maxNodes`.
- `getFunctionSource`: exact function/method body by name.
- `getStructInfo`: struct declaration (+methods optionally).
- `getFileInfo`: file metadata/symbols/source with actual fields:
  - `options.withSource`
  - `options.withComments`
  - `options.includeFunctionBodies`
  - `options.functionBodyLimit`
  - `filter.symbolKinds`
  - `filter.nameContains`
  - `filter.exportedOnly`
- `getImplementations`: interface ↔ concrete implementations.
- `getDependencyGraph`: package graph, fan-in/out, cycles.
- `getComplexityReport`: LoC/nesting/cyclomatic by function.
- `getDeadCodeReport`: unused symbols.

## Recommended workflows

### Orientation
1) `listPackages { "dir": ".", "testScope": "exclude" }`
2) `getProjectSchema { "dir": ".", "depth": "standard", "testScope": "exclude" }` (optional)
3) `getDependencyGraph { "dir": ".", "testScope": "exclude" }` (optional)

### Understand a symbol
1) `getSymbolContext { "dir": ".", "ident": "<Ident>", "kind": "<optional>", "testScope": "include" }`
2) `getDefinitions { "dir": ".", "ident": "<Ident>", "testScope": "exclude" }`
3) `getReferences { "dir": ".", "ident": "<Ident>", "testScope": "include" }`
4) `callGraph { "dir": ".", "ident": "<Ident>", "direction": "both", "maxDepth": 2, "testScope": "include" }`
5) `getFunctionSource` / `getStructInfo` / `getFileInfo` as needed

### Quality pass
- `getComplexityReport { "dir": ".", "package": "<pkg>", "testScope": "exclude" }`
- `getDeadCodeReport { "dir": ".", "package": "<pkg>", "limit": 20, "testScope": "exclude" }`

## Large result sets
- Use limit/offset where available.
- Prioritize relevant files first.

## Output guidance
- Always include file paths and lines.
- Keep snippets short (1–5 lines) unless user requests full bodies.
- If user asks a quick question, return concise output (no forced heavy template).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zfullio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
