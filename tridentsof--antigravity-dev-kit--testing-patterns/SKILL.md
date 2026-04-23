---
name: testing-patterns
description: name: testing-patterns Use when this capability is needed.
metadata:
  author: tridentsof
---
---
name: testing-patterns
description: Test strategies and patterns
---

# Testing Patterns

> Test strategies for full-stack applications.

---

## Test Planning Workflow (CRITICAL)

> **Protocol:** Plan -> Test -> Mark Done

1. **Create Plan**: List scenarios to test in a markdown list.
2. **Implement**: Write the test code.
3. **Verify**: Run the test.
4. **Update**: Mark as `[x]` in the plan.

**Example Plan:**
```markdown
- [ ] User can login with valid credentials
- [ ] Login fails with invalid password
```

---

## Test Pyramid

```
        /\
       /E2E\        Few, slow, critical paths
      /------\
     / Integ- \     Some, key integrations
    /--ration--\
   /    Unit    \   Many, fast, isolated
  /--------------\
```

| Type | Speed | Scope |
|------|-------|-------|
| Unit | Fast | Single function/class |
| Integration | Medium | Multiple components |
| E2E | Slow | Full user flow |

---

## AAA Pattern

```csharp
[Fact]
public void Method_Scenario_Expected()
{
    // Arrange - Set up test data
    var input = CreateInput();
    
    // Act - Execute the code
    var result = Process(input);
    
    // Assert - Verify outcome
    Assert.Equal(expected, result);
}
```

---

## Test Naming

Format: `Method_Scenario_ExpectedResult`

| ❌ Bad | ✅ Good |
|--------|---------|
| TestAdd | Add_TwoPositive_ReturnsSum |
| Test1 | GetUser_InvalidId_ThrowsException |

---

## What to Test

| ✅ Test | ❌ Skip |
|---------|---------|
| Business logic | Framework code |
| Edge cases | Getters/setters |
| Error handling | Third-party libs |
| Public API | Implementation details |

---

## Mocking Guidelines

| Mock | Don't Mock |
|------|------------|
| External APIs | The code under test |
| Database | Simple objects |
| File system | Pure functions |
| Time/Random | Business logic |

---

## Code Coverage

| Metric | Target |
|--------|--------|
| Overall | 70-80% |
| Business logic | 90%+ |
| Controllers | 60%+ |
| Infrastructure | Lower priority |

---

## DO / DON'T

| ✅ Do | ❌ Don't |
|-------|---------|
| One concept per test | Test multiple things |
| Descriptive names | Cryptic test names |
| Independent tests | Tests that depend on order |
| Test edge cases | Only happy path |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tridentsof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
