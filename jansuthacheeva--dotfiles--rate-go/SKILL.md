---
name: rate-go
description: Rates a Go project on hexagonal architecture, single responsibility principle, and Go idioms. Provides scores from 1-10 and actionable recommendations. Use when this capability is needed.
metadata:
  author: jansuthacheeva
---

# Go Project Quality Analyzer

Analyze the Go codebase and provide a comprehensive quality assessment. Rate each dimension from 1 to 10 and provide specific recommendations for improvement.

## Analysis Dimensions

### 1. Hexagonal Architecture (Ports & Adapters)

Evaluate how well the project separates concerns:

**What to look for:**
- **Domain/Core**: Business logic isolated from infrastructure (usually `internal/domain/`, `core/`, or `pkg/`)
- **Ports**: Interfaces defined by the domain for external communication
- **Input Adapters**: HTTP handlers, CLI, gRPC servers that call into the domain
- **Output Adapters**: Database repositories, external API clients that implement domain interfaces
- **Dependency Direction**: Dependencies point inward (adapters depend on domain, not vice versa)

**Rating criteria:**
- 9-10: Clear separation, domain has no infrastructure imports, all external deps behind interfaces
- 7-8: Good separation with minor leakage, most adapters properly isolated
- 5-6: Some separation attempts but domain has infrastructure dependencies
- 3-4: Mixed concerns, business logic scattered across handlers/repositories
- 1-2: No separation, everything in one package or tightly coupled

### 2. Single Responsibility Principle (SRP)

Assess whether packages and functions have focused responsibilities:

**What to look for:**
- **Package cohesion**: Each package has one clear purpose reflected in its name
- **Function size**: Functions do one thing, typically under 50 lines
- **Interface segregation**: Small, focused interfaces (1-3 methods ideal)
- **No god packages**: Avoid `utils/`, `helpers/`, `common/` dumping grounds
- **No god structs**: Types don't accumulate unrelated methods

**Rating criteria:**
- 9-10: Every package has clear purpose, functions are focused, interfaces are minimal
- 7-8: Most packages well-scoped, occasional mixed responsibilities
- 5-6: Some good structure but several packages with mixed concerns
- 3-4: Many packages doing multiple things, large functions common
- 1-2: No clear organization, everything mixed together

### 3. Go Idioms & Best Practices

Evaluate adherence to idiomatic Go patterns:

**What to look for:**
- **Error handling**: Explicit error returns, wrapped with context, no panic for normal errors
- **Interfaces**: Small, behavior-focused (like `io.Reader`), defined by consumer not implementer
- **Naming**: MixedCaps, short names for local scope, descriptive for exported
- **Concurrency**: Proper use of goroutines, channels, context for cancellation
- **Resource management**: `defer` for cleanup, proper `Close()` calls
- **Standard library**: Prefer stdlib over external deps when reasonable
- **Package organization**: `internal/` for private packages, clear public API
- **Comments**: Package comments, exported function docs, no obvious comments

**Rating criteria:**
- 9-10: Exemplary Go code, could be used as teaching material
- 7-8: Solid idiomatic Go with minor style inconsistencies
- 5-6: Generally acceptable but some anti-patterns present
- 3-4: Multiple anti-patterns, code feels like it was ported from another language
- 1-2: Not idiomatic Go, major violations throughout

## Execution Steps

1. **Map the structure**: Use Glob to understand package layout and find go.mod
2. **Identify layers**: Locate domain/core packages vs infrastructure/adapters
3. **Check dependencies**: Look at imports to verify dependency direction
4. **Sample key files**: Read representative files from each layer
5. **Assess patterns**: Look for interface definitions, error handling, naming

## Output Format

Provide your analysis in this exact format:

```
## Go Project Quality Rating

### Hexagonal Architecture: X/10

**Current State:**
[2-3 sentences describing the architectural approach]

**Strengths:**
- [specific example with file reference]
- [specific example with file reference]

**Issues:**
- [specific issue with file reference]
- [specific issue with file reference]

**Recommendations:**
1. [actionable recommendation]
2. [actionable recommendation]

---

### Single Responsibility Principle: X/10

**Current State:**
[2-3 sentences on package/function organization]

**Strengths:**
- [specific example with file reference]

**Issues:**
- [specific package or function that violates SRP]

**Recommendations:**
1. [specific refactoring suggestion]
2. [specific refactoring suggestion]

---

### Go Idioms: X/10

**Current State:**
[2-3 sentences on idiomatic Go usage]

**Strengths:**
- [specific pattern used well]

**Issues:**
- [specific anti-pattern with example]

**Recommendations:**
1. [specific improvement]
2. [specific improvement]

---

## Overall Score: X/10

[1 paragraph summary highlighting the most important findings]

## Priority Improvements

1. **[Highest impact]**: [specific action]
2. **[Medium impact]**: [specific action]
3. **[Lower priority]**: [specific action]
```

Be specific with file paths and line numbers when referencing code. Focus on actionable recommendations that would have the highest impact.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jansuthacheeva) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
