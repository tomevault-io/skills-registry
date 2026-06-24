---
name: architecture
description: Standards for structural design, Clean Architecture, and project layout in Golang. Use when this capability is needed.
metadata:
  author: fierzone
---

# Golang Architecture Standards

## **Priority: P0 (CRITICAL)**

## Principles

- **Clean Architecture**: Separate concerns. Inner layers (Domain) rely on nothing. Outer layers (Adapters) rely on Inner.
- **Project Layout**: Follow standard Go project layout (`cmd`, `internal`, `pkg`).
- **Dependency Injection**: Explicitly pass dependencies via constructors. Avoid global singletons.
- **Package Oriented Design**: Organize by feature/domain, not by layer (avoid `controllers/`, `services/` at root).
- **Interface Segregation**: Define interfaces where they are _used_ (Consumer implementation).

## Standard Project Layout

- `cmd/`: Application entry points (e.g., `cmd/server/main.go`).
- `internal/`: Private application code. Not importable by other modules.
  - `internal/domain/`: Entities, Business Rules (Pure Go, no heavy imports).
  - `internal/usecase/`: Application Logic (Interactors).
  - `internal/adapter/`: Database, HTTP, GRPC adapters.
- `pkg/`: Library code ok to use by external applications.
- `configs/`: Configuration files.

## Guidelines

- **Use Constructors**: `NewService(repo Repository) *Service`.
- **Inversion of Control**: Service depends on `Repository` interface, not `SQLRepository` struct.
- **Wire up in Main**: Main function composes the dependency graph.

## References

- [Standard Project Layout](references/project-layout.md)
- [Clean Architecture Layers](references/clean-arch.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fierzone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
