---
name: nestjs-swagger
description: Sets up Swagger (OpenAPI) documentation and global validation pipes (class-validator/transformer) for NestJS.
metadata:
  author: erixcel
---

# NestJS Swagger & Validation Setup

This skill provides the standard configuration for API documentation and input validation.

## 1. Install Dependencies

```bash
npm install @nestjs/swagger class-transformer class-validator
```

## 2. Core Configuration

Create these files in `src/core/` to modularize the setup.

### Swagger Configuration

**File**: `src/core/swagger.core.ts`

```typescript
import { INestApplication } from "@nestjs/common";
import { DocumentBuilder, SwaggerModule } from "@nestjs/swagger";

export function setupSwagger(app: INestApplication) {
  const config = new DocumentBuilder()
    .setTitle("API Documentation")
    .setDescription("API endpoints for backend-cachorros")
    .setVersion("1.0")
    .addBearerAuth()
    .build();

  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup("api", app, document);
}
```

### Global Validation Pipe

**File**: `src/core/transformer.core.ts`

```typescript
import { INestApplication, ValidationPipe } from "@nestjs/common";

export function setupTransformer(app: INestApplication) {
  app.useGlobalPipes(
    new ValidationPipe({
      transform: true,
      transformOptions: {
        enableImplicitConversion: true,
      },
      whitelist: true,
    }),
  );
}
```

## 3. Main Integration

Update `src/main.ts` to apply the configurations.

**File**: `src/main.ts`

```typescript
import { setupSwagger } from "@core/swagger.core";
import { setupTransformer } from "@core/transformer.core";

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  setupTransformer(app);
  setupSwagger(app);

  await app.listen(3000);
}
bootstrap();
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erixcel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
