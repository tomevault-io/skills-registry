---
name: backend-architecture-repository-pattern
description: Data access abstraction, repository pattern. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend Architecture Repository Pattern

## When to use this skill
- Abstracting data access
- Multiple data sources
- Testability
- Complex queries

## Workflow
- [ ] Create repository interface
- [ ] Implement repository
- [ ] Inject into services
- [ ] Abstract query logic
- [ ] Keep repositories focused

## Instructions

### Interface
```php
<?php

namespace App\Repositories;

interface UserRepositoryInterface
{
    public function find(int $id): ?User;
    public function findByEmail(string $email): ?User;
    public function save(User $user): User;
}
```

### Implementation
```php
<?php

namespace App\Repositories;

class UserRepository implements UserRepositoryInterface
{
    public function __construct(
        private UserModel $model
    ) {}

    public function find(int $id): ?User
    {
        return $this->model->find($id);
    }

    public function findByEmail(string $email): ?User
    {
        return $this->model->where('email', $email)->first();
    }
}
```

## Resources
- Abstract data access
- Easy to mock for testing
- Switch data sources easily

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
