---
name: biome
description: Biome fast formatter and linter. Use for code quality. Use when this capability is needed.
metadata:
  author: g1joshi
---

# Biome

Biome (formerly Rome) is a unified toolchain. It replaces **Prettier** and **ESLint**. v2.0 (2025) adds a plugin system (GritQL) and multi-file analysis.

## When to Use

- **Greenfield Projects**: Start with Biome for instant linting/formatting.
- **CI Speed**: It lints a monorepo in milliseconds.
- **Simplicity**: One config file `biome.json`.

## Core Concepts

### Formatter

97% compatibility with Prettier.

### Linter

100+ rules. fast, informative diagnostics.

### Analyzer

Understands import graphs (unlike ESLint which mostly checks single files).

## Best Practices (2025)

**Do**:

- **Migrate**: Use `biome migrate` to import Prettier/ESLint configs.
- **Use Editor Extension**: The VS Code extension is extremely fast.

**Don't**:

- **Don't mix with Prettier**: Pick one.

## References

- [Biome Documentation](https://biomejs.dev/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
