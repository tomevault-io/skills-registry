---
name: test-arguments
description: Test $ARGUMENTS substitution in skill content Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Arguments Substitution Test

**Goal**: Test if $ARGUMENTS gets replaced with passed arguments.

## Received Arguments

Raw: $ARGUMENTS

## Task

Write the following to `earnings-analysis/test-outputs/arguments-result.txt`:

```
TEST: $ARGUMENTS substitution
DATE: {current date}

ARGUMENTS RECEIVED: $ARGUMENTS

SUBSTITUTION WORKED: [YES if you see actual args above, NO if you see literal "$ARGUMENTS"]
```

If no arguments were passed, note that too.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
