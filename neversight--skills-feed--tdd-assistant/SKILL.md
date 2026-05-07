---
name: tdd
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# TDD Assistant

> **Language**: English | [繁體中文](../../locales/zh-TW/skills/tdd-assistant/SKILL.md)

**Version**: 1.0.0
**Last Updated**: 2026-01-07
**Applicability**: Claude Code Skills

---

## Purpose

This skill guides developers through the Test-Driven Development workflow, helping them:
- Write effective failing tests (Red phase)
- Implement minimum code to pass tests (Green phase)
- Refactor safely while keeping tests green (Refactor phase)
- Identify and avoid common TDD anti-patterns
- Integrate TDD with BDD and ATDD approaches
- Apply TDD appropriately based on context

---

## Quick Reference

### TDD Cycle Checklist

```
┌─────────────────────────────────────────────────────────────────┐
│  🔴 RED Phase                                                   │
│  □ Test describes expected behavior, not implementation         │
│  □ Test name clearly states what is being tested                │
│  □ Test follows AAA pattern (Arrange-Act-Assert)                │
│  □ Test fails for the RIGHT reason                              │
│  □ Failure message is clear and actionable                      │
├─────────────────────────────────────────────────────────────────┤
│  🟢 GREEN Phase                                                 │
│  □ Write MINIMUM code to pass the test                          │
│  □ "Fake it" is acceptable (hardcode if needed)                 │
│  □ Don't optimize or over-engineer                              │
│  □ Test now passes                                              │
│  □ All other tests still pass                                   │
├─────────────────────────────────────────────────────────────────┤
│  🔵 REFACTOR Phase                                              │
│  □ Remove duplication (DRY)                                     │
│  □ Improve naming                                               │
│  □ Extract methods if needed                                    │
│  □ Run tests after EVERY change                                 │
│  □ No new functionality added                                   │
│  □ All tests still pass                                         │
└─────────────────────────────────────────────────────────────────┘
```

### FIRST Principles Quick Reference

| Principle | Check | Common Violations |
|-----------|-------|-------------------|
| **F**ast | < 100ms per unit test | Database calls, file I/O, network |
| **I**ndependent | No shared state | Static variables, execution order dependency |
| **R**epeatable | Same result always | DateTime.Now, Random, external services |
| **S**elf-validating | Clear pass/fail | Manual log checking, no assertions |
| **T**imely | Test before code | Writing tests after implementation |

### Anti-Pattern Quick Detection

| Symptom | Likely Anti-Pattern | Quick Fix |
|---------|---------------------|-----------|
| Tests break on refactoring | Testing implementation details | Test behavior only |
| Tests pass but bugs in prod | Over-mocking | Add integration tests |
| Random test failures | Test interdependence | Isolate test state |
| Slow test suite | Too many integration tests | Increase unit test ratio |
| Team avoids writing tests | Complex test setup | Simplify with builders |

---

## TDD vs BDD vs ATDD Quick Reference

| Aspect | TDD | BDD | ATDD |
|--------|-----|-----|------|
| **Who writes** | Developers | Developers + BA + QA | All stakeholders |
| **Language** | Code | Gherkin (Given-When-Then) | Business language |
| **Level** | Unit/Component | Feature/Scenario | Acceptance |
| **When** | During coding | Before coding | Before sprint |

### When to Use Which

```
Is it a technical implementation detail?
├─ Yes → TDD
└─ No → Is there a business stakeholder?
         ├─ Yes → Does stakeholder need to read/validate tests?
         │        ├─ Yes → ATDD → BDD → TDD
         │        └─ No → BDD → TDD
         └─ No → TDD
```

---

## Workflow Assistance

### Red Phase Guidance

When writing a failing test, ensure:

1. **Clear Intent**
   ```typescript
   // ❌ Vague
   test('it works', () => { ... });

   // ✅ Clear
   test('should calculate discount when order total exceeds threshold', () => { ... });
   ```

2. **Single Behavior**
   ```typescript
   // ❌ Multiple behaviors
   test('should validate and save user', () => { ... });

   // ✅ Single behavior
   test('should reject invalid email format', () => { ... });
   test('should save user with valid data', () => { ... });
   ```

3. **Proper Assertions**
   ```typescript
   // ❌ No assertion
   test('should process order', () => {
     orderService.process(order);
     // Missing assertion!
   });

   // ✅ Clear assertion
   test('should mark order as processed', () => {
     const result = orderService.process(order);
     expect(result.status).toBe('processed');
   });
   ```

### Green Phase Guidance

When making tests pass, remember:

1. **Minimum Implementation**
   ```typescript
   // Test: should return "FizzBuzz" for numbers divisible by both 3 and 5

   // ❌ Over-engineered first pass
   function fizzBuzz(n: number): string {
     const divisibleBy3 = n % 3 === 0;
     const divisibleBy5 = n % 5 === 0;
     if (divisibleBy3 && divisibleBy5) return 'FizzBuzz';
     if (divisibleBy3) return 'Fizz';
     if (divisibleBy5) return 'Buzz';
     return n.toString();
   }

   // ✅ Minimum for current test (fake it!)
   function fizzBuzz(n: number): string {
     return 'FizzBuzz'; // Just enough to pass THIS test
   }
   ```

2. **Progressive Generalization**
   - First test: Hardcode the answer
   - Second test: Add simple conditional
   - Third test: Generalize the pattern

### Refactor Phase Guidance

Safe refactoring checklist:

```
Before:
□ All tests are GREEN
□ Understand what the code does

During (one at a time):
□ Extract method → Run tests
□ Rename → Run tests
□ Remove duplication → Run tests
□ Simplify conditional → Run tests

After:
□ All tests still GREEN
□ Code is cleaner
□ No new functionality
```

---

## Integration with SDD

When working with Spec-Driven Development:

### Spec → Test Mapping

| Spec Section | Test Type |
|--------------|-----------|
| Acceptance Criteria | Acceptance tests (ATDD/BDD) |
| Business Rules | Unit tests (TDD) |
| Edge Cases | Unit tests (TDD) |
| Integration Points | Integration tests |

### Workflow

```
1. Read Spec (SPEC-XXX)
   ↓
2. Identify Acceptance Criteria
   ↓
3. Write BDD scenarios (if applicable)
   ↓
4. For each scenario:
   ├─ TDD: Red → Green → Refactor
   └─ Mark AC as implemented
   ↓
5. All ACs implemented?
   ├─ Yes → Mark Spec as complete
   └─ No → Return to step 4
```

### Test File Reference

```typescript
/**
 * Tests for SPEC-001: User Authentication
 *
 * Acceptance Criteria:
 * - AC-1: User can login with valid credentials
 * - AC-2: Invalid password shows error
 * - AC-3: Account locks after 3 failed attempts
 */
describe('User Authentication (SPEC-001)', () => {
  // Tests organized by AC
});
```

---

## Configuration Detection

This skill supports project-specific configuration.

### Detection Order

1. Check `CONTRIBUTING.md` for "Disabled Skills" section
   - If this skill is listed, it is disabled for this project
2. Check `CONTRIBUTING.md` for "TDD Standards" section
3. Check for existing test patterns in the codebase
4. If not found, **default to standard TDD practices**

### First-Time Setup

If no configuration found and context is unclear:

1. Ask: "This project hasn't configured TDD preferences. Which approach do you prefer?"
   - Pure TDD (Red-Green-Refactor)
   - BDD-style TDD (Given-When-Then)
   - ATDD with BDD and TDD

2. After selection, suggest documenting in `CONTRIBUTING.md`:

```markdown
## TDD Standards

### Preferred Approach
- Primary: TDD (Red-Green-Refactor)
- For features with business stakeholders: BDD

### Test Naming Convention
- Pattern: `should_[behavior]_when_[condition]`
- Example: `should_return_error_when_email_invalid`

### Coverage Targets
- Unit: 80%
- Integration: 60%
```

---

## Detailed Guidelines

For complete standards, see:
- [TDD Core Standard](../../core/test-driven-development.md)
- [TDD Workflow Guide](./tdd-workflow.md)
- [Language Examples](./language-examples.md)

For related testing standards:
- [Testing Standards](../../core/testing-standards.md)
- [Test Completeness Dimensions](../../core/test-completeness-dimensions.md)

---

## Refactor Phase Deep Dive (YAML Compressed)

```yaml
# === TDD REFACTOR = SAFE SMALL REFACTORING ===
refactor_phase:
  scope: "Single method/class improvements"
  duration: "5-15 minutes max"
  prerequisite: "All tests GREEN"
  rule: "No new functionality"

techniques:
  safe_refactorings:
    - extract_method: "Long method → smaller methods"
    - rename: "Improve naming clarity"
    - inline_variable: "Remove unnecessary temp"
    - replace_magic_number: "Constant with meaning"
    - extract_class: "SRP violation fix"
    - move_method: "Better cohesion"

workflow:
  steps:
    1: "Confirm all tests GREEN"
    2: "Make ONE small change"
    3: "Run tests immediately"
    4: "If RED → revert, try smaller step"
    5: "If GREEN → commit, repeat"

# === WHEN TO USE /refactor INSTEAD ===
escalation:
  use_tdd_refactor:
    - "Improving code just written"
    - "Single method/class cleanup"
    - "Takes <15 minutes"
  use_refactoring_assistant:
    - "Legacy code modernization"
    - "Refactor vs Rewrite decision"
    - "Large-scale architectural change"
    - "Technical debt management"
    - "Strangler Fig pattern needed"

# === REFACTOR ANTIPATTERNS ===
antipatterns:
  - name: "Refactoring without tests"
    symptom: "No safety net"
    fix: "Add characterization tests first"
  - name: "Too large refactoring"
    symptom: "Tests RED for hours"
    fix: "Smaller steps, commit frequently"
  - name: "Adding features while refactoring"
    symptom: "Scope creep"
    fix: "Separate commits: refactor then feature"
  - name: "Refactoring everything"
    symptom: "Perfectionism paralysis"
    fix: "Only refactor code you're changing"
```

---

## Related Standards

- [Test-Driven Development](../../core/test-driven-development.md) - Core TDD standard
- [Refactoring Standards](../../core/refactoring-standards.md) - Large-scale refactoring
- [Testing Standards](../../core/testing-standards.md) - Testing framework
- [Test Completeness Dimensions](../../core/test-completeness-dimensions.md) - 7 dimensions
- [Spec-Driven Development](../../core/spec-driven-development.md) - SDD integration
- [Testing Guide Skill](../testing-guide/SKILL.md) - Testing guide
- [Test Coverage Assistant](../test-coverage-assistant/SKILL.md) - Coverage assistance
- [Refactoring Assistant](../refactoring-assistant/SKILL.md) - Large-scale refactoring

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-01-07 | Initial release |

---

## License

This skill is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

**Source**: [universal-dev-standards](https://github.com/AsiaOstrich/universal-dev-standards)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
