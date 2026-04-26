---
name: common-patterns
description: Common UI patterns (forms, dialogs, notifications, and tables). Keywords: forms, dialogs, modals, notifications, tables, ui patterns. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Common Patterns

This skill lists common UI patterns and recommended defaults.

---

## Forms

- Validate at the boundary (schema-first where possible).
- Display field-level errors and a single top-level error summary for submission failures.

---

## Dialogs / modals

- Trap focus inside the dialog.
- Ensure ESC closes the dialog (unless forbidden for safety reasons).
- Restore focus to the triggering element on close.

---

## Notifications

- Prefer a single notification mechanism (snackbar/toast).
- Use success messages sparingly; highlight failures and required actions.

---

## Tables / grids

- Prefer server-side pagination for large datasets.
- Keep sorting/filtering state in the URL for shareability (when appropriate).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
