---
name: test-failure-investigator
description: Diagnose failing or flaky tests by classifying signal, reproducing deterministically, and narrowing to root cause. Use for red CI, unstable tests, or local test regressions. Use when this capability is needed.
metadata:
  author: puukis
---
Treat failing tests as evidence, not noise.

1. Reproduce failures with minimal scope.
2. Separate deterministic failures from flakes.
3. Inspect fixture state, timing assumptions, and environment coupling.
4. Identify smallest code path that explains failure.
5. Validate fix with targeted reruns before broad suite reruns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/puukis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
