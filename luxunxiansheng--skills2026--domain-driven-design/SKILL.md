---
name: domain-driven-design
description: Use when modeling domain concepts, aggregates, and bounded contexts or checking DDD alignment in existing code.
metadata:
  author: luxunxiansheng
---

# Domain-Driven Design (DDD) Professional Standard

This skill is based on the **Domain-Driven Design Crew** and **industry-standard** architectural patterns. Use this to ensure the codebase remains a faithful representation of the business domain.

## 1. Strategic Design (The Big Picture)

### Bounded Contexts
- Define clear logical boundaries where a specific model applies.
- **Rule**: One context, one language, one team.
- **Pattern**: Communicate across contexts via **Application Services** or **Domain Events**. Use an **Anti-Corruption Layer (ACL)** when integrating with legacy or external systems.

### Ubiquitous Language
- Use the **exact** terminology of domain experts in the code.
- **Rule**: If a domain expert calls it a `ResearchBlueprint`, don't call it `ProjectDraft`.

---

## 2. Tactical Design (The Building Blocks)

### Aggregates
- A cluster of domain objects that can be treated as a single unit.
- **Aggregate Root**: The only entry point for modifications (e.g., `Order` is the root for `OrderItem`).
- **Rule**: Transactions should only modify **one aggregate** at a time. Refer to other aggregates by **ID only**.

### Entities vs. Value Objects
- **Entity**: Has a unique identifier (`id`). Its identity persists even if attributes change (e.g., `User`).
- **Value Object**: No identity. Defined only by its attributes. Immutable. (e.g., `Money`, `Address`).
- **Rule**: Prefer Value Objects for descriptors to reduce complexity.

### Domain Services
- Logic that doesn't belong to a single entity (e.g., `InnovationDebateService`).
- **Rule**: Keep them stateless and purely focused on domain logic.

---

## 3. Layered Architecture (Isolation)

### Interface Layer (`interfaces/`)
- Handles HTTP/CLI input. Validates request shapes.
- **Constraint**: No business logic. Call only the Application Layer.

### Application Layer (`application/`)
- Orchestrates use cases. Loads aggregates via repositories.
- **Constraint**: No business logic. No database specifics.

### Domain Layer (`domain/`)
- Pure business logic. Entities, Value Objects, and Domain Service interfaces.
- **Constraint**: **Zero dependencies** on technical frameworks (FastAPI, SQLAlchemy).

### Infrastructure Layer (`infrastructure/`)
- Technical implementations (Repositories, AI Agents, DB Sessions).
- **Constraint**: Implements interfaces defined by the Domain/Application layers.

---

## 4. Operational Workflow

1.  **Clarify**: Before coding, identify the **Bounded Context**.
2.  **Model**: Define **Entities** and **Value Objects** in the Domain layer first.
3.  **Encapsulate**: Protect invariants within **Aggregates**.
4.  **Abstract**: Define **Repository** interfaces in the Domain; implement them in Infrastructure.
5.  **Orchestrate**: Create an **Application Service** (Use Case) to wire them together.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luxunxiansheng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
