---
name: tdd-architecture-patterns
description: Designing testable software systems using established architectural patterns. Use when this capability is needed.
metadata:
  author: jcorpac
---

# TDD Architecture Patterns

Software architecture should enable testing, not hinder it. These patterns ensure your system remains decoupled and verifiable.

## Core Patterns

### 1. Hexagonal Architecture (Ports & Adapters)
- Keep your business logic (the Core) isolated from external concerns (DB, UI, APIs).
- Use **Ports** (interfaces) for the core to communicate with the outside world.
- Use **Adapters** to implement those ports for specific technologies.

### 2. Dependency Injection (DI)
- Invert the control of dependencies. Instead of a class creating its helper, it receives it via a constructor or parameter.
- **Benefit**: Allows you to easily swap real dependencies with mocks in tests.

### 3. SOLID Principles
- **S**: Single Responsibility.
- **O**: Open/Closed (Open for extension, closed for modification).
- **L**: Liskov Substitution.
- **I**: Interface Segregation.
- **D**: Dependency Inversion.

## Guidelines
- **Avoid Global State**: It makes tests non-deterministic and hard to run in parallel.
- **Keep Logic Pure**: Prefer stateless functions where possible; they are the easiest to test.
- **Test-First Design**: If a feature is hard to test, it usually means the architecture is too tightly coupled.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
