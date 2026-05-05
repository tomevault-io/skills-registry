---
name: laravelrate-limiting
description: Apply per-user and per-route limits with RateLimiter and throttle middleware; use backoffs and headers for clients Use when this capability is needed.
metadata:
  author: neversight
---

# Rate Limiting and Throttle

Protect endpoints from abuse while keeping UX predictable.

## Commands

```
// App\Providers\RouteServiceProvider
RateLimiter::for('api', function (Request $request) {
    return Limit::perMinute(60)->by(optional($request->user())->id ?: $request->ip());
});

// routes/api.php
Route::middleware(['throttle:api'])->group(function () {
    // ...
});
```

## Patterns

- Scope limits by user when authenticated; fall back to IP
- Communicate limits to clients via standard headers
- Provide sensible 429 responses with retry hints
- Separate bursty endpoints into specialized limiters

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
