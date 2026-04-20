---
name: go-project-conventions
description: Project conventions with module caching, linting, security checks, and tests via Make Use when this capability is needed.
metadata:
  author: rcarmo
---

# Skill: Go project conventions

## Goal
Provide a standard Go workflow with module caching, linting, security checks, and tests driven by Make.

## Make targets (recommended)
- `make deps` → `go mod download` + `go mod tidy`
- `make vet` → `go vet ./...`
- `make lint` → `fmt` + `vet` + `staticcheck` (if available)
- `make security` → `gosec ./...` (if available)
- `make test` → `go test -v ./...`
- `make check` → `make lint && make test` (standard validation pipeline)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rcarmo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
