---
name: multi-language-feature-builder
description: Build multi-file features that span TypeScript and Python. Use when a feature requires changes in both languages. Use when this capability is needed.
metadata:
  author: complytime-labs
---

# Multi-Language Feature Builder

When asked to implement a feature that touches both TypeScript and Python:

1. Identify the contract boundary (e.g., REST endpoint, shared schema, CLI
   interface) and agree on it before writing code in either language.
2. Implement the Python side first; write the TypeScript side to match the
   agreed contract.
3. Keep each language's code idiomatic — do not port Python patterns into
   TypeScript or vice versa.
4. Update both language's tests together; a feature is not done until tests
   pass in both.

If the scope is unclear, ask for the entry point and expected output before
starting.

---
> Source: [complytime-labs/lola-eval](https://github.com/complytime-labs/lola-eval) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
