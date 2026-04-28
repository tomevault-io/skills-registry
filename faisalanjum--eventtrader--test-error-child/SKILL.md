---
name: test-error-child
description: Child skill that deliberately fails - for error propagation test Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Error Test Child

**Goal**: Deliberately fail to test error propagation.

## Task

1. Write "STARTING" to `earnings-analysis/test-outputs/error-child-start.txt`
2. Then FAIL by trying to read a non-existent file: `/nonexistent/path/that/does/not/exist.txt`
3. This should cause an error that propagates to parent

The parent skill will check what error message it receives.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
