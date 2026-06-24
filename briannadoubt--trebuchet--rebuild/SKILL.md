---
name: rebuild
description: Rebuild the trebuchet CLI binary in release configuration Use when this capability is needed.
metadata:
  author: briannadoubt
---

Rebuild the trebuchet CLI binary:

1. Navigate to the Trebuchet project directory
2. Run `swift build --product trebuchet --configuration release`
3. Confirm the build succeeded
4. Report the binary location: `/Users/bri/dev/Trebuchet/.build/release/trebuchet`

If the build fails, show the error output and suggest fixes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/briannadoubt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
