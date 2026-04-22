---
name: fuzz
description: Set up and run fuzz testing to discover crashes, edge cases, and security vulnerabilities. Use when user says "fuzz test", "fuzz this function", "find edge cases", or wants to stress-test input handling with randomized data. Use when this capability is needed.
metadata:
  author: gr1m0h
---

Set up and run fuzz testing for the specified target.

## Context

Project language:
!`ls *.go go.mod 2>/dev/null && echo "Go" || ls Cargo.toml 2>/dev/null && echo "Rust" || ls package.json 2>/dev/null && echo "JavaScript/TypeScript" || ls *.py pyproject.toml 2>/dev/null && echo "Python" || echo "Unknown"`

## Target: $ARGUMENTS

## Instructions

1. Identify the target function/module and its input types
2. Select appropriate fuzzing framework for the language
3. Create fuzz harness with seed corpus
4. Configure fuzzing parameters (iterations, timeout)
5. Run fuzz campaign
6. Triage and report findings with minimized reproductions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gr1m0h) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
