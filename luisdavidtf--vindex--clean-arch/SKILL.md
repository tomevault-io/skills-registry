---
name: clean-arch
description: > Use when this capability is needed.
metadata:
  author: luisdavidtf
---

## Core Principles

1. **Dependency Rule**: Source code dependencies always point inwards.
   - **Data/Presentation -> Domain**
   - **Domain -> NEVER DEPENDS ON ANYTHING**
2. **Separation of Concerns**: Business logic is isolated from UI and DB details.

## Layers

### 1. Domain (The Core)
- **Entities**: Pure TypeScript classes or interfaces representing business objects.
- **Use Cases**: Application-specific business rules.
- **Repository Interfaces**: Abstract definitions of how to access data.

### 2. Data (The Gateway)
- **Implementations**: Concrete implementations of Repository Interfaces.
- **Data Sources**: APIs, Databases, Local Storage.
- **Mappers**: Transform data models (DB schema) into Domain Entities.

### 3. Presentation (The UI)
- **ViewModels/Hooks**: Connect UI to Use Cases.
- **Views**: React Components that render state.

## CRITICAL RULES

### 1. No Frameworks in Domain
- **NEVER** import from `react`, `react-native`, or `drizzle-orm` in the `src/domain` folder.
- **ALWAYS** keep the Domain pure.

### 2. Dependency Injection
- **ALWAYS** inject dependencies (like repositories) into Use Cases via constructor or factory function.

### 3. Repository Pattern
- **ALWAYS** define the interface in Domain (`domain/repositories/IUserRepository.ts`).
- **ALWAYS** implement it in Data (`data/repositories/UserRepositoryImpl.ts`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luisdavidtf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
