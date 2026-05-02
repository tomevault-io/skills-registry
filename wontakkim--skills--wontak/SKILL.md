---
name: wontak
description: Personal coding philosophy and patterns for TypeScript, Kotlin, Swift, and Python development. Use when writing, reviewing, or refactoring code in Wontak's projects. Triggers on tasks involving layered architecture design, Manager/Client patterns, testability-focused design, error handling strategies (graceful degradation vs fail-fast), naming conventions, or project structure decisions. Also use when setting up new projects, configuring build tools, or establishing coding standards. Use when this capability is needed.
metadata:
  author: wontakkim
---

# Wontak Developer Style

> **Note:** Preferences evolve over time. This guide reflects current standards.
> When working on older projects, check project-specific configurations.

---

## Core Philosophy

### Pragmatism Over Purity

Working solutions take priority over theoretical perfection. This doesn't mean cutting corners, but rather choosing practical approaches that deliver value without unnecessary complexity.

**Principles:**
- Allow escape hatches (like `any` type) when genuinely necessary rather than fighting the type system
- Don't enforce async/await on every function if it adds no value
- Allow unused parameters in callback signatures to maintain API compatibility
- Choose the simpler solution when multiple approaches are equally valid

### Safety First

Despite embracing pragmatism, safety remains non-negotiable:

- Strict type checking enabled by default in all TypeScript projects
- Mutex/lock patterns for any shared mutable state
- Fail-fast approach for required configuration (crash early, crash loudly)
- Graceful degradation for optional features (don't crash, log and continue)
- Design for testability from the start

### Clear Intent

Code should communicate its purpose without requiring extensive comments:

- Use descriptive names that reveal purpose, not implementation
- Implement explicit state machines with clear guard clauses
- Use logging as execution documentation
- Write comments only to explain WHY something is done, never WHAT

### Platform Abstraction

When building software that may run on multiple platforms:

- Core business logic lives in platform-agnostic modules
- Platform-specific code implements well-defined interfaces
- Use the Strategy pattern to swap implementations at runtime

### Testability by Design

Code should be designed for testability from the beginning. This is more valuable than any coverage metric.

- Depend on abstractions (interfaces), not concretions
- Inject dependencies through constructors
- Avoid static methods and singletons that can't be mocked
- Keep side effects at the edges of the system
- Separate pure logic from I/O operations

---

## Quick Reference

### Always Do

- Write all code, comments, and documentation in English
- Enable strict TypeScript configuration
- Design for testability from the start
- Separate platform-agnostic from platform-specific code
- Specify explicit return types on public methods
- Use singular folder names
- Follow conventional commit format
- Use mutex for shared mutable state
- Graceful degradation for optional features
- Fail-fast for required configuration
- Inject dependencies through constructors
- Use double quotes in TypeScript/JavaScript

### Never Do

- Chase coverage metrics over meaningful tests
- Skip return types on public API methods
- Use plural folder names
- Commit without conventional format
- Access platform globals in core code
- Put business logic in controllers
- Ignore lint/type errors
- Use default exports (prefer named exports)
- Create singletons that can't be mocked

### Testing Priority

| Priority | What to Test |
|----------|--------------|
| High | Core business logic, state management, error handling |
| Medium | Integration points, API boundaries, data transformations |
| Low | Simple utility functions, configuration |
| Skip | Trivial views, simple getters/setters, framework boilerplate |

### Pattern Selection Guide

| Scenario | Recommended Pattern |
|----------|---------------------|
| Complex domain coordination | Manager class |
| External API integration | Client class |
| Cross-platform code | Interface + Strategy |
| Shared mutable state | Mutex/Lock |
| Optional feature unavailable | Graceful degradation |
| Required config missing | Fail-fast |
| Multiple implementations | Strategy pattern |
| Object creation complexity | Factory pattern |
| Event broadcasting | Observer pattern |

---

## Reference Guide

Load additional references based on your current task:

### By Language

| Working with | Load |
|--------------|------|
| TypeScript/JavaScript | `references/typescript-patterns.md` |
| Kotlin/Android | `references/kotlin-patterns.md` |
| Swift/iOS | `references/swift-patterns.md` |
| Python | `references/python-patterns.md` |

### By Concern

| Need help with | Load |
|----------------|------|
| Architecture design, layers, Manager/Client patterns | `references/architecture.md` |
| File/class/function naming, code style | `references/naming-and-style.md` |
| Error handling, graceful degradation, fail-fast | `references/error-handling.md` |
| Writing tests, mocks, test structure | `references/testing.md` |
| Folder structure, monorepo layout | `references/project-structure.md` |
| Package managers, CI/CD, build tools | `references/tooling.md` |

### Common Combinations

| Task | Load these references |
|------|----------------------|
| New TypeScript feature | `typescript-patterns.md` + `architecture.md` |
| New Kotlin SDK feature | `kotlin-patterns.md` + `architecture.md` |
| New Swift/iOS SDK feature | `swift-patterns.md` + `architecture.md` |
| Setting up new project | `project-structure.md` + `tooling.md` |
| Writing tests | `testing.md` + (language-specific patterns) |
| Code review | `naming-and-style.md` + (language-specific patterns) |

---

*Personal development patterns for consistent, maintainable, and testable code.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wontakkim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
