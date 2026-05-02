---
name: backend-engineer
description: - {ProjectName}.{Context}.API (Presentation layer) Use when this capability is needed.
metadata:
  author: gabrielmacus
---

# Projects
- {ProjectName}.{Context}.API (Presentation layer)
- {ProjectName}.{Context}.Modules (Domain, Application, Infrastructure)
- {ProjectName}.{Context}.Migrations (Database migrations)
- {ProjectName}.Integration (Files to communicate between microservices/bounded contexts)


# Architecture & Patterns

## 1. Pagination
- **Pattern Source**: See `GeminiReference.Backoffice.Modules.Posts.Domain.Criteria.PostPagination`.
- **Rules**:
  - Inherit from `Pagination`.
  - `override` `MaxPageAllowed` and `MaxSizeAllowed`.
  - Expose `public static long MaxPageLimit` for external use.

## 2. Validation & Localization
- **Pattern Source**: See `GeminiReference.Backoffice.API.Resources.v1.Posts.Actions.PaginatePosts.PaginatePostsValidator`.
- **Rules**:
  - Use `SharedLocalization` for generic errors.
  - Use `.When(x => x.Field.HasValue)` for `Optional<T>` fields.

## 3. Value Objects & Constants
- **Pattern Source**: See `GeminiReference.Backoffice.Modules.Posts.Domain.ValueObjects.PostTitle`.
- **Rules**:
  - Constants (e.g., `MAX_LENGTH`) MUST be defined inside the Value Object.
  - Reference them as `PostTitle.MAX_LENGTH`.

## 4. Observability & Logging
- **Pattern Source**: See `Neuraltech.SharedKernel.Infraestructure.Extensions.ObservabilityExtension`.
- **Rules**:
  - Use `UseDefaultExtensions()` -> `UseObservability()`.
  - Do NOT manually configure logging in `Program.cs`.

## 5. Authentication (Pending)
- **Zero Trust**: Microservices will validate auth tokens via `AddSharedAuthentication`.

## 6. AI Generation Rules
- **Entities**: Always create a strongly-typed ID Value Object (e.g., `PostId`).
- **Snapshots**: Strict separation between `InternalSnapshot` (Domain) and `PublicSnapshot` (Integration).
- **Code Style**: concise, modern C# (file-scoped namespaces, records, primary constructors).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gabrielmacus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
