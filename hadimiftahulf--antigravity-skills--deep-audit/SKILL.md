---
name: deep-audit
description: Universal architectural auditor for any tech stack. Enforces Separation of Concerns, Clean Architecture, and best practices by identifying the project structure and detecting layer violations. Use when this capability is needed.
metadata:
  author: hadimiftahulf
---

# Deep Audit Workflow

This skill performs a deep code analysis to identify architectural violations, code smells, and security risks. It is designed to be **Universal**, adapting its checks to the detected technology stack (Laravel, Node, Go, Python, Flutter, etc.).

## Phase 1: Context & Stack Detection

1.  **Identify Stack**:
    - Check root files: `composer.json` (PHP), `package.json` (JS/TS), `go.mod` (Go), `pom.xml` (Java), `pubspec.yaml` (Dart), `requirements.txt` (Python).
2.  **Map Architecture**:
    - **Monolith/MVC**: `Controllers`, `Models`, `Views`.
    - **Frontend/SPA**: `Components`, `Hooks`, `Store`, `Services`.
    - **Clean Architecture**: `Domain`, `Application`, `Infrastructure`, `Presentation`.
    - **Microservices**: `Handlers`, `Services`, `Repositories`.

---

## Phase 2: Universal Layer Invariants

Regardless of the stack, enforce these **Universal Rules**:

### 1. The UI / Presentation Layer

_Files: `_.blade.php`, `_.tsx`, `_.vue`, `_.dart`(Widgets),`Controllers`_

- **MUST**: Handle user input, display data, state routing.
- **MUST NOT**:
  - Execute raw SQL queries.
  - Contain complex business rules (calculations, workflows).
  - Access 3rd-party APIs directly (should use Service/Gateway).
  - Contain hardcoded secrets or sensitive configurations.

### 2. The Business Logic / Domain Layer

_Files: `Services`, `UseCases`, `Domains`, `Hooks` (Logic), `Context`_

- **MUST**: Contain the core business rules, validation, and data transformation.
- **MUST NOT**:
  - Return HTML/JSX/Widget trees (UI agnostic).
  - Depend on "Framework internals" tightly (if using Clean Arch).
  - Import from the UI layer (Circular Dependency).

### 3. The Data / Infrastructure Layer

_Files: `Repositories`, `Models` (Active Record), `Database`, `API Clients`_

- **MUST**: Handle data persistence and external communication.
- **MUST NOT**:
  - Leak implementation details to the UI (e.g., exposing raw database cursors).

---

## Phase 3: Stack-Specific Presets

### A. Laravel / Filament (Legacy Support)

- **Service Layer**: Prohibit `Notification::make`, `Redirect::to` inside strict Services.
- **Filament Resources**: Ensure `form()` and `table()` schema definitions do not contain heavy logic (delegate to Actions/Services).
- **Models**: Prevent "Fat Models" (> 500 lines); suggest extracting Scopes or Traits.

### B. JavaScript / TypeScript (React, Vue, Node)

- **Components**: Detect potentially heavy renders or direct API calls in specific components where separation is expected.
- **Hooks**: Ensure custom hooks focus on logic/state, not returning excessive JSX.
- **Node API**: Verify `Controller` -> `Service` -> `DAL` flow. Check for "God Controllers" containing all logic.

### C. Mobile (Flutter/Native)

- **Widgets**: Detect business logic in `build()` methods.
- **State Management**: Verify usage of Bloc/Provider/Riverpod to separate state from UI.
- **Network**: Ensure no `http` calls directly in Widgets.

### D. Golang / Backend

- **Packages**: Check `cmd` (entry), `internal` (private), `pkg` (public) structure.
- **Structs**: Ensure separation between DTOs (API) and Domain Models (Logic) and DAO (DB) if applicable.

---

## Phase 4: Execution & Reporting

1.  **Scan**: Use `grep`, `find`, or `ast-parser` tools to read target files.
2.  **Analyze**: Compare against the Invariants and Presets.
3.  **Report Findings**:
    - **CRITICAL**: Security leaks (Keys in code), SQL Injection risk, Circular Dependencies.
    - **WARNING**: Layer violations (Logic in UI), God Classes (>1000 lines).
    - **INFO**: Suggestion for better pattern (e.g., "Consider extracting this logic to a Hook").
4.  **Refactor Plan**: Propose specific moves (e.g., "Move logic from `ProductController` to `PricingService`").

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
