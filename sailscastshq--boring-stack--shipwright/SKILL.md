---
name: shipwright
description: > Use when this capability is needed.
metadata:
  author: sailscastshq
---

# Shipwright

Shipwright (`sails-hook-shipwright`) is the modern build system for The Boring JavaScript Stack. Built on [Rsbuild](https://rsbuild.dev), it replaces the legacy Grunt pipeline with a fast, zero-config asset pipeline that supports React, Vue, and Svelte through framework plugins.

## When to Use

Use this skill when:

- Configuring `config/shipwright.js` (framework plugins, build options)
- Understanding the asset pipeline (CSS, JavaScript entry points, static assets)
- Setting up Tailwind CSS with PostCSS
- Configuring server-side rendering (SSR) with `config/inertia.js`
- Debugging the dev server, HMR, or build issues
- Understanding the `views/app.ejs` template and `shipwright.styles()`/`shipwright.scripts()` helpers
- Managing path aliases (`~/` for `assets/js/`)
- Working with code splitting and third-party CSS imports

## Rules

Read individual rule files for detailed explanations and code examples:

- [rules/getting-started.md](rules/getting-started.md) - What Shipwright is, dev/build commands, project structure
- [rules/configuration.md](rules/configuration.md) - config/shipwright.js, framework plugins, entry points, path aliases
- [rules/asset-pipeline.md](rules/asset-pipeline.md) - CSS with Tailwind, JavaScript entry, static assets, code splitting
- [rules/ssr.md](rules/ssr.md) - Server-side rendering setup, SSR entry point, framework differences
- [rules/development.md](rules/development.md) - Dev server, HMR, Node --watch-path, common issues, debugging

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sailscastshq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
