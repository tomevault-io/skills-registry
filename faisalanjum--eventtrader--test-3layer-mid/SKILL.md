---
name: test-3layer-mid
description: Layer 2 (middle) of 3-layer test - calls Layer 3 Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Layer 2 Middle

**Goal**: Call Layer 3 skill and pass result to Layer 1.

## Task

1. Note: "LAYER2_SECRET = orange"
2. Call `/test-3layer-bottom`
3. Capture the result
4. Write to: `earnings-analysis/test-outputs/3layer-mid.txt`

Return format: "LAYER2_RECEIVED: [what Layer 3 returned] | LAYER2_SECRET: orange"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
