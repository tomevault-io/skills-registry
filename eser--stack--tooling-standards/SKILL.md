---
name: tooling-standards
description: Development tooling: Deno runtime, JSR package registry, deno.json and package.json configuration, and editor settings. Use when setting up projects, choosing packages, or configuring build tools. Use when this capability is needed.
metadata:
  author: eser
---

# Tooling Standards

Standards for Deno runtime, JSR registry, and project configuration.

## Quick Start

```bash
deno install    # install dependencies
deno fmt        # format code
deno lint       # lint code
deno test       # run tests
```

## Key Principles

**Deno:** Use `deno install/fmt/lint/test` instead of npm commands

**Config files:**

- `package.json` for dependencies and scripts
- `tsconfig.json` for TypeScript
- `deno.json` only for fmt/lint settings

**Registry:** Prefer jsr.io over npm. Use `npm:@jsr/` prefix in package.json

**Packages:** JSR when available, npm when no JSR alternative exists

## References

See [rules.md](references/rules.md) for complete guidelines with examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eser) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
