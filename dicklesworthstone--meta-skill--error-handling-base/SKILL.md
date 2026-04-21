---
name: error-handling-base
description: >- Use when this capability is needed.
metadata:
  author: dicklesworthstone
---

# Error Handling Base

> **Core insight:** Good error handling is about communication - to users, developers, and monitoring systems.

## Rules

- Always handle errors explicitly - never ignore them
- Use meaningful error messages that describe what went wrong
- Include context in error messages (what was being attempted)
- Log errors at appropriate levels (error vs warning vs info)
- Distinguish between recoverable and unrecoverable errors

## Pitfalls

- Swallowing exceptions without logging
- Using generic error messages like "An error occurred"
- Exposing internal implementation details in user-facing errors
- Catching broad exception types when specific ones are needed

## Examples

```
// Bad: Generic error
throw new Error("Error");

// Good: Descriptive error with context
throw new Error(`Failed to parse config file '${filename}': ${parseError.message}`);
```

## Checklist

- [ ] All error paths are handled explicitly
- [ ] Error messages are actionable and descriptive
- [ ] Sensitive information is not leaked in errors
- [ ] Errors are logged before being re-thrown or converted

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dicklesworthstone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
