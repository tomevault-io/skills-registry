---
name: workload-regression-maintainer
description: Maintain realistic workload examples and regression tests that verify generated log extension behavior across edge cases. Use when this capability is needed.
metadata:
  author: doljae
---

# Workload Regression Maintainer

Use this skill when tasks involve usage examples or integration-style behavior checks.

## Workflow
1. Identify missing or failing usage scenario.
2. Add/update example class under `workload/src/main/kotlin/examples`.
3. Add/update regression tests under `workload/src/test/kotlin/examples`.
4. Keep examples concise but representative.
5. Verify with `./gradlew :workload:test`.

## Guardrails
- Keep workload free of processor implementation logic.
- Favor edge cases that mirror real user projects (nested types, visibility, reserved words, package depth).
- Maintain readable test naming and assertions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doljae) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
