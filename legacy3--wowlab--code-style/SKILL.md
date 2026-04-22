---
name: code-style
description: Enforce code style and formatting preferences. Use when writing or reviewing code to ensure consistent style. Use when this capability is needed.
metadata:
  author: legacy3
---

# Code Style

Project code style and formatting rules.

## Control Flow

**Always use brackets** for control flow statements. Never single-line without braces.

### Wrong

```ts
if (condition) return early;

if (x) doSomething();
else doOther();

for (const item of items) process(item);
```

### Right

```ts
if (condition) {
  return early;
}

if (x) {
  doSomething();
} else {
  doOther();
}

for (const item of items) {
  process(item);
}
```

## DRY - Don't Repeat Yourself

**Always check for existing utilities before creating new ones.**

### Before Writing New Code

1. Search `src/hooks/` for existing React hooks
2. Search `src/lib/` for utility functions and modules
3. Search `src/lib/state/` for existing query/store hooks
4. Look for similar patterns in the codebase

### If Similar Code Exists

- Extend the existing utility
- Extract common logic into shared function
- Don't duplicate - refactor

## No Backwards Compatibility

**Prefer breaking and remaking over maintaining legacy support.**

### Do

- Delete obsolete code entirely
- Rename things to be correct, update all usages
- Remove deprecated APIs immediately
- Refactor aggressively when improving

### Don't

- Keep old function signatures "for compatibility"
- Add `// @deprecated` and leave code around
- Maintain multiple ways to do the same thing
- Add shims or adapters for old patterns
- Comment out code "in case we need it"

## No Legacy Code

**Remove, don't preserve.**

### Signs of Legacy Code to Remove

- Commented-out code blocks
- Functions with `_old`, `_legacy`, `_deprecated` suffixes
- `// TODO: remove after migration`
- Unused exports
- Dead code paths
- Backwards-compat shims

### When Refactoring

1. Delete the old implementation
2. Write the new implementation
3. Update all call sites
4. Don't provide migration period

## Code Cleanup Rules

- Delete unused imports immediately
- Remove empty files
- Delete unused variables (not just prefix with `_`)
- Remove console.log statements
- Clean up TODO comments by doing them or removing them

## Formatting - Use Intl APIs

**Use `Intl.NumberFormat` and `Intl.DateTimeFormat` for locale-aware formatting.**

### Wrong

```tsx
{
  value.toFixed(2);
}
{
  `${duration}ms`;
}
{
  (percent * 100).toFixed(1) + "%";
}
```

### Right

```tsx
// Get locale from next-intlayer
const { locale } = useLocale();

// Numbers with locale
new Intl.NumberFormat(locale).format(value);

// Compact notation (1.2M)
new Intl.NumberFormat(locale, { notation: "compact" }).format(value);

// Percentages
new Intl.NumberFormat(locale, { style: "percent" }).format(ratio);

// Relative time
new Intl.RelativeTimeFormat(locale, { numeric: "auto" }).format(days, "day");
```

## Instructions

When writing or reviewing code:

1. Check control flow has brackets
2. Search for existing utilities before creating new ones
3. Delete obsolete code, don't deprecate
4. Remove legacy patterns, don't maintain them
5. Keep codebase lean - when in doubt, delete
6. Use `Intl` APIs for locale-aware number/date/duration formatting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/legacy3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
