---
name: software-architecture
description: Guide for quality focused software architecture. Use when designing software systems, reviewing architecture, analyzing code structure, planning refactors, or mentoring on architectural patterns. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# Software Architecture Development Skill

This skill provides guidance for quality focused software development and architecture. It is based on Clean Architecture and Domain Driven Design principles.

## Code Style Rules

### General Principles

- **Early return pattern**: Always use early returns when possible, over nested conditions for better readability
- Avoid code duplication through creation of reusable functions and modules
- Decompose long (more than 80 lines of code) components and functions into multiple smaller components and functions. If they cannot be used anywhere else, keep it in the same file. But if file longer than 200 lines of code, it should be split into multiple files.
- Use arrow functions instead of function declarations when possible

### Best Practices

#### Library-First Approach

- **ALWAYS search for existing solutions before writing custom code**
  - Check npm for existing libraries that solve the problem
  - Evaluate existing services/SaaS solutions
  - Consider third-party APIs for common functionality
- Use libraries instead of writing your own utils or helpers. For example, use `cockatiel` instead of writing your own retry logic.
- **When custom code IS justified:**
  - Specific business logic unique to the domain
  - Performance-critical paths with special requirements
  - When external dependencies would be overkill
  - Security-sensitive code requiring full control
  - When existing solutions don't meet requirements after thorough evaluation

#### Architecture and Design

- **Clean Architecture & DDD Principles:**
  - Follow domain-driven design and ubiquitous language
  - Separate domain entities from infrastructure concerns
  - Keep business logic independent of frameworks
  - Define use cases clearly and keep them isolated
- **Naming Conventions:**
  - **AVOID** generic names: `utils`, `helpers`, `common`, `shared`
  - **USE** domain-specific names: `OrderCalculator`, `UserAuthenticator`, `InvoiceGenerator`
  - Follow bounded context naming patterns
  - Each module should have a single, clear purpose
- **Separation of Concerns:**
  - Do NOT mix business logic with UI components
  - Keep database queries out of controllers
  - Maintain clear boundaries between contexts
  - Ensure proper separation of responsibilities

#### Anti-Patterns to Avoid

- **NIH (Not Invented Here) Syndrome:**
  - Don't build custom auth when Auth0/Supabase exists
  - Don't write custom state management instead of using Redux/Zustand
  - Don't create custom form validation instead of using established libraries
- **Poor Architectural Choices:**
  - Mixing business logic with UI components
  - Database queries directly in controllers
  - Lack of clear separation of concerns
- **Generic Naming Anti-Patterns:**
  - `utils.js` with 50 unrelated functions
  - `helpers/misc.js` as a dumping ground
  - `common/shared.js` with unclear purpose
- Remember: Every line of custom code is a liability that needs maintenance, testing, and documentation

#### Code Quality

- Proper error handling with typed catch blocks
- Break down complex logic into smaller, reusable functions
- Avoid deep nesting (max 3 levels)
- Keep functions focused and under 50 lines when possible
- Keep files focused and under 200 lines of code when possible

## Outputs & Deliverables

- **Primary Output**: Architecture guidance documents, decision records, and recommended module boundaries
- **Secondary Output**: Sample directory layouts and interface contract templates
- **Success Criteria**: Clear, implementable architecture with trade-offs documented and no ambiguous module responsibilities
- **Quality Gate**: Review by `architect` and `implementer` prior to implementation

## Constraints

- **Technical Constraints:** Provide design guidance only; do not implement or refactor existing code directly
- **Scope Constraints:** Focus on architecture and high-level design; low-level implementation details belong to `implementer`
- **Governance Constraints:** Major changes require `orchestrator` sign-off when they touch cross-cutting concerns

## Common Pitfalls

- **Mixing Layers**: Business logic in API handlers, database queries in controllers. Respect Clean Architecture boundaries.
- **Generic Naming**: `utils.js`, `helpers`, `common` are dumping grounds. Use domain-specific names like `OrderCalculator`, `UserAuthenticator`.
- **Missing Abstraction Levels**: Low-level technical details in high-level domains. Create proper abstractions between layers.
- **Ignoring the Implicit Contract**: Changing a module's interface breaks callers. Always version APIs; communicate breaking changes.
- **Over-Optimizing**: Choosing performance-first architecture before measuring. Start simple; optimize when data shows bottlenecks.
- **Too Much Coupling**: Direct imports between distant modules create a tangled dependency graph. Use dependency injection.

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Design Input | Requirements, constraints | Architecture guidance | Recommend module boundaries and patterns |
| Decision Recording | Architecture decisions | `state-manager` | Log decisions in `decisions.md` for future reference |
| Handoff | Architecture defined | `implementer` | Provide clear module contracts and responsibilities |
| Evolution | Changing requirements | Architecture review | Update design when new constraints emerge |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
