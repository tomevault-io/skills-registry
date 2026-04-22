---
name: symfony-skfront-service
description: Create Bll services for front/back. Use for business logic that calls API. Use when this capability is needed.
metadata:
  author: swoking
---

# Front Service (Bll) Skill

## Mission

Create Business Logic Layer services that call API services.

---

## Location

- Front: `front/src/Service/Bll/<Name>Service.php`
- Back: `back/src/Service/Bll/<Name>Service.php`

---

## Template

```php
<?php

namespace App\Service\Bll;

use App\Service\Api\Api<Feature>;
use StarterKit\Model\ApiResult;
use StarterKit\Service\Bll\HelperService;

class <Feature>Service
{
    public function __construct(
        private readonly HelperService $helper,
        private readonly Api<Feature>  $api,
    ) {
        $this->helper->logDebug("[App] - Service - <Feature>Service - Load");
    }

    public function read(string $key): ApiResult
    {
        $this->helper->logDebug("[App] - Service - <Feature>Service - Read");

        return $this->api->read($this->helper->getLanguage(), $key);
    }

    public function update(string $key, array $data): ApiResult
    {
        $this->helper->logDebug("[App] - Service - <Feature>Service - Update");

        return $this->api->update($this->helper->getLanguage(), $key, $data);
    }
}
```

---

## Key Rules

1. **Constructor injection** (unlike Route controllers)
2. **Inject `HelperService`** for language and logging
3. **Use `$this->helper->getLanguage()`** for API calls
4. **Return `ApiResult`**
5. **Log method calls**

---

## Checklist

- [ ] `HelperService` injected
- [ ] Api service(s) injected
- [ ] Methods use `$this->helper->getLanguage()`
- [ ] Debug logging at method entry
- [ ] Returns `ApiResult`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/swoking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
