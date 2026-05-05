---
name: laravelcontroller-tests
description: Write focused controller tests using HTTP assertions; keep heavy logic in Actions/Services and unit test them Use when this capability is needed.
metadata:
  author: neversight
---

# Controller Tests

## Feature tests for endpoints

```php
it('rejects empty email', function () {
  $this->post('/register', ['email' => ''])->assertSessionHasErrors('email');
});
```

## Better tests

- Move validation to Form Requests; assert errors from the request class
- Extract business logic into Actions; unit test them directly
- Use factories for realistic data; avoid heavy mocking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
