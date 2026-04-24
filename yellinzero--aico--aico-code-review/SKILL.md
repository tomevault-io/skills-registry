---
name: aico-code-review
description: | Use when this capability is needed.
metadata:
  author: yellinzero
---

# Code Review

## When to Request Review

**Mandatory:**

- After completing each task
- After implementing major feature
- Before merge to main

**Optional:**

- When stuck (fresh perspective)
- Before refactoring
- After fixing complex bug

## Review Checklist

| Category       | Check                           |
| -------------- | ------------------------------- |
| Correctness    | Does it do what it should?      |
| Tests          | Are there tests? Do they pass?  |
| Security       | Any vulnerabilities?            |
| Performance    | Any obvious bottlenecks?        |
| Readability    | Is code clear and maintainable? |
| Error Handling | Are errors handled properly?    |

## Issue Severity

| Severity      | Action                           |
| ------------- | -------------------------------- |
| **Critical**  | Fix immediately, blocks progress |
| **Important** | Fix before proceeding            |
| **Minor**     | Note for later, can proceed      |

## Review Output Template

```markdown
## Code Review: [Feature/Task Name]

### Files Modified

- `path/to/file.ts` - [what changed]

### Issues

**Critical:**

- [ ] [Issue description]

**Important:**

- [ ] [Issue description]

**Minor:**

- [ ] [Issue description]

### Assessment

- [ ] Ready to proceed
- [ ] Needs fixes (see issues above)
```

## Key Rules

- ALWAYS review after each task completion
- MUST fix Critical issues immediately
- MUST fix Important issues before proceeding
- Minor issues can be noted for later
- If reviewer is wrong, push back with technical reasoning

## Common Mistakes

- ❌ Skip review because "it's simple" → ✅ Review everything
- ❌ Ignore Critical issues → ✅ Fix immediately
- ❌ Proceed with Important issues → ✅ Fix first

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yellinzero) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
