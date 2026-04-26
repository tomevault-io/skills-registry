---
name: rust-skills
description: Rust backend patterns, best practices, and implementation guides Use when this capability is needed.
metadata:
  author: fujigo-software
---

# Rust Backend Skills

Memory-safe, concurrent language for reliable backend services.

## Sub-Skills

### Architecture
- [project-structure.md](architecture/project-structure.md) - Project layout
- [error-handling.md](architecture/error-handling.md) - Error design
- [module-system.md](architecture/module-system.md) - Module patterns

### Database
- [diesel-patterns.md](database/diesel-patterns.md) - Diesel ORM
- [sea-orm.md](database/sea-orm.md) - SeaORM patterns
- [sqlx-patterns.md](database/sqlx-patterns.md) - sqlx async
- [migrations.md](database/migrations.md) - Migration tools

### Security
- [jwt-auth.md](security/jwt-auth.md) - JWT authentication
- [middleware.md](security/middleware.md) - Auth middleware
- [input-validation.md](security/input-validation.md) - Validation

### API
- [actix-web.md](api/actix-web.md) - Actix Web patterns
- [axum.md](api/axum.md) - Axum patterns
- [rocket.md](api/rocket.md) - Rocket patterns

### Error Handling
- [result-patterns.md](error-handling/result-patterns.md) - Result/Option
- [thiserror.md](error-handling/thiserror.md) - thiserror crate
- [anyhow.md](error-handling/anyhow.md) - anyhow patterns

### Testing
- [unit-testing.md](testing/unit-testing.md) - Unit tests
- [integration.md](testing/integration.md) - Integration tests
- [mocking.md](testing/mocking.md) - Mock patterns

### Performance
- [async-patterns.md](performance/async-patterns.md) - Async/await
- [memory.md](performance/memory.md) - Memory optimization

## Detection
Auto-detected when project contains:
- `Cargo.toml` file
- `main.rs` file
- `use actix_web` or `use axum` imports

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fujigo-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
