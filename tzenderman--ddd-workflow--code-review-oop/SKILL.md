---
name: code-review-oop
description: OOP-focused code review. Validates SRP, composition over inheritance, pattern usage, and conditional elimination. Pairs with code-review-ddd for complete review. Use when this capability is needed.
metadata:
  author: tzenderman
---

# OOP Code Review

## Overview

Core principles for maintainable OOP in Python:
- Small, single-purpose methods; extract convenience methods rather than combining responsibilities.
- Abstractions describe contracts, not implementations (select_rate, not cheapest_rate).
- Prefer composition over inheritance; inject behavior via protocols/ABCs.
- **Eliminate conditionals**; use Strategy/State/Chain of Responsibility to isolate variation. Patterns are almost never overkill.
- Depend on abstractions (DI), not concretions; keep side effects out of strategies/adapters.

If any of this conflicts with your first instinct under time pressure, stop and refactor before adding flags or branching logic.

## Conditionals are the enemy (no compromises)

**Conditionals make code hard to test and hard to extend.** Every `if/elif/else` is a code path that must be tested, a place where bugs hide, and a location that must change when behavior varies.

Why patterns are almost never overkill:
- A "simple" conditional today becomes a tangled mess of nested branches tomorrow.
- Each conditional couples the caller to every variant's implementation details.
- Testing conditionals requires exercising every branch; testing strategies requires one test per strategy.
- Adding a new variant to a conditional means editing existing code; adding a strategy means adding a new class.

**Never rationalize conditionals.** Watch for these thoughts:
- "It's just one little if statement" → That's how all conditional rot starts.
- "State pattern is overkill for two states" → No, it isolates state-specific behavior and makes adding a third state trivial.
- "Strategy pattern is overkill for two algorithms" → No, it makes testing each algorithm in isolation possible.
- "I'll refactor when it gets complex" → You won't. The conditional will grow until refactoring is painful.
- "This is just dispatch logic" → Dispatch belongs in a registry or factory, not inline conditionals.
- "It's only used in one place" → One place with three branches is three responsibilities in one place.

**The rule:** If you're about to write `if`, ask: "What pattern eliminates this conditional?" State, Strategy, Factory, Chain of Responsibility, or Command almost always apply. Use them.

## Eliminating conditionals with patterns

**Default to patterns. Conditionals require justification, not patterns.**

Use Strategy when:
- You have direction- or policy-specific behavior (even just two variants).
- You want each variant testable in isolation.
- You want zero client changes to add a variant.

Use State when:
- Object behavior varies by internal state (even just two states).
- State transitions should be explicit and testable.
- You want state-specific logic isolated, not scattered in conditionals.

Use Command/Registry when:
- You dispatch by event type (e.g., webhooks, actions).
- Handlers should be independent, swappable units.

Use Factory when:
- Object creation varies by type or configuration.
- Construction logic would otherwise live in a conditional.

**There is no "otherwise, keep it simple."** A conditional is not simple—it's a testability and extensibility liability disguised as simplicity.

## Red Flags (refactor now)

- **Any conditional controlling behavior** → Use State, Strategy, Factory, or Command instead.
- Method name contains "and" or mixes responsibilities.
- Adding boolean flags to extend behavior in-place.
- Protocol/ABC methods name an implementation (cheapest_rate, fastest_query).
- Client must change when adding a new strategy/handler/provider.
- One class mixes translation, business logic, and IO.

### Conditional rationalizations (never accept these)
- "It's just two branches" → Two branches = two responsibilities. Use State or Strategy.
- "Adding a pattern for this is overkill" → Patterns are never overkill. Conditionals are always technical debt.
- "I'll refactor when there's a third case" → You won't. Do it now.
- "This conditional is obvious" → Obvious today, mysterious tomorrow. Pattern intent is always clearer.
- "It's just early return / guard clause" → Guard clauses for validation are OK. Behavior branching is not.

## Quick fixes

| Symptom | Fix |
| --- | --- |
| `if/elif/else` controlling behavior | Replace with State, Strategy, Factory, or Command—no exceptions |
| "and" in method name | Split into focused methods; add a convenience wrapper |
| Boolean flag to toggle behavior | Extract strategies/states; inject behavior |
| Implementation in method name | Rename to contract (select, calculate, render) |
| New behavior needs client changes | Depend on abstraction; inject implementations |
| Adapter with business logic | Move logic to services; keep adapter as translation only |
| "Just this one conditional" | No. Use a pattern. Conditionals are never acceptable for behavior variation. |

## Choose a pattern (yes/no guide)

- Need to pick among interchangeable algorithms? → Strategy → `patterns/strategy.md`
- Same object should change behavior as state changes? → State → `patterns/state.md`
- Encapsulate operations (queue/undo/log/retry)? → Command → `patterns/command.md`
- Notify many listeners when something changes? → Observer → `patterns/observer.md`
- Make incompatible interfaces work together? → Adapter → `patterns/adapter.md`
- Build complex objects step-by-step with options? → Builder → `patterns/builder.md`
- Create families of related objects consistently? → Abstract Factory → `patterns/abstract-factory.md`
- Part–whole hierarchies; treat groups/leaves uniformly? → Composite → `patterns/composite.md`
- Simplify usage of a complex subsystem? → Facade → `patterns/facade.md`
- Same algorithm with overridable steps? → Template Method → `patterns/template-method.md`
- Pass requests along handlers until one handles? → Chain of Responsibility → `patterns/chain-of-responsibility.md`
- Reduce many-to-many communication via a hub? → Mediator → `patterns/mediator.md`
- Add new operations over structured data without modifying nodes? → Visitor → `patterns/visitor.md`

Tie-breakers:
- Prefer composition-first (Strategy, Adapter, Composite) before inheritance-first (Template Method, Visitor) unless algorithm invariants are strong.
- When multiple match, pick the smallest change that isolates variation and removes conditionals.
- **When in doubt, use a pattern.** The cost of a "unnecessary" pattern is near zero; the cost of a conditional is ongoing technical debt.

## Dependency injection (ban service locator)

- Never call global getters like `get_email_service()` or `get_shipping_service()` from deep functions, background tasks, or domain code.
- Inject dependencies via constructor or function parameters at the application boundary (controller → service → task).

## Testing guidance

- Test each responsibility separately (selection vs purchase; translation vs orchestration).
- Strategies should be pure (deterministic), easy to unit test without IO.
- Adapters are thin translation; stub the external client and assert normalized output.
- Keep convenience methods that compose single-responsibility methods for callers.
- **Patterns eliminate branch coverage burden.** One test per strategy/state vs N tests to cover all conditional branches. This alone justifies patterns over conditionals.

## References (load on demand)

- Code review checklist: `criteria/oop-checklist.md`
- Patterns: `patterns/*.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tzenderman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
