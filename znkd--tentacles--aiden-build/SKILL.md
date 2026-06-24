---
name: aiden-build
description: Build the Aiden Tauri project with required chmod and codesign steps. Use when asked to build Aiden, run Tauri build/dev, or prepare Apple-darwin binaries for Aiden before a build. Use when this capability is needed.
metadata:
  author: znkd
---

# Aiden Build

## Workflow

1. Ensure you are in the Aiden project root (the folder that contains `src-tauri/`).
2. Run one of these script commands:
   - `npx tsx skills/aiden-build/scripts/aiden-build.ts` (prep + prompt for build/dev)
   - `npx tsx skills/aiden-build/scripts/aiden-build.ts prep`
   - `npx tsx skills/aiden-build/scripts/aiden-build.ts build`
   - `npx tsx skills/aiden-build/scripts/aiden-build.ts dev`
   - `npx tsx skills/aiden-build/scripts/aiden-build.ts --uploadDMG`

## Notes

- If the binaries are missing, ask the user whether to proceed and how to obtain them.
- If codesign fails, capture the exact error and ask how to proceed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/znkd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
