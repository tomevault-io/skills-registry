---
name: go
description: > Use when this capability is needed.
metadata:
  author: grafana
---

# Go Programming Language

Guidelines for working effectively with Go projects.

## Reading Dependency Source Files

To see source files from a dependency, or to answer questions about a dependency:

```bash
go mod download -json MODULE
```

Use the returned Dir path to read the source files.

## Reading Documentation

Use go doc to read documentation for packages, types, functions, etc:

```bash
go doc foo.Bar       # Documentation for a specific symbol
go doc -all foo      # All documentation for a package
```

## Running Programs

Use go run instead of go build to avoid leaving behind build artifacts:

```bash
go run .             # Run the current package
go run ./cmd/foo     # Run a specific command
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grafana) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
