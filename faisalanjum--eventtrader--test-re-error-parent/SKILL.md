---
name: test-re-error-parent
description: Retest 2026-02-05: How do child errors propagate? Use when this capability is needed.
metadata:
  author: faisalanjum
---
# Test: Error propagation

1. Call /test-re-error-child
2. Write to earnings-analysis/test-outputs/test-re-error-parent.txt:
   - Did you receive an exception/error object? Or just text?
   - What did the child return?
   - "ERROR_PROPAGATION: EXCEPTION" if you got a thrown error
   - "ERROR_PROPAGATION: TEXT_ONLY" if error was embedded in text response

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
