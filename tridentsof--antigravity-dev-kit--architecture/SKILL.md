---
name: architecture
description: description: System design patterns and architecture decisions Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: architecture
description: System design patterns and architecture decisions
---

# Architecture Patterns

> System design for Vue3 + ASP.NET applications.

---

## Layered Architecture

```
┌─────────────────────────────────────┐
│         Presentation (Vue3)         │
├─────────────────────────────────────┤
│          API (Controllers)          │
├─────────────────────────────────────┤
│        Business Logic (Services)    │
├─────────────────────────────────────┤
│         Data Access (Repositories)  │
├─────────────────────────────────────┤
│          Database (SQL Server)      │
└─────────────────────────────────────┘
```

---

## Frontend Architecture

```
src/
├── components/     # Reusable UI components
├── composables/    # Shared logic (useXxx)
├── views/          # Page components
├── stores/         # Pinia stores
├── api/            # API client
├── types/          # TypeScript types
└── router/         # Vue Router
```

---

## Backend Architecture

```
src/
├── Controllers/    # HTTP endpoints
├── Services/       # Business logic
├── Repositories/   # Data access
├── Models/         # Domain models
├── DTOs/           # Data transfer objects
└── Infrastructure/ # Cross-cutting concerns
```

---

## Communication Patterns

| Pattern | Use Case |
|---------|----------|
| REST API | Standard CRUD operations |
| SignalR | Real-time updates |
| Background Jobs | Async processing |
| Message Queue | Event-driven systems |

---

## Decision Matrix

| Decision | Option A | Option B |
|----------|----------|----------|
| State management | Pinia | Composables |
| API calls | Axios | Fetch |
| Forms | VeeValidate | Native |
| Testing | Vitest | Jest |

---

## Scaling Considerations

| Layer | Strategy |
|-------|----------|
| Frontend | CDN, code splitting |
| API | Horizontal scaling, load balancer |
| Database | Read replicas, caching |
| Background | Worker processes |

---

## DO / DON'T

| ✅ Do | ❌ Don't |
|-------|---------|
| Clear layer separation | Mixed concerns |
| Dependency injection | Hard dependencies |
| Interface-based design | Concrete dependencies |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
