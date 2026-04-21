---
name: build-fix
description: Build and Fix - Incrementally fix TypeScript and build errors. Run build, parse errors, fix one at a time, re-verify. Use when this capability is needed.
metadata:
  author: kokiebisu
---

# Build and Fix

Incrementally fix TypeScript and build errors:

1. Run build: bun run build

2. Parse error output:
   - Group by file
   - Sort by severity

3. For each error:
   - Show error context (5 lines before/after)
   - Explain the issue
   - Propose fix
   - Apply fix
   - Re-run build
   - Verify error resolved

4. Stop if:
   - Fix introduces new errors
   - Same error persists after 3 attempts
   - User requests pause

5. Show summary:
   - Errors fixed
   - Errors remaining
   - New errors introduced

Fix one error at a time for safety!

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kokiebisu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
