---
name: backend-php-modern-features
description: Modern PHP 8+ features including enums, attributes, readonly properties, and union types. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend PHP Modern Features

## When to use this skill
- Using PHP 8.0+
- Modernizing codebase
- Type-safe code
- Leveraging new language features

## Workflow
- [ ] Use enums for fixed sets of values
- [ ] Apply attributes for metadata
- [ ] Use readonly for immutable properties
- [ ] Leverage union types
- [ ] Use named arguments

## Instructions

### Enums (PHP 8.1+)
```php
<?php

enum UserStatus: string
{
    case ACTIVE = 'active';
    case INACTIVE = 'inactive';
    case BANNED = 'banned';

    public function label(): string
    {
        return match($this) {
            self::ACTIVE => 'Active User',
            self::INACTIVE => 'Inactive',
            self::BANNED => 'Banned User'
        };
    }
}

// Usage
$user->status = UserStatus::ACTIVE;
```

### Readonly Properties (PHP 8.1+)
```php
<?php

class User
{
    public function __construct(
        public readonly int $id,
        public readonly string $email,
        public string $name
    ) {}
}

$user = new User(1, 'user@example.com', 'John');
// $user->id = 2; // Error: Cannot modify readonly property
```

### Union Types (PHP 8.0+)
```php
<?php

function processValue(int|float|string $value): int|float
{
    return is_string($value) ? (int)$value : $value;
}
```

### Named Arguments (PHP 8.0+)
```php
<?php

function createUser(
    string $name,
    string $email,
    bool $isActive = true
): User {
    // ...
}

// Usage
$user = createUser(
    email: 'user@example.com',
    name: 'John Doe'
);
```

### Attributes (PHP 8.0+)
```php
<?php

#[Attribute]
class Route
{
    public function __construct(
        public string $path,
        public string $method = 'GET'
    ) {}
}

class UserController
{
    #[Route('/api/users', 'GET')]
    public function index() {}
}
```

### Match Expression (PHP 8.0+)
```php
<?php

$message = match($status) {
    UserStatus::ACTIVE => 'User is active',
    UserStatus::INACTIVE => 'User is inactive',
    UserStatus::BANNED => 'User is banned',
    default => 'Unknown status'
};
```

## Resources
- Use enums instead of constants
- Readonly for immutable data
- Match is stricter than switch

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
