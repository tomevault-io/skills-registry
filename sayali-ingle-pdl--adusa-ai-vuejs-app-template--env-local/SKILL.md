---
name: env-local
description: Generates .env.local file for local development environment variables. Contains developer-specific configuration like API URLs, ports, and feature flags. Gitignored for security.
metadata:
  author: sayali-ingle-pdl
---

# Environment Local Skill

## Purpose
Generate .env.local file for local development environment configuration.

## Output
Create the file: `.env.local`

## Notes
- `.env.local` is used for local development environment variables
- This file should be in `.gitignore` (never commit secrets)
- Developers must replace `<>` placeholders with actual values
- Used by Vite to inject environment variables at build time
- All Vite env vars must be prefixed with `VITE_` to be exposed to client code
- NODE_ENV determines the build mode (development, production, test)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sayali-ingle-pdl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
