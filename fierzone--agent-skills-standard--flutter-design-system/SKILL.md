---
name: flutter-design-system
description: Enforce strict Design Language System (DLS) adherence. Prevents hardcoded colors, spacing, and typography. Detects and uses project tokens. Use when this capability is needed.
metadata:
  author: fierzone
---

# Flutter Design System Enforcement

## **Priority: P0 (CRITICAL)**

Zero tolerance for hardcoded design values.

## Guidelines

- **Colors**: Use tokens (`VColors.*`, `AppColors.*`), never `Color(0xFF...)` or `Colors.red`.
- **Spacing**: Use tokens (`VSpacing.*`), never magic numbers like `16` or `24`.
- **Typography**: Use tokens (`VTypography.*`, `textTheme.*`), never inline `TextStyle`.
- **Borders**: Use tokens (`VBorders.*`), never raw `BorderRadius.`
- **Components**: Use DLS widgets (`VButton`) over raw Material widgets (`ElevatedButton`) if available.

[Detailed Examples](references/usage.md)

## Anti-Patterns

- **No Hex Colors**: `Color(0xFF...)` is strictly forbidden.
- **No Color Enums**: `Colors.blue` is forbidden in UI code.
- **No Magic Spacing**: `SizedBox(height: 10)` is forbidden.
- **No Inline Styles**: `TextStyle(fontSize: 14)` is forbidden.
- **No Raw Widgets**: Don't use `ElevatedButton` when `VButton` exists.

## Related Topics

mobile-ux-core | flutter/widgets | idiomatic-flutter

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fierzone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
