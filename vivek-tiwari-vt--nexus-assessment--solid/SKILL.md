---
name: solid
description: Transforms code into senior-engineer quality software through SOLID principles, TDD, clean code, and professional design. Use when writing any code, refactoring, planning architecture, designing systems, reviewing code, debugging, creating tests, or making design decisions. Primarily TypeScript/NestJS; applicable to any OOP codebase. Use when this capability is needed.
metadata:
  author: vivek-tiwari-vt
---

# Solid Skills: Professional Software Engineering

Operate as a senior software engineer. Every line of code, design decision, and refactoring must embody professional craftsmanship.

## When This Skill Applies

**Use this skill when:**
- Writing ANY code (features, fixes, utilities)
- Refactoring existing code
- Planning or designing architecture
- Reviewing code quality
- Debugging issues
- Creating tests
- Making design decisions

## Core Philosophy

> "Code is to create products for users & customers. Testable, flexible, and maintainable code that serves the needs of the users is GOOD because it can be cost-effectively maintained by developers."

Goal: Enable developers to **discover, understand, add, change, remove, test, debug, deploy**, and **monitor** features efficiently.

## The Non-Negotiable Process

### 1. ALWAYS Start with Tests (TDD)

**Red-Green-Refactor:**

```
1. RED - Write a failing test that describes the behavior
2. GREEN - Write the SIMPLEST code to make it pass
3. REFACTOR - Clean up, remove duplication (Rule of Three)
```

**Three Laws of TDD:**
1. No production code unless it makes a failing test pass
2. No more test code than sufficient to fail
3. No more production code than sufficient to pass

Design happens during **REFACTORING**, not during coding.

See: [references/tdd.md](references/tdd.md)

### 2. Apply SOLID Rigorously

| Principle | Question to Ask |
|-----------|-----------------|
| **S**RP | "Does this have ONE reason to change?" |
| **O**CP | "Can I extend without modifying?" |
| **L**SP | "Can subtypes replace base types safely?" |
| **I**SP | "Are clients forced to depend on unused methods?" |
| **D**IP | "Do high-level modules depend on abstractions?" |

See: [references/solid-principles.md](references/solid-principles.md)

### 3. Write Clean, Human-Readable Code

**Naming (priority order):** Consistency → Understandability → Specificity → Brevity → Searchability.

**Structure:**
- One level of indentation per method
- No `else` when possible (early returns)
- When validating untrusted strings against an object/map, use `Object.hasOwn(...)` (or `Object.prototype.hasOwnProperty.call(...)`) — not the `in` operator
- **Wrap primitives in domain objects** (IDs, emails, money)
- First-class collections (wrap arrays in classes)
- One dot per line (Law of Demeter)
- Entities small: < 50 lines per class, < 10 per method
- No more than two instance variables per class

**Value Objects are MANDATORY for** domain primitives. Never use raw primitives for domain concepts in public APIs.

See: [references/clean-code.md](references/clean-code.md)

### 4. Design with Responsibility in Mind

For every class, ask:
1. "What pattern is this?" (Entity, Service, Repository, Factory, etc.)
2. "Is it doing too much?" (Object calisthenics)

**Object stereotypes:** Information Holder, Structurer, Service Provider, Coordinator, Controller, Interfacer.

See: [references/object-design.md](references/object-design.md)

### 5. Manage Complexity

**Essential complexity** = inherent to the problem. **Accidental complexity** = introduced by solutions. Minimize accidental complexity.

**Detect via:** Change amplification, cognitive load, unknown unknowns.

**Fight with:** YAGNI, KISS, DRY (only after Rule of Three).

See: [references/complexity.md](references/complexity.md)

### 6. Architect for Change

- **Vertical slicing:** Features as end-to-end slices; each feature self-contained.
- **Dependency rule:** Dependencies point inward (toward domain). Infrastructure depends on domain, never reverse.

See: [references/architecture.md](references/architecture.md)

## Four Elements of Simple Design (XP)

1. **Runs all the tests**
2. **Expresses intent**
3. **No duplication** (Rule of Three)
4. **Minimal** (fewest classes/methods)

## Code Smell Detection

| Smell | Solution |
|-------|----------|
| Long Method | Extract methods, compose method |
| Large Class | Extract class, SRP |
| Long Parameter List | Introduce parameter object |
| Divergent Change | Split into focused classes |
| Shotgun Surgery | Move related code together |
| Feature Envy | Move method to envied class |
| Data Clumps | Extract class |
| Primitive Obsession | Value objects |
| Switch Statements | Polymorphism |
| Speculative Generality | YAGNI — remove |

See: [references/code-smells.md](references/code-smells.md)

## Design Patterns

**Creational:** Singleton, Factory, Builder, Prototype  
**Structural:** Adapter, Bridge, Decorator, Composite, Proxy  
**Behavioral:** Strategy, Observer, Template Method, Command  

**Don't force patterns.** Let them emerge from refactoring.

See: [references/design-patterns.md](references/design-patterns.md)

## Testing Strategy

**Test types:** Unit (many, fast) → Integration (some) → E2E (few). Use Arrange-Act-Assert. Name tests with concrete examples, not abstract statements.

See: [references/testing.md](references/testing.md)

## Behavioral Principles

- **Tell, Don't Ask** — Command objects; don't query and decide.
- **Law of Demeter** — Only talk to immediate friends.
- **Design by Contract** — Preconditions, postconditions, invariants.

## Checklists

**Pre-code:** Understand requirement; what test first?; simplest solution?; real vs hypothetical problem?

**During:** Simplest thing? Single responsibility? Abstractions vs concretions? Clear names? Duplication to extract (Rule of Three)?

**Post-code:** All tests pass? Dead code? Simplify conditions? Accurate names? Would a junior understand in 6 months?

## Red Flags — Stop and Rethink

- Writing code without a test
- Class with > 2 instance variables
- Method > 10 lines
- More than one level of indentation
- Using `else` when early return works
- Hardcoding configurable values
- Abstractions before third duplication
- "Just in case" features
- Depending on concrete implementations
- God classes

## Remember

> "A little bit of duplication is 10x better than the wrong abstraction."

> "Focus on WHAT needs to happen, not HOW."

> "Design principles become second nature through practice."

Goal: **Systems thinking** — principles internalized, focus on optimizing the entire development process.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vivek-tiwari-vt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
