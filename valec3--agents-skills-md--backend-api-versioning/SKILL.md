---
name: backend-api-versioning
description: API versioning strategies, backward compatibility. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend API Versioning

## When to use this skill
- Breaking changes
- Multiple API versions
- Backward compatibility
- Migration planning

## Workflow
- [ ] Choose versioning strategy
- [ ] Version early
- [ ] Maintain old versions
- [ ] Deprecate gracefully
- [ ] Document changes

## Instructions

### URL Versioning
```php
<?php

// Config/Routes.php
$routes->group('api/v1', ['namespace' => 'App\Controllers\API\V1'], function($routes) {
    $routes->resource('users');
});

$routes->group('api/v2', ['namespace' => 'App\Controllers\API\V2'], function($routes) {
    $routes->resource('users');
});
```

### Header Versioning
```php
<?php

$version = $this->request->getHeaderLine('Accept-Version') ?: 'v1';
```

### Deprecation
```php
<?php

return $this->respond($data)
    ->setHeader('X-API-Deprecated', 'This endpoint will be removed in v3')
    ->setHeader('X-API-Sunset', '2024-12-31');
```

## Resources
- Version from day 1
- Maintain 2-3 versions max
- Communicate deprecations early

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
