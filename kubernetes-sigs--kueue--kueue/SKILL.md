---
name: pointless-intermediate-variables
description: Flag redundant local variables that add noise without adding clarity. Use when this capability is needed.
metadata:
  author: kubernetes-sigs
---

# Skill: Pointless Intermediate Variables

**Pointless intermediate variables** — `x := foo.Bar; use(x)` where `use(foo.Bar)` is
equally clear. Redundant locals add noise without adding clarity.

---
> Source: [kubernetes-sigs/kueue](https://github.com/kubernetes-sigs/kueue) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-01 -->
