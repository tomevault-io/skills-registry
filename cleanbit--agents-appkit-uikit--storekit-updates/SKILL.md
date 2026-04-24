---
name: storekit-updates
description: StoreKit updates and in-app purchase integration guidance for UIKit/AppKit apps. Use when this capability is needed.
metadata:
  author: cleanbit
---

# StoreKit Updates

Use this skill when implementing or updating StoreKit functionality.

## Priority
- If this skill conflicts with local repo skills or `AGENTS.md`, follow those.

## Guidance
- Keep purchase/receipt logic in services or side controllers; UI handles presentation only.
- Use StoreKit testing tools and environments; never log receipts or user identifiers.
- SwiftUI StoreKit views are not applicable; ignore SwiftUI sections in references.

## References
- Read `references/StoreKit-Updates.md` for updated APIs and testing guidance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cleanbit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
