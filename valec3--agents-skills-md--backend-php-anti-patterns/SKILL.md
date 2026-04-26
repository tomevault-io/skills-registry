---
name: backend-php-anti-patterns
description: Common PHP mistakes and anti-patterns to avoid for cleaner, more maintainable code. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend PHP Anti Patterns

## When to use this skill
- Code reviews
- Refactoring legacy code
- Identifying problematic patterns
- Training developers

## Workflow
- [ ] Identify anti-patterns in code
- [ ] Refactor to better solutions
- [ ] Document why it's an anti-pattern
- [ ] Prevent in code reviews
- [ ] Use linters to catch automatically

## Instructions

### Anti-Pattern 1: God Objects
```php
<?php

// ❌ Bad: Class does everything
class User
{
    public function save() {}
    public function sendEmail() {}
    public function generatePDF() {}
    public function processPayment() {}
    public function logActivity() {}
}

// ✅ Good: Single Responsibility
class User
{
    // Only user data and basic operations
}

class UserRepository
{
    public function save(User $user) {}
}

class EmailService
{
    public function sendWelcomeEmail(User $user) {}
}
```

### Anti-Pattern 2: Using Global State
```php
<?php

// ❌ Bad: Global variables
global $db;
$users = $db->query('SELECT * FROM users');

// ✅ Good: Dependency injection
class UserController
{
    public function __construct(
        private readonly Database $db
    ) {}

    public function index()
    {
        $users = $this->db->query('SELECT * FROM users');
    }
}
```

### Anti-Pattern 3: Silent Failures
```php
<?php

// ❌ Bad: Swallowing exceptions
try {
    $user = $this->getUser($id);
} catch (\Exception $e) {
    // Do nothing
}

// ✅ Good: Handle or propagate
try {
    $user = $this->getUser($id);
} catch (NotFoundException $e) {
    return $this->respond(['error' => 'User not found'], 404);
} catch (\Exception $e) {
    log_message('error', $e->getMessage());
    throw $e;
}
```

### Anti-Pattern 4: Magic Numbers/Strings
```php
<?php

// ❌ Bad
if ($user->status === 1) {
    // What does 1 mean?
}

// ✅ Good: Use enums or constants
enum UserStatus: int
{
    case ACTIVE = 1;
    case INACTIVE = 2;
}

if ($user->status === UserStatus::ACTIVE) {
    // Clear intent
}
```

### Anti-Pattern 5: Massive Arrays
```php
<?php

// ❌ Bad: Unclear structure
$user = [
    'id' => 1,
    'name' => 'John',
    'data' => ['email' => 'john@example.com']
];

// ✅ Good: Use DTOs or entities
class User
{
    public function __construct(
        public readonly int $id,
        public readonly string $name,
        public readonly string $email
    ) {}
}
```

### Anti-Pattern 6: Type Coercion Abuse
```php
<?php

// ❌ Bad: Relying on loose comparison
if ($value == true) {}  // '1', 'yes', [1] all match
if ($count == 0) {}     // '', null, false, [] all match

// ✅ Good: Strict comparison
if ($value === true) {}
if ($count === 0) {}
```

### Anti-Pattern 7: Not Using Exceptions
```php
<?php

// ❌ Bad: Returning error codes
function getUser(int $id)
{
    $user = $this->db->find($id);
    return $user ?: false;
}

// ✅ Good: Throw exceptions
function getUser(int $id): User
{
    $user = $this->db->find($id);
    if (!$user) {
        throw new NotFoundException('User not found');
    }
    return $user;
}
```

## Resources
- Use SRP (Single Responsibility)
- Avoid global state
- Handle errors explicitly
- Use value objects over arrays
- Enable strict_types

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
