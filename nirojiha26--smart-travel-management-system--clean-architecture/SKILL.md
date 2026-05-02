---
name: clean-architecture
description: Enforces Clean Architecture principles for the Smart Travel Management System. Use when adding new features or refactoring backend code. Use when this capability is needed.
metadata:
  author: nirojiha26
---

# Clean Architecture Guidelines

When working on the backend of the Smart Travel Management System, you MUST adhere to the following Clean Architecture principles.

## Structure

The solution consists of four main layers.

### 1. Domain Layer (`SmartTravel.Domain`)
*   **Purpose**: Inner-most layer. Contains enterprise logic and entities.
*   **Contents**: Entities, Enums, Value Objects, Domain Exceptions.
*   **Dependencies**: NONE. This project must not reference any other project.
*   **Rules**:
    *   Do not include data annotations that depend on EF Core.
    *   Keep logic pure C#.

### 2. Application Layer (`SmartTravel.Application`)
*   **Purpose**: Orchestrates application logic.
*   **Contents**: DTOs, Interfaces (Service contracts, Repository contracts), Validators, Mappers.
*   **Dependencies**: references `SmartTravel.Domain`.
*   **Rules**:
    *   Define interfaces here (e.g., `IUserService`), implement them in Infrastructure.
    *   Use DTOs for data transfer, never expose Entities directly to the API.

### 3. Infrastructure Layer (`SmartTravel.Infrastructure`)
*   **Purpose**: Implements interfaces defined in Application.
*   **Contents**: Database Context (EF Core), Repositories, Services (e.g., EmailService), External API Integrations.
*   **Dependencies**: references `SmartTravel.Application` and `SmartTravel.Domain`.
*   **Rules**:
    *   All external concerns (SQL, File System, 3rd party APIs) go here.

### 4. API Layer (`SmartTravel.API`)
*   **Purpose**: The entry point.
*   **Contents**: Controllers, Middleware, Program.cs (DI setup).
*   **Dependencies**: references `SmartTravel.Application` and `SmartTravel.Infrastructure`.
*   **Rules**:
    *   Controllers should be thin. Delegate logic to Services/mediators.
    *   Return standard HTTP responses.

## Workflow for New Features

1.   **Domain**: Define the Entity (if new).
2.  **Application**: Define the DTOs and the Interface (e.g., `IProductService`).
3.  **Infrastructure**: Implement the Interface (e.g., `ProductService`).
4.  **API**: Create the Controller and register services in `Program.cs`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nirojiha26) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
