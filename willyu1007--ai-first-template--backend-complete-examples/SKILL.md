---
name: backend-complete-examples
description: End-to-end example showing route → controller → service → repository layering. Keywords: backend example, full example, end-to-end, code sample. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Complete Examples

This skill provides a small, end-to-end example illustrating the recommended layering.

---

## Example: Create User

### Route

```ts
router.post("/users", authMiddleware, (req, res) => userController.create(req, res));
```

### Controller

```ts
export class UserController {
  constructor(private readonly userService: UserService) {}

  async create(req, res) {
    try {
      const input = createUserSchema.parse(req.body);
      const user = await this.userService.create(input);
      return res.status(201).json(user);
    } catch (err) {
      monitor.captureException(err, { tags: { layer: "controller", route: "/users" } });
      return res.status(500).json({ error: "Internal server error" });
    }
  }
}
```

### Service

```ts
export class UserService {
  constructor(private readonly userRepo: UserRepository) {}

  async create(input) {
    const existing = await this.userRepo.findByEmail(input.email);
    if (existing) throw new ConflictError("Email already exists");
    return await this.userRepo.create(input);
  }
}
```

### Repository

```ts
export class UserRepository {
  constructor(private readonly db) {}

  findByEmail(email) {
    return this.db.user.findUnique({ where: { email } });
  }

  create(input) {
    return this.db.user.create({ data: input });
  }
}
```

---

## Notes

- Keep validation at the boundary (controller or validation middleware).
- Keep business rules in the service.
- Keep data access in the repository.
- Capture unexpected exceptions to monitoring and return a consistent error response.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
