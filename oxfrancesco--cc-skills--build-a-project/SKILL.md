---
name: build-a-project
description: Create or update a Makefile with dev and build targets that run the repo's actual development and build commands. Use when asked to standardize run/build commands or add Makefile targets. Use when this capability is needed.
metadata:
  author: oxfrancesco
---

# Build and Run Makefile

Create a Makefile with `dev` and `build` targets that match the project's real commands. The Makefile must use the repo's package manager and scripts.

## Determine the package manager

1. Read `package.json` and check the `packageManager` field. Use it if present.
2. If missing, infer from lockfiles (prefer the most specific signal):
   - `bun.lockb` or `bun.lock`
   - `pnpm-lock.yaml`
   - `yarn.lock`
   - `package-lock.json`
3. If multiple signals exist or none are found, ask the user before proceeding.

## Determine dev and build commands

1. Read `package.json` scripts in the project root that owns the app (monorepos usually define root scripts that proxy to packages).
2. Use `dev` for development and `build` for builds when available.
3. If a script is missing, search for a clear alternative (`start`, `serve`, `preview`, or framework docs) and confirm with the user when ambiguous.
4. Form the command using the package manager:
   - Bun: `bun run <script>`
   - Pnpm: `pnpm <script>`
   - Yarn: `yarn <script>`
   - Npm: `npm run <script>`

## Create or update Makefile

- Create or update `Makefile` in the repo root.
- Add `.PHONY: dev build`.
- `dev` runs the resolved dev command.
- `build` runs the resolved build command.
- Do not leave placeholders; write the final commands.
- Preserve any existing targets not related to `dev` or `build`.

## Stop and ask when needed

If the package manager or commands cannot be determined confidently, stop and ask the user for clarification before editing files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxfrancesco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
