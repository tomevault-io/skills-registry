---
name: vite-config
description: Generates vite.config.ts for building and serving the Vue 3 application. Configures dev server, API proxy, build format, and plugins.
metadata:
  author: sayali-ingle-pdl
---

# Vite Config Skill

## Purpose
Generate the `vite.config.ts` file for building and serving the Vue 3 application.

## Input Parameters
Read from `copilot-instructions.md` and `docs/requirements/application-parameters.md`:
- `application_id`: The application ID for style tag
- `default_port`: The development server port
- `api_base_path`: The base path for API proxy
- `vite_build_format`: The build output format (system or es)

## Output
Create the file: `vite.config.ts`

## Template
See: `examples.md` in this directory for complete template and detailed examples.

## Notes
- The proxy configuration should be updated with the actual backend service URL
- The `appId` is imported from the global constants file
- CSS is injected by JavaScript in build mode for single-spa compatibility
- SVG loader is configured to preserve viewBox for proper scaling

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sayali-ingle-pdl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
