---
name: gate-5-test-execution
description: Gate 5 validation: Test execution and 100% pass rate requirement Use when this capability is needed.
metadata:
  author: zerobias-org
---

### Gate 5: Test Execution

**STOP AND CHECK:**
```bash
# All tests must pass
npm test
echo "Exit code: $?"
# Must be 0

# No failures
npm test 2>&1 | grep -i "fail"
# Must return nothing
```

**PROCEED ONLY IF:**
- ✅ All new tests pass
- ✅ No regression in existing tests
- ✅ Zero test failures
- ✅ Coverage maintained or improved

**IF FAILED:** Fix failing tests before building.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zerobias-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
