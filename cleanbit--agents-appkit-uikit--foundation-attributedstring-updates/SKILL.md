---
name: foundation-attributedstring-updates
description: Foundation AttributedString updates and UIKit/AppKit integration guidance. Use when this capability is needed.
metadata:
  author: cleanbit
---

# Foundation AttributedString Updates

Use this skill when working with `AttributedString` or migrating styled text.

## Priority
- If this skill conflicts with local repo skills or `AGENTS.md`, follow those.

## Guidance
- Prefer `AttributedString` for new styled text; bridge to `NSAttributedString` only at UIKit/AppKit boundaries.
- Keep formatting logic in Core or side controllers; UI should only render results.
- Use locale-aware formatting from `formatting-foundation`.
- SwiftUI is forbidden; ignore any SwiftUI guidance in references.

## References
- Read `references/Foundation-AttributedString-Updates.md` for API updates and examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cleanbit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
