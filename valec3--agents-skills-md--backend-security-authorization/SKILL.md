---
name: backend-security-authorization
description: RBAC, permissions, access control. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend Security Authorization

## When to use this skill
- Role-based access control
- Permission systems
- Resource authorization
- Policy enforcement

## Workflow
- [ ] Define roles and permissions
- [ ] Check permissions before actions
- [ ] Use middleware for routes
- [ ] Implement policies
- [ ] Deny by default

## Instructions

### RBAC Structure
```php
<?php

enum Role: string
{
    case ADMIN = 'admin';
    case USER = 'user';
    case GUEST = 'guest';
}

class User extends Entity
{
    public function hasRole(Role $role): bool
    {
        return $this->role === $role->value;
    }

    public function can(string $permission): bool
    {
        return in_array($permission, $this->permissions ?? []);
    }
}
```

### Authorization Filter
```php
<?php

class PermissionFilter implements FilterInterface
{
    public function before(RequestInterface $request, $arguments = null)
    {
        $permission = $arguments[0] ?? null;
        $user = auth()->user();

        if (!$user || !$user->can($permission)) {
            return Services::response()
                ->setJSON(['error' => 'Forbidden'])
                ->setStatusCode(403);
        }
    }
}
```

## Resources
- Check permissions, not roles
- Use filters for route protection
- Deny by default

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
