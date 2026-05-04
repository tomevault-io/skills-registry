---
name: cargo-fuzz
description: Rust fuzzing with cargo-fuzz (libFuzzer). Use when this capability is needed.
metadata:
  author: neversight
---

# Cargo Fuzz Skill

**Trit**: -1 (MINUS)
**Category**: testing-handbook-skills
**Author**: Trail of Bits
**Source**: trailofbits/skills
**License**: AGPL-3.0

## Description

Rust fuzzing with cargo-fuzz (libFuzzer).

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
cargo-fuzz (−) + SDF.Ch4 (+) + [balancer] (○) = 0
```

**Skill Trit**: -1 (MINUS - verification)


### Connection Pattern

Pattern matching extracts structure. This skill recognizes and transforms patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
