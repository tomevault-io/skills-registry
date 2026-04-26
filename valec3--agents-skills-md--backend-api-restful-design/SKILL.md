---
name: backend-api-restful-design
description: REST principles, resource naming, HTTP methods. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend API RESTful Design

## When to use this skill
- Designing APIs
- Creating endpoints
- Following REST standards
- API consistency

## Workflow
- [ ] Use nouns for resources
- [ ] Use HTTP methods correctly
- [ ] Return proper status codes
- [ ] Version APIs
- [ ] Use plural nouns

## Instructions

### REST Principles
```
GET    /api/users      - List users
GET    /api/users/1    - Get user
POST   /api/users      - Create user
PUT    /api/users/1    - Update user
PATCH  /api/users/1    - Partial update
DELETE /api/users/1    - Delete user
```

### Nested Resources
```
GET    /api/users/1/orders        - User's orders
POST   /api/users/1/orders        - Create order for user
GET    /api/orders/123            - Single order (flat)
```

### Status Codes
```php
<?php

return $this->respond($data, 200);        // OK
return $this->respondCreated($data);      // 201
return $this->respondNoContent();         // 204
return $this->failValidationErrors($e);   // 400
return $this->failUnauthorized();         // 401
return $this->failForbidden();            // 403
return $this->failNotFound();             // 404
return $this->failServerError($message);  // 500
```

## Resources
- Use nouns, not verbs
- HTTP methods for actions
- Proper status codes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
