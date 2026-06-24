---
name: clean-architecture-playbook
description: Summarize and apply Clean Architecture best practices and layer conventions. Use when users ask for Clean Architecture guidance, CQRS command/query patterns, folder and naming conventions, or architecture reviews. Keep responses language-agnostic unless a specific language is requested. Use when this capability is needed.
metadata:
  author: thundermiracle
---

# Clean Architecture Playbook

## Overview

Provide concise, executable guidance for layering, CQRS, naming conventions, validation, and testing strategy. Load language-specific references only when needed.

## Workflow

1. Identify the intent: summary, implementation support, or review.
2. If the request targets an endpoint, confirm entity and command/query type.
3. If Rust is requested, read `references/rust.md`.
4. If TypeScript + NestJS is requested, read `references/typescript-nestjs.md`.
5. Build the response using the structure below.
6. Keep phrasing language-agnostic unless the user asks for a specific stack.
7. Calibrate recommendations by project scale: for large long-lived systems prefer full layered Clean Architecture; for small teams/MVPs allow vertical slices with strict boundary rules.

## Response Structure

- `Summary` (1-3 sentences)
- `Layering Rules`
- `Boundaries & DTOs`
- `Commands vs Queries`
- `Folder & Naming Conventions`
- `Validation & Errors`
- `Testing & Checks`
- `Language-Specific Notes` (only when relevant)

## Mandatory Project Rules

- Keep the Domain layer pure. Do not define DB-access interfaces in Domain. Define DB-access interfaces in Application.
- Require 100% unit-test coverage for Domain logic, except simple data-holder entities that only provide construction and trivial accessors.
- Use a simplified CQRS pattern:
- Query: return Application DTOs without traversing Domain.
- Command: always create Domain entities and pass through Domain business logic before persistence.
- Application reads data from repositories and passes it into Domain for business logic execution.
- If duplicated logic appears in Application, extract it into an Application Service.

## Advanced Guidance (from reviewed reference repos)

- Keep use cases as the primary organizing unit and keep delivery/UI concerns out of use-case logic.
- Permit read-side optimization in CQRS (e.g., query services/raw SQL/views) when it improves performance and keeps boundaries explicit.
- Apply cross-cutting policies at the use-case boundary (validation, logging, authorization, performance timing, exception mapping), not inside domain entities.
- Use domain events to decouple side effects from entity methods; add an outbox strategy when reliable integration delivery is required.
- For API adapters, prefer explicit request/response models and presenter-style mapping for consistent output contracts.

## When to Add More Detail

- For implementation/extension requests, provide a short command/query checklist.
- For data-model requests, summarize normalized schema intent and map it to likely aggregates.
- For architecture reviews, map findings by layer and evaluate them against dependency direction rules.

## Resources

- `references/rust.md`: Rust-specific structure, conventions, and endpoint patterns.
- `references/typescript-nestjs.md`: TypeScript + NestJS structure, CQRS wiring, and endpoint patterns.

## External Benchmarks (reviewed)

- Article: `https://readmedium.com/5-github-repos-that-teach-you-how-to-build-clean-architecture-in-net-c8d330256966`
- `https://github.com/jasontaylordev/CleanArchitecture`
- `https://github.com/m-jovanovic/event-reminder`
- `https://github.com/kgrzybek/sample-dotnet-core-cqrs-api`
- `https://github.com/ivanpaulovich/clean-architecture-manga`
- `https://github.com/ardalis/CleanArchitecture`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thundermiracle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
