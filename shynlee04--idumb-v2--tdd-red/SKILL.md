---
name: tdd-red
description: Write failing tests FIRST before any implementation. RED phase of RED-GREEN-REFACTOR cycle. Non-negotiable before implementing features. Use when this capability is needed.
metadata:
  author: shynlee04
---

# TDD Red Phase

> **TRAP 8 Defense**: Tests before implementation

## The Rule

Write a failing test that describes the desired behavior BEFORE writing any implementation code.

## RED Phase Requirements

1. **Test MUST fail** - If it passes immediately, you're testing the wrong thing
2. **Test MUST be specific** - Testing exact expected behavior
3. **Test MUST be minimal** - Only test one thing
4. **Test MUST compile** - TypeScript clean

## Test Template

```typescript
describe('FeatureName', () => {
  it('should [expected behavior] when [condition]', () => {
    // Arrange - Set up test data
    const input = createTestInput();
    
    // Act - Execute the behavior
    const result = featureUnderTest(input);
    
    // Assert - Verify expected outcome
    expect(result).toBe(expectedValue);
  });
});
```

## Before Writing Test

Ask yourself:
1. What is the smallest behavior I can test?
2. What exact input/output do I expect?
3. Am I testing behavior, not implementation?
4. Will this test fail for the right reason?

## After Test Written

1. Run the test with project test command
2. Confirm it **FAILS**
3. Read the failure message - it should describe missing behavior
4. Proceed to GREEN phase ONLY after red confirmed

## Verification

```bash
# Run test - expect FAIL
pnpm test:fast -- --run <test-file>
```

## RED Phase Complete When

- [ ] Test is written and compiles
- [ ] Test fails when run
- [ ] Failure message describes the missing behavior
- [ ] No implementation code written yet

## Failure Mode

If you skip RED:
```
⛔ TDD VIOLATION

Attempted: Write implementation without failing test

ACTION: Stop. Write failing test first.
```

## Next Phase

After RED confirmed → Proceed to GREEN phase (minimal implementation to pass test)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shynlee04) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
