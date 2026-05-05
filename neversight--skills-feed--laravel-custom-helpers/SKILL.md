---
name: laravel-custom-helpers
description: Create and register small, pure helper functions when they improve clarity; keep them organized and tested Use when this capability is needed.
metadata:
  author: neversight
---

# Custom Helpers

## Create a helper file

```php
// app/Support/helpers.php
function money(int $cents): string { return number_format($cents / 100, 2); }
```

## Autoload

Add to `composer.json`:

```json
{
  "autoload": { "files": ["app/Support/helpers.php"] }
}
```

Run `composer dump-autoload`.

## Guidelines

- Keep helpers small and pure; avoid hidden IO/state
- Prefer static methods on value objects when domain-specific

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
