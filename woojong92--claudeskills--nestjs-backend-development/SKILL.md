---
name: nestjs-backend-development
description: Build scalable server-side applications with NestJS. Use when (1) creating a new NestJS app, (2) creating Modules, Controllers, and Services, (3) setting up Database integration (TypeORM), (4) implementing Authentication/Authorization (Custom JWT), or (5) setting up API Documentation (Swagger). Use when this capability is needed.
metadata:
  author: woojong92
---

# NestJS Backend Development

Build efficient, scalable Node.js server-side applications with NestJS.

## Tech Stack

- **NestJS 10+** (Core Framework)
- **TypeScript** (Language)
- **TypeORM** (Database ORM)
- **PostgreSQL** (Recommended Database)
- **JWT** (Authentication)
- **Swagger** (API Documentation)
- **Jest** (Testing)
- **Docker** (Containerization)

## Quick Start

### 1. Create Project

Generating a new project with strict mode enabled is recommended.

```bash
npm i -g @nestjs/cli
nest new my-nest-app --strict
cd my-nest-app
```

For detailed project setup and configuration (ConfigModule, ValidationPipe), see [setup-guide.md](references/setup-guide.md).

### 2. Generate Resource

Use the CLI to generate a complete CRUD resource (Module, Controller, Service, DTOs, Entities).

```bash
nest g resource users
```

For understanding the project structure and architecture, see [project-structure.md](references/project-structure.md).

### 3. Setup Database (TypeORM)

Install TypeORM and configured it in `app.module.ts`.

```bash
npm install --save @nestjs/typeorm typeorm pg
```

For complete database configuration and Entity patterns, see [database.md](references/database.md).

## Workflows

### Implementing Authentication

1. **Install JWT package**: `npm install @nestjs/jwt`
2. **Create AuthModule**: Configure `JwtModule` with secrets.
3. **Create AuthGuard**: Implement `CanActivate` to verify tokens with `JwtService`.
4. **Protect Routes**: Use `AuthGuard` on controllers/routes.

See [authentication.md](references/authentication.md) for the complete implementation guide.

### Creating a Robust API Endpoint

1. **Define DTO**: Create a class with validation decorators.
2. **Create Controller Method**: Use decorators (`@Post`, `@Body`) and types.
3. **Implement Service Logic**: Handle business logic and database interactions.
4. **Add Swagger Docs**: Use `@ApiProperty` on DTOs and `@ApiOperation` on controllers.

See [dto-validation.md](references/dto-validation.md) for validation patterns.

## Reference Files

- **[setup-guide.md](references/setup-guide.md)** - CLI installation, project creation, main.ts bootstrapping, ConfigModule.
- **[project-structure.md](references/project-structure.md)** - Modular architecture, folder structure, core components (Modules, Controllers, Services).
- **[database.md](references/database.md)** - TypeORM setup, Entity definition, Repository pattern, Transactions.
- **[authentication.md](references/authentication.md)** - Custom JWT Guard, JwtService, Auth guards, CurrentUser decorator.
- **[dto-validation.md](references/dto-validation.md)** - DTO creation, class-validator decorators, Pipes.

## Best Practices

### Architecture

- **Modularization**: specific features should be in their own modules (e.g., `AuthModule`, `UsersModule`).
- **Dependency Injection**: Always inject dependencies via constructors. Avoid hard-coding.
- **Environment Variables**: Never commit secrets. Use `@nestjs/config` and `.env` files.

### Code Quality

- **DTOs**: Always use DTOs for input data validation. Do not use raw objects.
- **Strict Mode**: Use TypeScript strict mode.
- **Async/Await**: Use async/await for all asynchronous operations (DB calls).

### API Design

- **RESTful**: Follow REST conventions (GET /users, POST /users, GET /users/:id).
- **Status Codes**: Return appropriate HTTP status codes (201 Created, 400 Bad Request, 404 Not Found).
- **Documentation**: Keep Swagger documentation up to date.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/woojong92) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
