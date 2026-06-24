---
name: wycheproof
description: Google's Wycheproof test vectors for cryptographic implementation testing. Use when this capability is needed.
metadata:
  author: plurigrid
---

# Wycheproof Skill

**Trit**: -1 (MINUS)
**Category**: testing-handbook-skills
**Author**: Trail of Bits
**Source**: trailofbits/skills
**License**: AGPL-3.0

## Description

Google's Wycheproof test vectors for cryptographic implementation testing.

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

### Primary Chapter: 4. Pattern Matching

**Concepts**: unification, match, segment variables, pattern

### GF(3) Balanced Triad

```
wycheproof (+) + SDF.Ch4 (+) + [balancer] (+) = 0
```

**Skill Trit**: 1 (PLUS - generation)


### Connection Pattern

Pattern matching extracts structure. This skill recognizes and transforms patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plurigrid) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
