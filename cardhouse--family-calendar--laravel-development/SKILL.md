---
name: laravel-development
description: >- Use when this capability is needed.
metadata:
  author: cardhouse
---

# Laravel Development

## Workflow

1. Follow project conventions and Laravel 12 structure.
2. Use Boost docs search for framework or package questions.
3. Prefer framework-native patterns (Eloquent, Form Requests, policies, resources).
4. Prevent N+1 queries with eager loading and `Model::preventLazyLoading()` in dev.
5. Add `declare(strict_types=1);` to all PHP files.
6. Write or update Pest tests for behavior changes.

## Database & Models

- Prefer Eloquent relationships and query scopes over raw `DB::` usage.
- Add indexes for frequently queried columns.
- Use the `casts()` method for cast definitions.
- Add cascade deletes where orphaned rows are invalid.
- Ensure migrations include full column definitions when altering columns.

## HTTP, Validation, Authorization

- Use Form Request classes for validation; include custom messages.
- Use policies/gates for authorization checks.
- Prefer named routes and the `route()` helper.

## Testing (Pest)

- Create tests with `php artisan make:test --pest`.
- Use factories for model setup and avoid hardcoded dates.
- Use `assertSuccessful()`, `assertForbidden()`, etc. instead of raw status codes.

## Performance & UX

- Avoid lazy loading in non-production.
- Eager load nested relationships and apply query limits for dashboards.

## Lessons Learned

- Append new lessons and do/don't guidance to `references/lessons.md` with a date and short context.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cardhouse) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
