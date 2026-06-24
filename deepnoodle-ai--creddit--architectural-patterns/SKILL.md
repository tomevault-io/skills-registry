---
name: architectural-patterns
description: Reference for software architecture patterns including Clean Architecture, Hexagonal Architecture, Modular Monolith, and Vertical Slice Architecture. Use when this capability is needed.
metadata:
  author: deepnoodle-ai
---

# Architecture Patterns Reference

## Clean Architecture

Concentric layers with dependencies pointing inward. Business rules at the core, externalities at the edges.

### Layer Breakdown

```
Outer → Inner:
  Frameworks & Drivers  (DB, web framework, UI)
  Interface Adapters     (controllers, presenters, gateways)
  Application Business   (use cases / interactors)
  Enterprise Business    (entities, domain objects)
```

### Canonical Directory Layout

```
project/
├── domain/              # Entities, value objects, domain errors
│   ├── entities/
│   └── value_objects/
├── usecases/            # Application-specific business rules
│   ├── create_order.go  # One file per use case (or grouped by aggregate)
│   └── ports/           # Interfaces that use cases depend on
│       ├── repository.go
│       └── notifier.go
├── adapters/            # Concrete implementations of ports
│   ├── postgres/
│   ├── http/
│   └── email/
└── main.go              # Composition root / wiring
```

### Key Rules
- Domain layer has ZERO imports from outer layers
- Use cases define port interfaces; adapters implement them
- Data crosses boundaries as plain structs/DTOs, never ORM models
- The composition root (main) is the only place that knows all concrete types

### When to Apply
- Complex domain logic that must survive framework changes
- Teams that need strict boundary enforcement
- Long-lived products where tech stack may evolve

### Common Mistakes
- Leaking ORM types into use cases
- "Pass-through" use cases that just delegate to a repository (indicates anemic domain)
- Creating ports for things that never change (over-abstraction)

---

## Hexagonal Architecture

The application is a hexagon. Left side: driving adapters (things that call your app). Right side: driven adapters (things your app calls). Ports define the boundary contracts.

### Structure

```
project/
├── domain/
│   ├── model/           # Aggregates, entities, value objects
│   └── services/        # Domain services (stateless logic across aggregates)
├── ports/
│   ├── inbound/         # Interfaces the outside world calls (use cases)
│   └── outbound/        # Interfaces the domain calls (repos, messaging)
├── adapters/
│   ├── inbound/         # HTTP handlers, CLI, gRPC, message consumers
│   └── outbound/        # Postgres repo, S3 client, SMTP sender
└── config/              # Wiring / DI / app bootstrap
```

### Key Rules
- Ports are interfaces owned by the domain side
- Adapters are plug-and-play: swap Postgres for DynamoDB by writing a new adapter
- Inbound adapters translate external input → domain commands
- Outbound adapters translate domain calls → infrastructure operations

### When to Apply
- Systems needing tech-swap flexibility (e.g., multi-cloud, DB migration)
- Integration-heavy services with many external dependencies
- Microservices where each service has clear inbound/outbound boundaries

---

## Modular Monolith

A single deployable with internal modules that have explicit boundaries. Each module encapsulates its own domain, data, and services. Modules communicate through public APIs, not by reaching into each other's internals.

### Structure

```
project/
├── modules/
│   ├── users/
│   │   ├── api.go           # Public module interface (exported functions/types)
│   │   ├── internal/        # Private to this module
│   │   │   ├── service.go
│   │   │   ├── repository.go
│   │   │   └── model.go
│   │   └── events.go        # Events this module publishes
│   ├── billing/
│   │   ├── api.go
│   │   ├── internal/
│   │   └── events.go
│   └── scheduling/
│       ├── api.go
│       ├── internal/
│       └── events.go
├── shared/                  # Truly shared utilities (logging, errors, config)
└── main.go                  # Wires modules together
```

### Key Rules
- Modules NEVER import another module's `internal/` package
- Cross-module communication uses the public `api.go` or events
- Each module can own its own DB schema/tables (logical separation, physical co-location)
- The shared package is minimal: only genuinely cross-cutting concerns

### When to Apply
- Growing monolith that needs structure before (or instead of) splitting into microservices
- Small teams that don't want distributed system complexity
- Products where feature boundaries are clear but deployment should remain simple

### Migration Path to Microservices
- Module boundaries become service boundaries
- Public APIs become RPC/HTTP contracts
- Events become messages on a broker

## Vertical Slice Architecture

Organize by feature/behavior, not by technical layer. Each slice contains everything needed to handle one operation end-to-end.

### Structure

```
project/
├── features/
│   ├── create_order/
│   │   ├── handler.go       # HTTP/gRPC handler
│   │   ├── command.go       # Request DTO
│   │   ├── logic.go         # Business rules for this operation
│   │   ├── repository.go    # Data access for this operation
│   │   └── tests/
│   ├── get_order/
│   │   ├── handler.go
│   │   ├── query.go
│   │   ├── logic.go
│   │   └── tests/
│   └── cancel_order/
├── shared/                  # Cross-cutting: middleware, DB connection, auth
└── main.go
```

### Key Rules
- Each slice is self-contained: handler → logic → data access
- Duplication across slices is acceptable (prefer isolation over DRY across features)
- Shared code is minimal and genuinely cross-cutting
- New features = new directories, not modifications to existing layers

### When to Apply
- CRUD-heavy applications with many independent operations
- Teams where different people own different features
- Codebases where layer-based organization led to shotgun surgery

## Feature-Sliced Design

Primarily used in frontend applications. Organizes code into layers (app, pages, features, entities, shared) with strict import rules.

### Structure

```
src/
├── app/                     # App-wide setup: providers, routing, global styles
├── pages/                   # Page-level composition (routes → features)
├── widgets/                 # Composite UI blocks used across pages
├── features/                # User-facing interactions (each self-contained)
│   ├── auth/
│   │   ├── ui/
│   │   ├── model/
│   │   └── api/
│   └── checkout/
├── entities/                # Business entities (user, product, order)
│   ├── user/
│   │   ├── ui/
│   │   ├── model/
│   │   └── api/
│   └── product/
└── shared/                  # UI kit, libs, utilities, configs
```

### Import Rules
- Layers can only import from layers below them
- `app` → `pages` → `widgets` → `features` → `entities` → `shared`
- No upward imports, no cross-imports at the same layer

### When to Apply
- Frontend-heavy SPAs (React, Vue, Angular)
- Large teams needing strict import discipline
- Products where UI features evolve independently

## Selection Guidance

| Factor                         | Clean/Hexagonal            | Modular Monolith         | Vertical Slice    | Feature-Sliced          |
| ------------------------------ | -------------------------- | ------------------------ | ----------------- | ----------------------- |
| Complex domain logic           | ✅ Best fit                 | ✅ Good                   | ⚠️ Logic scattered | ❌ Frontend-focused      |
| Many integrations              | ✅ Adapter pattern          | ⚠️ Per-module             | ⚠️ Per-slice       | ❌ Not designed for this |
| CRUD-heavy                     | ⚠️ Overhead                 | ✅ Good                   | ✅ Best fit        | ⚠️ If frontend           |
| Team wants microservice option | ⚠️ Possible                 | ✅ Best path              | ⚠️ Possible        | ❌ N/A                   |
| Frontend SPA                   | ❌ Wrong domain             | ❌ Wrong domain           | ⚠️ Possible        | ✅ Best fit              |
| Small/early project            | ❌ Over-engineered          | ⚠️ Might be too much      | ✅ Light enough    | ⚠️ If frontend           |
| Go or Rust codebase            | ✅ Natural fit (interfaces) | ✅ Natural fit (packages) | ✅ Natural fit     | ❌ JS/TS ecosystem       |

### Combining Patterns
Patterns are not mutually exclusive. Common combinations:
- **Modular Monolith + Clean Architecture** within each module
- **Vertical Slice + Hexagonal ports** for integration-heavy slices
- **Feature-Sliced Design (frontend) + Hexagonal (backend)** for full-stack apps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deepnoodle-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
