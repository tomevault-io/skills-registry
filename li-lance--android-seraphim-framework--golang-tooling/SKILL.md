---
name: golang-tooling
description: Go developer toolchain — gopls LSP diagnostics, linting, formatting, and vet. Use when setting up Go tooling, running linters, or integrating gopls with Claude Code. (triggers: gopls, golangci-lint, golangci.yml, go vet, goimports, staticcheck, go tooling, go lint) Use when this capability is needed.
metadata:
  author: li-lance
---

# Golang Tooling Standards

## **Priority: P1 (Operational)**

## Verification Workflow (Mandatory)

After writing or modifying Go code, run in order:

1. **`mcp__ide__getDiagnostics`** — gopls real-time errors and type warnings (requires gopls-lsp
   plugin)
2. **`go vet ./...`** — catch printf mismatches, unreachable code, shadowed variables
3. **`goimports -w .`** — organize imports and format in one pass
4. **`golangci-lint run ./...`** — run full linter suite (if `.golangci.yml` present)

## Tool Overview

| Tool            | Purpose                                          | When to Use              |
|-----------------|--------------------------------------------------|--------------------------|
| `gopls`         | LSP: diagnostics, completion, hover              | Always (IDE integration) |
| `go vet`        | Static analysis — correctness bugs               | After every edit         |
| `goimports`     | Import sorting + `gofmt`                         | Before commit            |
| `golangci-lint` | Aggregated linters (errcheck, staticcheck, etc.) | CI / pre-commit          |
| `staticcheck`   | Advanced static analysis                         | Large codebases          |

## golangci-lint Setup

Configure via `.golangci.yml` at repo root. Recommended linters:

- `errcheck` — enforce error handling
- `staticcheck` — bug detection beyond go vet
- `govet` — shadow, composites
- `revive` — style enforcement
- `gosec` — security issues

See [golangci.yml example](references/golangci.md).

## gopls Integration

`gopls` powers `mcp__ide__getDiagnostics`. Install:

```bash
go install golang.org/x/tools/gopls@latest
```

## Anti-Patterns

- **No `gofmt` alone**: Use `goimports` — it does formatting AND imports.
- **No manual import sorting**: Let `goimports` manage the order.
- **No skipping go vet**: Run it — catches real bugs `gofmt` misses.
- **No broad lint disable**: Fix the root cause instead of `//nolint` comments.

## References

- [golangci.yml example](references/golangci.md)

---
> Source: [li-lance/android-seraphim-framework](https://github.com/li-lance/android-seraphim-framework) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
