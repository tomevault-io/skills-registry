---
name: test-review
description: Review tests to replace loose validation (contains, isNotNull, isNotEmpty) with strict validation (isEqualTo on parsed fields). Use when user wants to improve test assertions or mentions /test-review command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /test-review - Improve Test Assertions

## Usage
```
/test-review                           # Review all tests
/test-review LoginControllerTest.java  # Specific test file
```

## Workflow

1. Load `.claude/agents/test-review-agent.md`
2. Find tests using `contains()`, `isNotNull()`, `isNotEmpty()` where exact value is known
3. Verify all response fields have strict assertions (read DTO, check every field)
4. Replace loose assertions with exact assertions on parsed fields
5. Run tests to verify behavior unchanged

## Anti-Patterns to Fix

**contains() on structured data:**
```java
// BAD:  assertThat(setCookie).contains("SESSION=");
// GOOD: assertThat(cookie.getName()).as("cookie name").isEqualTo("SESSION");
```

**isNotNull() when value is known:**
```java
// BAD:  assertThat(response.getExpiresAt()).isNotNull();
// GOOD: assertThat(response.getExpiresAt()).isEqualTo(expected);
```

**isNotEmpty() when content is known:**
```java
// BAD:  assertThat(response.getPermissions()).isNotEmpty();
// GOOD: assertThat(response.getRoles()).containsExactly("ADMIN", "MEMBER");
```

## Rules

- Parse before assert — never assert on raw strings for structured data
- One field per assertion — easier to debug
- Use `.as()` — describe what you're validating

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
