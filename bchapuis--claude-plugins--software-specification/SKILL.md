---
name: software-specification
description: This skill should be used when the user asks to "write specifications", "specify an abstraction", "create contract tests", "property-based testing", "design by contract", "specification tests", or discusses how to formally specify interfaces before implementation. Provides methodology for writing executable specifications using the Basic/Advanced/Edge cases structure. Use when this capability is needed.
metadata:
  author: bchapuis
---

# Specification Methodology

## Overview

Specification-driven development formalizes abstraction contracts through executable tests before implementation. Specifications define *what* an abstraction does, not *how* it does it, enabling multiple implementations to be validated against the same contract.

## When to Use Specifications

Apply specification-driven development when:
- Designing new abstractions (interfaces, protocols, contracts)
- Creating components with multiple potential implementations
- Establishing clear behavioral contracts for APIs
- Working with abstractions identified through decomposition
- Ensuring implementations satisfy defined invariants

## Specification Structure

Organize specifications into three categories, in order of complexity:

### Basic Cases

Cover the fundamental expected behaviors - the "happy path":
- Normal inputs produce expected outputs
- Core functionality works as documented
- Standard use cases succeed

### Advanced Cases

Address more complex scenarios:
- Multiple valid inputs and their interactions
- State transitions and sequences
- Boundary conditions within valid ranges
- Performance characteristics (if contractual)

### Edge Cases

Handle exceptional and corner situations:
- Invalid inputs and error conditions
- Empty, null, or missing values
- Extreme values (max/min bounds)
- Concurrent access (if applicable)
- Resource exhaustion scenarios

## Arrange/Act/Assert Pattern

Structure each specification test using AAA:

```
Arrange: Set up preconditions and inputs
Act:     Execute the behavior under test
Assert:  Verify postconditions and outputs
```

**Principles:**
- Keep each section clearly separated
- One logical assertion per test (multiple physical asserts allowed if testing one concept)
- Arrange section should be minimal - complex setup suggests design issues
- Act section should be a single operation
- Assert section verifies the contract, not implementation details

## Contract Tests vs Implementation Tests

**Contract tests (specifications):**
- Test against the abstraction/interface
- Verify behavioral contracts
- Run against any conforming implementation
- Focus on *what*, not *how*

**Implementation tests:**
- Test specific implementation details
- Verify internal behavior
- Tied to one implementation
- Focus on *how*

Specifications are contract tests. Keep implementation details out of specifications.

## Property-Based Testing

Beyond example-based tests, consider properties that must always hold:

### Common Properties

- **Identity**: `f(identity) = identity` (e.g., adding zero, multiplying by one)
- **Idempotence**: `f(f(x)) = f(x)` (e.g., absolute value, normalization)
- **Commutativity**: `f(a, b) = f(b, a)` (e.g., addition, set union)
- **Associativity**: `f(f(a, b), c) = f(a, f(b, c))`
- **Inverse**: `f(g(x)) = x` (e.g., encode/decode, serialize/deserialize)
- **Invariants**: Conditions that must hold before and after operations

### When to Use Properties

- Mathematical operations
- Serialization/deserialization pairs
- Collection operations
- State machine transitions
- Any operation with algebraic laws

## Writing Effective Specifications

### Detect Project Context

Before writing specifications:
1. Identify the project's language and testing framework
2. Follow existing test conventions and patterns
3. Place specification files alongside or mirroring source structure
4. Use the project's assertion style and utilities

### Specification File Organization

```
tests/
├── specifications/          # or specs/, contracts/
│   ├── repository_spec.*    # Repository abstraction specs
│   ├── cache_spec.*         # Cache abstraction specs
│   └── parser_spec.*        # Parser abstraction specs
└── implementations/         # Implementation-specific tests
```

### Naming Conventions

Name specifications to describe the contract:
- `should_return_empty_when_not_found`
- `should_throw_on_invalid_input`
- `should_maintain_invariant_after_mutation`

### Dependencies

Specifications depend on the abstraction (interface/type), not any implementation:

```
Specification → Abstraction (interface)
                    ↑
              Implementation
```

This allows running the same specifications against different implementations.

## Integration with Decomposition

When working with abstractions from software decomposition:

1. **Receive abstraction definition**: Interface, responsibilities, collaborators
2. **Identify key behaviors**: What must this abstraction do?
3. **Structure specifications**: Basic → Advanced → Edge cases
4. **Define properties**: What invariants must hold?
5. **Write executable specs**: Test files depending only on the abstraction

## Workflow

To specify an abstraction:

1. **Understand the abstraction**: Read interface/type definition or description
2. **List behaviors**: Enumerate what the abstraction must do
3. **Categorize**: Assign each behavior to Basic/Advanced/Edge
4. **Identify properties**: Find invariants and algebraic laws
5. **Write specifications**: Create test file with AAA structure
6. **Verify dependencies**: Ensure specs depend on abstraction, not implementation

## Additional Resources

### Reference Files

For detailed patterns and language-specific examples:
- **`references/patterns.md`** - Common specification patterns by category
- **`references/properties.md`** - Property-based testing patterns and laws

### Example File

- **`examples/cache-spec.ts`** - TypeScript example showing the complete pattern (Basic/Advanced/Edge/Properties with AAA)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bchapuis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
