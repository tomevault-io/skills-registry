---
name: coding-practices
description: Code quality practices: error handling, validation, logging, and DRY. Use when writing or reviewing code. Use when this capability is needed.
metadata:
  author: eser
---

# Coding Practices

Guidelines for writing maintainable, robust, and self-documenting code.

## Quick Start

```typescript
// Self-documenting with proper error handling
function createUser(email: string, age: number): User {
  if (!email.includes("@")) throw new Error("Invalid email");
  if (age < 0 || age > 150) throw new Error("Invalid age");
  return { email, age };
}
```

## Key Principles

- Compare entities by IDs, never by slugs/usernames/strings
- Use meaningful names (self-documenting code)
- Comments explain "why", not "what"
- DRY: abstract when used 3+ times
- Validate all input data
- Handle all error cases with proper Error objects
- Never ignore errors
- Use named constants instead of magic values
- Avoid circular dependencies

## References

See [rules.md](references/rules.md) for complete guidelines with examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eser) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
