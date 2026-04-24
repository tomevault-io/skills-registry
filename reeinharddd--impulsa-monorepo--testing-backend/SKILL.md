---
name: testing-backend
description: Backend testing standards (NestJS, Jest). Use when writing unit or integration tests for API. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# Backend Testing Skill

> **Purpose:** Ensure API reliability via Unit and Integration tests using Jest.

## File Naming

- **Unit:** `[name].spec.ts` (alongside source).
- **Integration:** `test/[feature].e2e-spec.ts`.

## Coverage Targets

- **Services:** 80%
- **Controllers:** 70%
- **Critical Path:** 95%

## Mocking Prisma

Use `jest-mock-extended`.

```typescript
import { mockDeep } from "jest-mock-extended";
import { PrismaClient } from "@prisma/client";

const prisma = mockDeep<PrismaClient>();
prisma.model.findMany.mockResolvedValue([]);
```

## AAA Pattern

```typescript
it("should create resource", async () => {
  // Arrange
  const dto = { name: "Test" };
  prisma.resource.create.mockResolvedValue(result);

  // Act
  const res = await service.create(dto);

  // Assert
  expect(res).toEqual(result);
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
