---
name: system-architecture-clean-code
description: Architectural guidelines for TMS based on Clean Architecture and Repository Pattern. Use when this capability is needed.
metadata:
  author: smolagen
---

# System Architecture Skill

## Core Philosophy
The project follows a **pragmatic Clean Architecture** approach tailored for FastAPI patterns.
`Layer Separation`: API -> Service -> Repository -> Database.

## Layer Responsibilities

### 1. API Layer (`src/api/`, `src/bot/`)
- **Responsibility**: Handle HTTP/Telegram requests, validation (Pydantic), and response formatting.
- **Rule**: NO business logic here. Delegate to Services.
- **Dependencies**: Depends on `Services`.

### 2. Service Layer (`src/services/`)
- **Responsibility**: Business logic, orchestration, transactions (UoW).
- **Rule**: Framework agnostic (mostly). Should not know about HTTP or Telegram Context.
- **Dependencies**: Depends on `Repositories` and other `Services`.

### 3. Repository Layer (`src/database/repository.py`)
- **Responsibility**: Pure data access (CRUD).
- **Rule**: Returns ORM models or Domain objects. No business logic.
- **Dependencies**: ORM Models (`src/database/models.py`).

## New Feature Checklist
When implementing a new feature (e.g., "Billing"):
1.  **Define Model**: Add to `src/database/models.py`.
2.  **Create Repository**: Add methods to `src/database/repository.py` (or specialized repo).
3.  **Create Service**: Implement logic in `src/services/billing.py`.
4.  **Expose API**: Create router in `src/api/` or handler in `src/bot/`.

## Cross-Cutting Concerns
- **Logging**: Use `src.core.logging`.
- **Config**: Use `src.config.settings`.
- **Async**: Everything I/O bound must be `async`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/smolagen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
