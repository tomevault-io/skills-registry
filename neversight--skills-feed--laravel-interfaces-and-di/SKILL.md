---
name: laravel-interfaces-and-di
description: Use interfaces and dependency injection to decouple code; bind implementations in the container Use when this capability is needed.
metadata:
  author: neversight
---

# Interfaces and Dependency Injection

Define narrow interfaces and inject them where needed. Bind concrete implementations in a service provider.

```php
interface Slugger { public function slug(string $s): string; }

final class AsciiSlugger implements Slugger {
  public function slug(string $s): string { /* ... */ }
}

$this->app->bind(Slugger::class, AsciiSlugger::class);
```

Benefits: easier testing (mock interfaces), clearer contracts, swap implementations without touching consumers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
