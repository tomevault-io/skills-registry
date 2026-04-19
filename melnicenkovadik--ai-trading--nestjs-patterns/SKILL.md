---
name: nestjs-patterns
description: Provide code generation patterns and templates for NestJS (modules, controllers, services, DTOs, pipes, guards, interceptors). Use when the user mentions NestJS or asks for NestJS templates, patterns, or scaffolding. Use when this capability is needed.
metadata:
  author: melnicenkovadik
---

# NestJS Patterns

## Quick start

1. Default to TypeScript and a feature module per domain.
2. Keep controllers thin; move logic to services.
3. Use DTOs with `class-validator` and a global `ValidationPipe`.
4. Prefer constructor-based dependency injection.
5. Return file paths plus minimal templates.

## Templates

### main.ts

```ts
import { ValidationPipe } from "@nestjs/common";
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.useGlobalPipes(new ValidationPipe({ whitelist: true, transform: true }));
  await app.listen(3000);
}
bootstrap();
```

### Feature module

```ts
import { Module } from "@nestjs/common";
import { ItemsController } from "./items.controller";
import { ItemsService } from "./items.service";

@Module({
  controllers: [ItemsController],
  providers: [ItemsService],
})
export class ItemsModule {}
```

### Controller

```ts
import { Body, Controller, Get, Param, Post } from "@nestjs/common";
import { CreateItemDto } from "./dto/create-item.dto";
import { ItemsService } from "./items.service";

@Controller("items")
export class ItemsController {
  constructor(private readonly itemsService: ItemsService) {}

  @Get()
  findAll() {
    return this.itemsService.findAll();
  }

  @Get(":id")
  findOne(@Param("id") id: string) {
    return this.itemsService.findOne(id);
  }

  @Post()
  create(@Body() dto: CreateItemDto) {
    return this.itemsService.create(dto);
  }
}
```

### Service

```ts
import { Injectable } from "@nestjs/common";
import { CreateItemDto } from "./dto/create-item.dto";

@Injectable()
export class ItemsService {
  findAll() {
    return [];
  }

  findOne(id: string) {
    return { id };
  }

  create(dto: CreateItemDto) {
    return { id: "1", ...dto };
  }
}
```

### DTO

```ts
import { IsString, MinLength } from "class-validator";

export class CreateItemDto {
  @IsString()
  @MinLength(1)
  name!: string;
}
```

### Pipe

```ts
import { BadRequestException, Injectable, PipeTransform } from "@nestjs/common";

@Injectable()
export class ParseIntPipe implements PipeTransform<string, number> {
  transform(value: string) {
    const parsed = Number(value);
    if (Number.isNaN(parsed)) {
      throw new BadRequestException("Validation failed");
    }
    return parsed;
  }
}
```

### Guard

```ts
import { CanActivate, ExecutionContext, Injectable } from "@nestjs/common";

@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const req = context.switchToHttp().getRequest();
    return Boolean(req.user);
  }
}
```

### Interceptor

```ts
import { CallHandler, ExecutionContext, Injectable, NestInterceptor } from "@nestjs/common";
import { tap } from "rxjs/operators";

@Injectable()
export class TimingInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler) {
    const start = Date.now();
    return next.handle().pipe(tap(() => console.log(`took ${Date.now() - start}ms`)));
  }
}
```

## Notes

- Keep controllers simple and validate inputs at the boundary.
- Use guards for auth and interceptors for cross-cutting concerns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melnicenkovadik) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
