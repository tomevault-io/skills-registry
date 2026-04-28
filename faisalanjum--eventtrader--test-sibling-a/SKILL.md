---
name: test-sibling-a
description: Sibling A for isolation test - writes secret, checks for sibling B's secret Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Sibling A Test

**Goal**: Test if sibling skills share context.

## Task

1. Write "SIBLING_A_SECRET = red" to `earnings-analysis/test-outputs/sibling-a-secret.txt`
2. Try to find any evidence of "SIBLING_B_SECRET" in your context
3. Check if file `earnings-analysis/test-outputs/sibling-b-secret.txt` exists
4. Report findings

Write to: `earnings-analysis/test-outputs/sibling-a-result.txt`

Return: "SIBLING_A_DONE: red | SAW_B: [yes/no]"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
