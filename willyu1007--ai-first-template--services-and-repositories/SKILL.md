---
name: services-and-repositories
description: Service and repository patterns, dependency boundaries, and testability. Keywords: services, repositories, business logic, data access, dependency injection. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Services and Repositories

This skill describes how to structure business logic (services) and data access (repositories).

---

## 1. Service layer (business rules)

Services should:
- Enforce business rules
- Orchestrate multiple repositories
- Avoid HTTP-specific types and concerns

Example:

```ts
export class UserService {
  constructor(private readonly userRepo: UserRepository) {}

  async create(input: CreateUserInput): Promise<User> {
    const existing = await this.userRepo.findByEmail(input.email);
    if (existing) throw new ConflictError("Email already exists");
    return await this.userRepo.create(input);
  }
}
```

---

## 2. Repository layer (data access)

Repositories should:
- Encapsulate query construction
- Hide ORM details from services
- Provide transaction helpers where needed

```ts
export class UserRepository {
  constructor(private readonly db: PrismaClient) {}

  findByEmail(email: string) {
    return this.db.user.findUnique({ where: { email } });
  }

  create(input: CreateUserInput) {
    return this.db.user.create({ data: input });
  }
}
```

---

## 3. When to introduce repositories

Use repositories when:
- Queries are non-trivial (joins, pagination, filtering)
- Multiple call sites reuse the same query logic
- You need better test seams and isolation

If a service is a thin wrapper over a single simple query, a repository may be optional.

---

## Related Skills

- `database-patterns`
- `testing-guide`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
