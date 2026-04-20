---
name: application
description: Application configuration and testing. Config files for app name/URL, environment validation with @t3-oss/env-core. Activate when working with app config or running tests. Use when this capability is needed.
metadata:
  author: ludicroushq
---

# Application

## Config

- `src/config/app.ts` - App name/URL
- `src/config/env/` - Environment validation (server/client) using @t3-oss/env-core

## Test

You can run `bun run test` to validate linting, testing, and typechecking. It's best to run this before the agent session concludes to ensure you have given the user valid code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ludicroushq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
