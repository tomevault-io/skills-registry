---
name: node-typescript-service
description: Builds and extends Node.js/TypeScript microservice endpoints with request validation (Zod/Joi/class-validator), structured error handling, and unit + integration tests. Use when the user wants to create or modify an API endpoint, service layer, or route handler in Express, Nest.js, or Fastify. Do not use for React frontend components, UI elements, or accessibility audits.
metadata:
  author: vanessamarely
---

# Skill: Node.js/TypeScript Service

## Purpose

Create or modify Node.js/TypeScript backend services with proper validation, error handling, logging, and testing.

## Inputs

- `serviceName`: Service or module identifier
- `endpointSpec`: HTTP method, path, request schema, response schema
- `businessLogic`: Description of what the endpoint does
- `framework`: Express, Nest, Fastify (detected from project)

## Outputs

- Route handler or controller file
- Validation schemas
- Error handling implementation
- Test file with unit and integration tests

## Procedures

### 1. Detect Project Framework

Check `package.json` dependencies:
- `express` → Express.js
- `@nestjs/core` → Nest.js
- `fastify` → Fastify

If unclear, request clarification.

### 2. Load Backend Policy

Read: `.github/copilot-instructions/backend-policy.md`

Follow Node.js/TypeScript conventions.

### 3. Define Request Validation Schema

Create validation schema using the project's validation library.

Common libraries:
- Zod
- Joi
- class-validator (Nest.js)

Example (Zod):
```tsx
import { z } from 'zod'

const CreateUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(1),
  age: z.number().int().positive().optional()
})

type CreateUserRequest = z.infer<typeof CreateUserSchema>
```

Refer to: `references/validation-and-errors.md`

### 4. Implement Route Handler

Structure varies by framework.

#### Express.js

```tsx
import express, { Request, Response } from 'express'

router.post('/users', async (req: Request, res: Response) => {
  try {
    const data = CreateUserSchema.parse(req.body)
    const user = await createUser(data)
    res.status(201).json({ success: true, data: user })
  } catch (error) {
    handleError(error, res)
  }
})
```

#### Nest.js

```tsx
import { Controller, Post, Body } from '@nestjs/common'
import { CreateUserDto } from './dto/create-user.dto'

@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Post()
  async create(@Body() createUserDto: CreateUserDto) {
    return this.usersService.create(createUserDto)
  }
}
```

### 5. Implement Error Handling

Map errors to appropriate HTTP status codes:
- 400: Validation error, malformed request
- 401: Authentication required
- 403: Insufficient permissions
- 404: Resource not found
- 409: Conflict (duplicate resource)
- 500: Internal server error

Return structured error responses:
```tsx
{
  success: false,
  error: {
    code: 'VALIDATION_ERROR',
    message: 'Invalid input data',
    details: [...]
  }
}
```

Refer to: `references/validation-and-errors.md`

### 6. Add Logging

Use structured logging (not `console.log`):
- Winston
- Pino
- Framework-specific loggers

```tsx
logger.info('User created', { userId: user.id })
logger.error('Database connection failed', { error })
```

### 7. Implement Business Logic

Extract business logic to service layer (not in route handler):

```tsx
class UsersService {
  async create(data: CreateUserRequest): Promise<User> {
    const existing = await this.findByEmail(data.email)
    if (existing) {
      throw new ConflictError('Email already registered')
    }
    return this.repository.save(data)
  }
}
```

### 8. Generate Tests

Create test file adjacent to handler:

#### Unit Tests

Test business logic in isolation:
```tsx
describe('UsersService', () => {
  it('should create a user with valid data', async () => {
    const data = { email: 'test@example.com', name: 'Test' }
    const user = await service.create(data)
    expect(user).toBeDefined()
    expect(user.email).toBe(data.email)
  })

  it('should throw ConflictError for duplicate email', async () => {
    await service.create({ email: 'test@example.com', name: 'Test' })
    await expect(
      service.create({ email: 'test@example.com', name: 'Test2' })
    ).rejects.toThrow(ConflictError)
  })
})
```

#### Integration Tests

Test full HTTP request/response:
```tsx
import request from 'supertest'

describe('POST /users', () => {
  it('should create user and return 201', async () => {
    const response = await request(app)
      .post('/users')
      .send({ email: 'test@example.com', name: 'Test' })
      .expect(201)

    expect(response.body.success).toBe(true)
    expect(response.body.data.email).toBe('test@example.com')
  })

  it('should return 400 for invalid email', async () => {
    const response = await request(app)
      .post('/users')
      .send({ email: 'invalid', name: 'Test' })
      .expect(400)

    expect(response.body.success).toBe(false)
  })
})
```

### 9. Update Routing

Register the new endpoint in the application's routing configuration.

Verify no route conflicts exist.

### 10. Validate Implementation

Check:
- Request validation is applied
- Error handling is comprehensive
- Responses follow consistent structure
- Logging captures relevant context
- Tests cover success and error cases

Suggest verification commands:
- `npm run lint`
- `npm test`
- `npm run build`

## Error Handling

**Missing validation library**: Suggest installing Zod, Joi, or class-validator.

**Framework mismatch**: Clarify the detected framework and adjust implementation.

**Route conflict**: Notify and suggest alternative path or method.

**Type safety issue**: Ensure types are explicit and validated.

## References

- Validation and Errors: `references/validation-and-errors.md`
- Service Specification Template: `assets/service-spec.template.md`

---
> Source: [vanessamarely/ai-playbook-reposito](https://github.com/vanessamarely/ai-playbook-reposito) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
