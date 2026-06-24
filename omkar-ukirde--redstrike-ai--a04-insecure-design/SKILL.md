---
name: a04-insecure-design
description: Skills for exploiting insecure design patterns including race conditions and parameter pollution per OWASP A04:2021. Use when this capability is needed.
metadata:
  author: omkar-ukirde
---

# Insecure Design (OWASP A04)

Flaws in design and architecture that cannot be fixed by proper implementation.

## Skills

- [Race Condition](references/race-condition.md) - Timing-based exploitation
- [Parameter Pollution](references/parameter-pollution.md) - HTTP parameter manipulation

## Quick Reference

| Attack | Target | Technique |
|--------|--------|-----------|
| Race Condition | Financial, limits | Parallel requests |
| HPP | WAF bypass, logic | Duplicate parameters |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/omkar-ukirde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
