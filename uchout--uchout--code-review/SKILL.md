---
name: rs-review
description: Review Rust code for correctness, design principles, and rule compliance. Use when this capability is needed.
metadata:
  author: uchout
---

# /rs-review

Review Rust code for rule compliance and best practices.

## Usage

```
/rs-review              # Review all modified .rs files
/rs-review src/lib.rs   # Review specific file
/rs-review src/         # Review directory
```

## What It Checks

**P0 - Critical:**
- Type-driven development (newtype, invalid states, exhaustive match)
- Over-engineering (premature abstraction, generic infection)

**P1 - Important:**
- Code style (SRP, encapsulation, naming, modules)

**Smells:**
- Repeated `map_err` → delegates to `rust-error-handling-checker`
- Generic infection → delegates to `rust-abstraction-builder`
- Compilation issues → delegates to `rust-compile-challenger`

## Output

```markdown
## Review: [filename]

### Critical (must fix)
- [ ] Issue → fix

### Warning (should fix)
- [ ] Issue → fix

### Suggestion
- [ ] Issue → fix
```

## Quick Mode

For fast checks without deep analysis:

```
/rs-review --quick      # clippy + fmt only
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uchout) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
