---
name: vite-react-ts-deploy
description: Explain and implement the production build + static deployment workflow for a Vite React-TS app (build output, base path, preview, common hosts). Use when this capability is needed.
metadata:
  author: andireuter
---

# Vite React-TS Build & Deploy Skill

Guide and implement production build and deployment settings.

## Rules / best practices

1) Use:
   - pnpm build (vite build)
   - pnpm preview to validate the production build locally
2) Static hosting:
   - Serve dist/ directory
3) If deploying under a subpath, set base in Vite config.
4) For SPAs, ensure the host is configured for history fallback routing.

## Deliverables

- Commands
- Relevant config changes (base, assets, etc.)
- Host-specific notes when requested

Use template.md as the default structure.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andireuter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
