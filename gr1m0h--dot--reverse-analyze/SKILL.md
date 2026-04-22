---
name: reverse-analyze
description: Perform reverse engineering analysis on code or binary for security research. Use when user says "reverse engineer", "analyze binary", "decompile", or investigating undocumented systems and legacy code. Use when this capability is needed.
metadata:
  author: gr1m0h
---

Perform reverse engineering analysis for security research.

## Context

Target files:
!`file $1 2>/dev/null || echo "Target not specified"`

## Target: $ARGUMENTS

## Instructions

1. Identify the target type (source code, binary, protocol capture)
2. Perform appropriate static analysis
3. Document architecture and key components
4. Identify security-relevant behaviors
5. Extract indicators of compromise if applicable
6. Generate structured analysis report

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gr1m0h) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
