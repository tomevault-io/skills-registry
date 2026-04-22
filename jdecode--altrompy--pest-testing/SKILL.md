---
name: pest-testing
description: Tests applications using Pest 4 PHP framework
metadata:
  author: jdecode
---

# Pest 4
**Apply when**: Creating/modifying tests, debugging, TDD.

## Documentation
**Docs**: Use `search-docs` for Pest 4 syntax.

## Usage
- **Create**: `php artisan make:test --pest Name`
- **Run**: `php artisan test --compact`
- **Filter**: `php artisan test --filter=testName`

## Syntax
`it('does something', function () { expect(true)->toBeTrue(); });`

## Features
- **Arch**: `arch('controllers')->expect('App\Http\Controllers')->toExtendNothing();`
- **Describe**: `describe('feature', function() { ... });`

## Best Practices
- **Mocking**: Use `Event::fake()`, `Queue::fake()`, `Notification::fake()` to speed up tests.
- **Database**: Use `use RefreshDatabase;` trait.
- **Expectations**: Use specific expectations (`expect($response)->toBeRedirect()`) rather than generic ones.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jdecode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
