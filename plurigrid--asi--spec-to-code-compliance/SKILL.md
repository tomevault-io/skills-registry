---
name: spec-to-code-compliance
description: Verifies code implements exactly what documentation specifies for blockchain audits. Use when comparing code against whitepapers, finding gaps between specs and implementation, or performing compliance checks for protocol implementations. Use when this capability is needed.
metadata:
  author: plurigrid
---

# Spec To Code Compliance Skill

**Trit**: -1 (MINUS)
**Category**: spec-to-code-compliance
**Author**: Trail of Bits
**Source**: trailofbits/skills
**License**: AGPL-3.0

## Description

Verifies code implements exactly what documentation specifies for blockchain audits. Use when comparing code against whitepapers, finding gaps between specs and implementation, or performing compliance checks for protocol implementations.

## When to Use

This is a Trail of Bits security skill. Refer to the original repository for detailed usage guidelines and examples.

See: https://github.com/trailofbits/skills

## Related Skills

- audit-context-building
- codeql
- semgrep
- variant-analysis


## SDF Interleaving

This skill connects to **Software Design for Flexibility** (Hanson & Sussman, 2021):

### Primary Chapter: 3. Variations on an Arithmetic Theme

**Concepts**: generic arithmetic, coercion, symbolic, numeric

### GF(3) Balanced Triad

```
spec-to-code-compliance (+) + SDF.Ch3 (○) + [balancer] (−) = 0
```

**Skill Trit**: 1 (PLUS - generation)


### Connection Pattern

Generic arithmetic crosses type boundaries. This skill handles heterogeneous data.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plurigrid) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
