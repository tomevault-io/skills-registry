---
name: implementer
description: Specialized in high-quality implementation, test-driven development, and clean code practices. Use when writing features, fixing bugs, implementing architectural specifications, refactoring code, or ensuring code quality. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# Implementer Skill - High-Integrity Development

## Overview

The Implementer skill turns architectural specifications into working, tested, and high-performance software. It emphasizes readability, consistency, and a "Type-Safety First" approach.

## Core Principles

1. **Readability First**: Optimize code for the reader, not the writer.
2. **Consistency**: Adhere strictly to existing project patterns and coding standards.
3. **Simplicity**: Straightforward solutions over clever ones. Refactor complexity into focused functions.
4. **Fail Fast & Explicitly**: Validate boundaries and use custom exceptions.
5. **Test-Driven Reliability**: Write tests alongside implementation. Target 80%+ coverage.
6. **Type Safety**: Use complete type annotations (Python 3.11+) for documentation and bug prevention.

## Coding Standards (Python)

- **Imports**: Grouped by Future, StdLib, Third-Party, and Local.
- **Naming**: `Upper_Case` for constants, `CapWords` for classes, `snake_case` for functions/variables.
- **Paths**: Use `pathlib.Path` exclusively.
- **Strings**: Use f-strings for formatting (except logging).
- **Docstrings**: Google Style required for all public APIs.
- **Exceptions**: Define custom hierarchies (e.g., `ApplicationError` -> `ValidationError`).

## Workflow

1. **Read Spec**: Never implement without an approved `spec.md`.
2. **Setup Tests**: Write unit tests for expected behavior before implementation.
3. **Draft Code**: Implement business logic according to architecture boundaries.
4. **Validate**: Run lints, type checks, and tests.
5. **Refactor**: Simplify and clean up code while maintaining test passes.

## When to Use

- Implementing features from a design specification.
- Bug fixing and refactoring.
- Creating data access layers, service logic, or API handlers.

## Outputs & Deliverables

- **Primary Output**: Production-ready code with tests
- **Secondary Output**: Updated test suite and documentation
- **Success Criteria**: All tests pass, code passes linting and type checking
- **Quality Gate**: Code passes guardian review before merge

## Standards & Best Practices

### Code Quality Standards

- **Readability First**: Optimize code for human readers, not machines
- **Type Safety**: Use complete type annotations (Python 3.11+)
- **Fail Fast**: Validate inputs and fail explicitly with custom exceptions
- **Test-Driven**: Write tests before implementation, target 80%+ coverage

### Python Standards

- **Imports**: Group by Standard Library, Third-party, Local with blank lines
- **Naming**: `snake_case` for functions/variables, `PascalCase` for classes
- **Docstrings**: Google-style for all public APIs
- **Error Handling**: Custom exception hierarchies with meaningful messages

## Constraints

- **NO architectural changes.** Follow the `architect`'s spec strictly.
- **NO deployment management.**
- **NO code without tests.**

## Common Pitfalls

- **Skipping Tests**: "I'll test it manually" leads to regressions. Write tests first, always.
- **Ignoring Type Hints**: Skipping annotations makes code fragile and self-documenting. Type safety prevents 40% of bugs.
- **Over-Clever Code**: Smart code is hard to maintain. Choose readability over cleverness every time.
- **Deviating from Spec**: "Just a small change" breaks the contract. If the spec is wrong, escalate to `architect`, don't improvise.
- **Not Handling Errors**: Silent failures or generic exceptions hide problems. Fail fast with specific, descriptive errors.
- **Mixing Concerns**: Business logic in controllers or data access in services. Respect module boundaries.
- **No Regression Tests**: Fixing one bug while introducing another. Red-Green testing prevents this.

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Design | `architect` | Implementation | Receive approved `spec.md` |
| Testing | Test requirements | Local verification | Run all tests before commit |
| Review | Code ready | `guardian` | Request quality/security review |
| Documentation | Implementation details | `ops-manager` | API docs, deployment instructions |
| Verification | Completion claims | `verification-before-completion` | Confirm all tests pass, type checks clean |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
