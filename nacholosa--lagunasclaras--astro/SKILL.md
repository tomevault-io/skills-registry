---
name: astro
description: Skill for using Astro projects. Includes CLI commands, project structure, core config options, and adapters. Use this skill when the user needs to work with Astro or when the user mentions Astro. Use when this capability is needed.
metadata:
  author: nacholosa
---

# Astro Usage Guide

**Always consult [docs.astro.build](https://docs.astro.build) for code examples and latest API.**

Astro is the web framework for content-driven websites.

---

## Quick Reference

### File Location

CLI looks for `astro.config.js`, `astro.config.mjs`, `astro.config.cjs`, and `astro.config.ts` in: `./`. Use `--config` for custom path.

### CLI Commands

- `bun astro dev` - Start the development server.
- `bun astro build` - Build your project and write it to disk.
- `bun astro check` - Check your project for errors.
- `bun astro add` - Add an integration.
- `bun astro sync` - Generate TypeScript types for all Astro modules.

**Re-run after adding/changing plugins.**

### Project Structure

Astro leverages an feature-based folder layout. Reference [.agents/Agent.md](file:///Users/nacho/Work/LagunasClaras/LagunasClaras-app/.agents/Agent.md) for full details.

- `src/pages` - Required. Routing only, must remain thin.
- `src/sections` - Main architectural unit (Features). Owns UI, local data, and types.
- `src/layouts` - Structural wrappers.
- `src/components` - Reusable, feature-agnostic UI. Use `clsx` and style variants.
- `src/styles` - Global styles. Use `global.css` utilities (`.service-card`, `.nav-link`).
- `src/assets` - Media. Use `<picture>` and `webp` for LCP optimization.
- `src/islands` - `.tsx` components for client-side interactivity only.

---

## Technical Standards

- **Strict Typing**: Every component must have a `Props` interface in a `.types.ts` file.
- **Path Aliases**: Use `@/` (src) and `@sections/` (src/sections).
- **Interactivity**: Default to static HTML. Hydrate as late as possible.
- **Design Tokens**: Follow [.agents/skills/design/SKILL.md](file:///Users/nacho/Work/LagunasClaras/LagunasClaras-app/.agents/skills/design/SKILL.md).

---

## Anti-Patterns (Do NOT)

- Treat Astro as React.
- Mix sections (Cross-section imports of local components).
- Add JavaScript "just in case".
- Duplicate logic in `/pages`.

---

## Core Config Options

| Option | Notes                                                                                                               |
| ------ | ------------------------------------------------------------------------------------------------------------------- |
| `site` | Your final, deployed URL. Astro uses this full URL to generate your sitemap and canonical URLs in your final build. |

---

## Adapters

Deploy to your favorite server, serverless, or edge host with build adapters. Use an adapter to enable on-demand rendering in your Astro project.

**Add [Node.js](https://docs.astro.build/en/guides/integrations-guide/node) adapter using astro add:**

```
bun astro add node --yes
```

**Add [Cloudflare](https://docs.astro.build/en/guides/integrations-guide/cloudflare) adapter using astro add:**

```
bun astro add cloudflare --yes
```

**Add [Netlify](https://docs.astro.build/en/guides/integrations-guide/netlify) adapter using astro add:**

```
bun astro add netlify --yes
```

**Add [Vercel](https://docs.astro.build/en/guides/integrations-guide/vercel) adapter using astro add:**

```
bun astro add vercel --yes
```

[Other Community adapters](https://astro.build/integrations/2/?search=&categories%5B%5D=adapters)

## Resources

- [Docs](https://docs.astro.build)
- [Config Reference](https://docs.astro.build/en/reference/configuration-reference/)
- [llms.txt](https://docs.astro.build/llms.txt)
- [GitHub](https://github.com/withastro/astro)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nacholosa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
