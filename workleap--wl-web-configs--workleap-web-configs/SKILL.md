---
name: workleap-web-configs
description: | Use when this capability is needed.
metadata:
  author: workleap
---

# wl-web-configs

Workleap's shared configuration library for web tooling. Provides pre-configured packages for ESLint, TypeScript, Rsbuild, Rslib, Stylelint, and Browserslist.

## Philosophy

- **No lock-in**: Default configurations can always be extended or overridden
- **By project type**: Configurations are composed internally and offered per project type for simplicity
- **ESM/ESNext by default**: Targets modern JavaScript environments
- **Distributed via NPM**: Easy to adopt new features by bumping package versions

## Supported Tools (Active)

| Tool | Package | Purpose |
|------|---------|---------|
| Browserslist | `@workleap/browserslist-config` | Browser targets for apps |
| ESLint | `@workleap/eslint-configs` | Code linting |
| Stylelint | `@workleap/stylelint-configs` | CSS linting |
| TypeScript | `@workleap/typescript-configs` | Type checking (linting only) |
| Rsbuild | `@workleap/rsbuild-configs` | Web application bundling |
| Rslib | `@workleap/rslib-configs` | Library bundling |

**In maintenance mode** (do not recommend): PostCSS, SWC, webpack, tsup

## Quick Reference

### Which Configuration to Use?

| Project Type | ESLint | TypeScript | Bundler |
|--------------|--------|------------|---------|
| Web app with React | `defineWebApplicationConfig` | `web-application.json` | `@workleap/rsbuild-configs` |
| React library | `defineReactLibraryConfig` | `library.json` | `@workleap/rslib-configs` |
| TypeScript library (no React) | `defineTypeScriptLibraryConfig` | `library.json` | `@workleap/rslib-configs` |
| Monorepo workspace root | `defineMonorepoWorkspaceConfig` | `monorepo-workspace.json` | N/A |

### Browserslist (Apps Only)

```bash
pnpm add -D @workleap/browserslist-config browserslist
```

```text
# .browserslistrc
extends @workleap/browserslist-config
```

Only for projects emitting application bundles. Libraries should not include Browserslist — the consuming application's Browserslist controls the final output targets.

To add custom browser targets while still using the shared config:

```text
# .browserslistrc
extends @workleap/browserslist-config
IE 11
last 2 OperaMobile 12.1 versions
```

## Reference Guide

For comprehensive setup guides, options, and examples, read the appropriate reference file:

- **ESLint** — [references/eslint.md](references/eslint.md): Installation, `define*Config` functions, rule categories, customization, and VS Code integration
- **TypeScript** — [references/typescript.md](references/typescript.md): Config files by project type, compiler option overrides, path mappings, and CLI scripts
- **Rsbuild** — [references/rsbuild.md](references/rsbuild.md): Dev/build/Storybook configs, predefined options, transformers, and Turborepo setup
- **Rslib** — [references/rslib.md](references/rslib.md): Library build/dev/Storybook configs, bundleless vs bundle, transformers, and type declarations
- **Stylelint** — [references/stylelint.md](references/stylelint.md): Installation, `.stylelintrc.json` setup, Prettier integration, and VS Code settings

## Critical Rules

1. **Stick to documented APIs** — these packages are thin wrappers, and undocumented options won't be passed through to the underlying tool
2. **Avoid maintenance-mode packages** — PostCSS, SWC, webpack, and tsup configs won't receive new features; recommend the active alternatives (Rsbuild, Rslib, Stylelint) instead
3. **ESM by default** — all configs target ESM/ESNext because Workleap's toolchain assumes modern JavaScript environments
4. **Browserslist for apps only** — libraries omit Browserslist because the consuming application's config controls the final output targets
5. **TypeScript for linting** — the shared TypeScript configs focus on type-checking; bundlers handle transpilation, so don't mix concerns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/workleap) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
