---
name: laravelhttp-client-resilience
description: Use the HTTP client with sensible timeouts, retries, and backoff; capture context and handle failures explicitly Use when this capability is needed.
metadata:
  author: neversight
---

# HTTP Client Resilience

Design outbound calls to be predictable and observable.

## Commands

```
use Illuminate\Support\Facades\Http;

$res = Http::baseUrl(config('services.foo.url'))
    ->timeout(5)
    ->retry(3, 200, throw: false)
    ->withHeaders(['Accept' => 'application/json'])
    ->get('/v1/things', ['q' => 'bar']);

if (!$res->successful()) {
    Log::warning('foo api failure', [
        'status' => $res->status(),
        'body' => substr($res->body(), 0, 500),
    ]);
}
```

## Patterns

- Set timeouts explicitly (client and server defaults differ)
- Use limited retries with backoff for transient failures only
- Prefer dependency injection for testability
- Add request/response context to logs (redact sensitive data)
- Use `pool()` for concurrency when calling multiple endpoints

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
