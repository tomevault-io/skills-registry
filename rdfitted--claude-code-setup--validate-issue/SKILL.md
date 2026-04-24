---
name: validate-issue
description: Validate a GitHub issue against the codebase and expected behavior; use when asked to verify an issue report. Use when this capability is needed.
metadata:
  author: rdfitted
---

# Validate Issue

## Overview

Use this skill to confirm an issue is accurate and reproducible.

## Inputs

- Issue text or link
- Optional reproduction steps or environment

## Workflow

1. Parse the issue claims and expected behavior.
2. Check relevant code paths for consistency with the claim.
3. Attempt reproduction or simulate with tests when feasible.
4. Record evidence supporting or contradicting the issue.
5. Recommend next actions (fix, clarify, close).

## Output

- Validation summary with evidence and recommendation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rdfitted) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
