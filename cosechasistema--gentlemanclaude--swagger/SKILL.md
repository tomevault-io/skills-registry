---
name: swagger
description: NestJS Swagger/OpenAPI documentation patterns with @nestjs/swagger. Use when this capability is needed.
metadata:
  author: cosechasistema
---
---
name: swagger
description: >
  NestJS Swagger/OpenAPI documentation patterns with @nestjs/swagger.
  Trigger: When documenting APIs with Swagger, adding API decorators, or generating OpenAPI specs.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "1.0"
---

## When to Use

- Setting up Swagger UI in NestJS
- Documenting controllers with API decorators
- Adding DTO property descriptions
- Grouping endpoints with tags
- Generating OpenAPI spec files
- Documenting authentication schemas

---

## Setup (main.ts)

```typescript
import { NestFactory } from '@nestjs/core';
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';
import { AppModule } from './app.module';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  const config = new DocumentBuilder()
    .setTitle('My API')
    .setDescription('API documentation')
    .setVersion('1.0')
    .addBearerAuth({
      type: 'http',
      scheme: 'bearer',
      bearerFormat: 'JWT',
      description: 'Enter JWT token',
    })
    .addTag('users', 'User management')
    .addTag('auth', 'Authentication')
    .build();

  const document = SwaggerModule.createDocument(app, config);
  SwaggerModule.setup('api/docs', app, document, {
    swaggerOptions: {
      persistAuthorization: true,
    },
  });

  await app.listen(3000);
}
bootstrap();
```

---

## Critical Patterns

### Controller Documentation

```typescript
import {
  ApiTags, ApiOperation, ApiResponse, ApiBearerAuth,
  ApiParam, ApiQuery, ApiBody,
} from '@nestjs/swagger';

@ApiTags('users')
@ApiBearerAuth()
@Controller('users')
export class UsersController {
  @Post()
  @ApiOperation({ summary: 'Create a user' })
  @ApiBody({ type: CreateUserDto })
  @ApiResponse({ status: 201, description: 'User created', type: UserResponseDto })
  @ApiResponse({ status: 409, description: 'Email already exists' })
  create(@Body() dto: CreateUserDto) {
    return this.usersService.create(dto);
  }

  @Get()
  @ApiOperation({ summary: 'List all users' })
  @ApiQuery({ name: 'page', required: false, type: Number, example: 1 })
  @ApiQuery({ name: 'limit', required: false, type: Number, example: 10 })
  @ApiQuery({ name: 'search', required: false, type: String })
  @ApiResponse({ status: 200, description: 'Users list', type: [UserResponseDto] })
  findAll(
    @Query('page') page?: number,
    @Query('limit') limit?: number,
    @Query('search') search?: string,
  ) {
    return this.usersService.findAll({ page, limit, search });
  }

  @Get(':id')
  @ApiOperation({ summary: 'Get user by ID' })
  @ApiParam({ name: 'id', type: String, description: 'User ID' })
  @ApiResponse({ status: 200, description: 'User found', type: UserResponseDto })
  @ApiResponse({ status: 404, description: 'User not found' })
  findOne(@Param('id') id: string) {
    return this.usersService.findOne(id);
  }

  @Patch(':id')
  @ApiOperation({ summary: 'Update a user' })
  @ApiResponse({ status: 200, type: UserResponseDto })
  update(@Param('id') id: string, @Body() dto: UpdateUserDto) {
    return this.usersService.update(id, dto);
  }

  @Delete(':id')
  @ApiOperation({ summary: 'Delete a user' })
  @ApiResponse({ status: 200, description: 'User deleted' })
  remove(@Param('id') id: string) {
    return this.usersService.remove(id);
  }
}
```

### DTO Documentation

```typescript
import { ApiProperty, ApiPropertyOptional, PartialType, OmitType, PickType } from '@nestjs/swagger';

export class CreateUserDto {
  @ApiProperty({ example: 'John Doe', description: 'Full name' })
  @IsString()
  @IsNotEmpty()
  name: string;

  @ApiProperty({ example: 'john@example.com', description: 'Unique email' })
  @IsEmail()
  email: string;

  @ApiProperty({ example: 'StrongP@ss1', minLength: 8 })
  @IsString()
  @MinLength(8)
  password: string;

  @ApiPropertyOptional({ example: 'Software developer', description: 'User bio' })
  @IsOptional()
  @IsString()
  bio?: string;

  @ApiProperty({ enum: ['USER', 'ADMIN', 'MODERATOR'], default: 'USER' })
  @IsEnum(Role)
  role: Role;
}

// Response DTO (exclude password)
export class UserResponseDto extends OmitType(CreateUserDto, ['password']) {
  @ApiProperty({ example: 'cuid_abc123' })
  id: string;

  @ApiProperty()
  createdAt: Date;
}

// Update DTO (all optional)
export class UpdateUserDto extends PartialType(
  OmitType(CreateUserDto, ['password']),
) {}

// Login DTO (pick specific fields)
export class LoginDto extends PickType(CreateUserDto, ['email', 'password']) {}
```

---

## Swagger CLI Plugin (Auto-Documentation)

```json
// nest-cli.json — reduces manual @ApiProperty decorators
{
  "compilerOptions": {
    "plugins": [
      {
        "name": "@nestjs/swagger",
        "options": {
          "classValidatorShim": true,
          "introspectComments": true,
          "dtoFileNameSuffix": [".dto.ts", ".entity.ts"]
        }
      }
    ]
  }
}
```

With the plugin enabled:
- `@ApiProperty()` is auto-added based on TypeScript types
- JSDoc comments become `description` values
- class-validator decorators are reflected in schema
- You still need `@ApiProperty()` for `example`, `enum`, `description` overrides

---

## Authentication Documentation

```typescript
// Multiple auth schemes
const config = new DocumentBuilder()
  .addBearerAuth({ type: 'http', scheme: 'bearer', bearerFormat: 'JWT' }, 'JWT')
  .addApiKey({ type: 'apiKey', in: 'header', name: 'X-API-KEY' }, 'API-KEY')
  .build();

// Controller-level
@ApiBearerAuth('JWT')
@Controller('users')
export class UsersController {}

// Method-level (public endpoint)
@Get('public')
@ApiOperation({ summary: 'Public endpoint — no auth required' })
findPublic() {}
```

---

## Paginated Response Pattern

```typescript
export class PaginatedResponseDto<T> {
  @ApiProperty()
  data: T[];

  @ApiProperty({ example: 100 })
  total: number;

  @ApiProperty({ example: 1 })
  page: number;

  @ApiProperty({ example: 10 })
  limit: number;

  @ApiProperty({ example: 10 })
  totalPages: number;
}

// Usage in controller
@ApiResponse({
  status: 200,
  description: 'Paginated user list',
  schema: {
    allOf: [
      {
        properties: {
          data: { type: 'array', items: { $ref: getSchemaPath(UserResponseDto) } },
          total: { type: 'number', example: 100 },
          page: { type: 'number', example: 1 },
          limit: { type: 'number', example: 10 },
        },
      },
    ],
  },
})
```

---

## Generate Static OpenAPI Spec

```typescript
// scripts/generate-swagger.ts
import { NestFactory } from '@nestjs/core';
import { DocumentBuilder, SwaggerModule } from '@nestjs/swagger';
import { AppModule } from '../src/app.module';
import { writeFileSync } from 'fs';

async function generate() {
  const app = await NestFactory.create(AppModule);
  const config = new DocumentBuilder().setTitle('API').setVersion('1.0').build();
  const document = SwaggerModule.createDocument(app, config);
  writeFileSync('./swagger.json', JSON.stringify(document, null, 2));
  await app.close();
}
generate();
```

---

## Decision Tree

```
Need to document a controller?   → @ApiTags + @ApiOperation + @ApiResponse
Need to document a DTO?          → @ApiProperty (required) / @ApiPropertyOptional
Need auth in Swagger UI?         → DocumentBuilder.addBearerAuth() + @ApiBearerAuth()
Want less boilerplate?           → Enable Swagger CLI Plugin in nest-cli.json
Need static OpenAPI JSON?        → Generate script with SwaggerModule.createDocument()
Need pagination schema?          → Custom generic + getSchemaPath()
Need enum in docs?               → @ApiProperty({ enum: MyEnum })
```

---

## Commands

```bash
npm i @nestjs/swagger        # Install
# Swagger UI available at: http://localhost:3000/api/docs
# OpenAPI JSON at: http://localhost:3000/api/docs-json
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cosechasistema) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
