---
name: backend-testing-integration
description: Integration tests, database testing. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend Testing Integration

## When to use this skill
- Testing with database
- API endpoint testing
- Feature testing
- End-to-end flows

## Workflow
- [ ] Use test database
- [ ] Seed data
- [ ] Test full flows
- [ ] Reset database
- [ ] Test API responses

## Instructions

### Database Test
```php
<?php

namespace Tests\Integration;

use CodeIgniter\Test\CIDatabaseTestCase;

class UserModelTest extends CIDatabaseTestCase
{
    protected $seed = 'TestSeeder';
    protected $basePath = 'tests/database';

    public function testFindUserByEmail()
    {
        $model = new UserModel();
        $user = $model->where('email', 'test@example.com')->first();

        $this->assertNotNull($user);
        $this->assertEquals('test@example.com', $user->email);
    }
}
```

### Feature Test
```php
<?php

$result = $this->withHeaders(['Authorization' => 'Bearer ' . $token])
    ->post('/api/users', ['name' => 'John'])
    ->json();

$this->assertEquals(201, $result->getStatusCode());
```

## Resources
- Use separate test database
- Seed fixtures
- Test full request/response

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
