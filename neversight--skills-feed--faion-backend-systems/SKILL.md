---
name: faion-backend-systems
description: Systems backends: Go, Rust, databases, caching. Use when this capability is needed.
metadata:
  author: neversight
---
> **Entry point:** `/faion-net` — invoke this skill for automatic routing to the appropriate domain.

# Backend Developer: Systems

Systems-level backend development in Go and Rust, plus database design and infrastructure patterns.

## Purpose

Handles high-performance backend services using Go and Rust, database design, caching strategies, and backend infrastructure.

## When to Use

- Go microservices and HTTP APIs
- Rust backend services
- Database design and optimization
- Caching strategies
- Message queues
- Error handling patterns

## Methodologies (22 files)

**Go (10):** go-backend, go-channels, go-concurrency-patterns, go-error-handling, go-error-handling-patterns, go-goroutines, go-http-handlers, go-project-structure, go-standard-layout

**Rust (7):** rust-backend, rust-error-handling, rust-http-handlers, rust-ownership, rust-project-structure, rust-testing, rust-tokio-async

**Database (3):** database-design, nosql-patterns, sql-optimization

**Infrastructure (4):** caching-strategy, error-handling, message-queues

## Tools

**Go:** Standard library, Gin, Echo, GORM, sqlx
**Rust:** Actix-web, Rocket, Tokio, Diesel, sqlx
**Database:** PostgreSQL, MySQL, MongoDB, Redis
**Queues:** RabbitMQ, Kafka, Redis

## Related Sub-Skills

| Sub-skill | Relationship |
|-----------|--------------|
| faion-backend-developer:enterprise | Enterprise web frameworks (Java, C#, PHP, Ruby) |
| faion-python-developer | Python backends (Django, FastAPI) |
| faion-javascript-developer | Node.js backends |
| faion-api-developer | API design patterns |

## Integration

Invoked by parent skill `faion-backend-developer` for Go/Rust/database work.

---

*faion-backend-developer:systems v1.0 | 22 methodologies*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
