---
name: nestjs-backend
description: NestJS + Prisma + GraphQL 后端开发最佳实践，适用于模块化架构、代码优先的 GraphQL 开发、数据库建模及 Web3 业务逻辑。在编写、重构或调试后端代码时触发此技能。 Use when this capability is needed.
metadata:
  author: xfz1987
---

# NestJS 后端开发规范

本技能为 Web3 Agent Marketplace 项目的后端开发提供标准化指导。

## 项目技术栈

- **NestJS** (v10+)
- **GraphQL** (Apollo Driver, Code-first)
- **Prisma** (ORM)
- **TypeScript**
- **Passport/JWT** (身份验证)

## 核心架构规范

采用模块化架构，每个功能模块应包含以下结构：

```
src/task/
├── task.module.ts      # 模块定义
├── task.resolver.ts    # GraphQL 解析器 (替代 Controller)
├── task.service.ts     # 业务逻辑
├── dto/                # 输入/输出类型定义
│   ├── create-task.input.ts
│   └── task.output.ts
└── entities/           # 领域模型 (通常对应 Prisma Model)
    └── task.entity.ts
```

## GraphQL 开发规范 (Code-first)

### 实体与输出 (Entity & Output)

使用 `@ObjectType()` 定义 GraphQL 类型：

```typescript
// src/task/entities/task.entity.ts
import { ObjectType, Field, ID, registerEnumType } from '@nestjs/graphql';

export enum TaskStatus {
  OPEN = 'OPEN',
  IN_PROGRESS = 'IN_PROGRESS',
  COMPLETED = 'COMPLETED',
}

registerEnumType(TaskStatus, { name: 'TaskStatus' });

@ObjectType()
export class Task {
  @Field(() => ID)
  id: string;

  @Field()
  title: string;

  @Field(() => TaskStatus)
  status: TaskStatus;

  @Field()
  createdAt: Date;
}
```

### 输入类型 (Input Types)

使用 `@InputType()` 定义 Mutation 的输入：

```typescript
// src/task/dto/create-task.input.ts
import { InputType, Field } from '@nestjs/graphql';
import { IsNotEmpty, MinLength } from 'class-validator';

@InputType()
export class CreateTaskInput {
  @Field()
  @IsNotEmpty()
  @MinLength(3)
  title: string;

  @Field({ nullable: true })
  description?: string;
}
```

### 解析器 (Resolver)

```typescript
// src/task/task.resolver.ts
import { Resolver, Query, Mutation, Args } from '@nestjs/graphql';
import { TaskService } from './task.service';
import { Task } from './entities/task.entity';
import { CreateTaskInput } from './dto/create-task.input';

@Resolver(() => Task)
export class TaskResolver {
  constructor(private readonly taskService: TaskService) {}

  @Query(() => [Task], { name: 'tasks' })
  async findAll() {
    return this.taskService.findAll();
  }

  @Mutation(() => Task)
  async createTask(@Args('input') input: CreateTaskInput) {
    return this.taskService.create(input);
  }
}
```

## Prisma 集成规范

### Prisma Service

确保 `PrismaService` 注入正确：

```typescript
// src/prisma/prisma.service.ts
import { Injectable, OnModuleInit } from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService extends PrismaClient implements OnModuleInit {
  async onModuleInit() {
    await this.$connect();
  }
}
```

### Service 使用

```typescript
// src/task/task.service.ts
import { Injectable } from '@nestjs/common';
import { PrismaService } from '../prisma/prisma.service';
import { CreateTaskInput } from './dto/create-task.input';

@Injectable()
export class TaskService {
  constructor(private prisma: PrismaService) {}

  async findAll() {
    return this.prisma.task.findMany({
      orderBy: { createdAt: 'desc' },
    });
  }

  async create(data: CreateTaskInput) {
    return this.prisma.task.create({
      data,
    });
  }
}
```

## 异常处理与验证

1.  **验证**：使用 `class-validator` 和 `class-transformer` 进行 DTO 验证。
2.  **异常**：直接抛出 NestJS 标准异常（如 `NotFoundException`, `BadRequestException`）。GraphQL 模块会自动将其转换为对应的 GraphQL Error。

```typescript
if (!task) {
  throw new NotFoundException(`Task with ID ${id} not found`);
}
```

## Web3 开发建议

1.  **地址存储**：在 Prisma 中使用 `String` 存储 `0x...` 地址，建议在应用层统一转换为小写进行比较。
2.  **签名校验**：使用 `viem` 或 `ethers` 在 Service 层校验 `personal_sign` 签名以进行身份认证。
3.  **BigInt 处理**：GraphQL 默认不支持 `BigInt`。如果涉及代币余额，建议：
    - 在 GraphQL 层使用 `String` 或自定义 `BigInt` Scalar。
    - 在数据库中存储为 `Decimal` (Prisma) 或 `String` 以避免精度丢失。

## 测试规范

每个 Service 和 Resolver 都应有对应的 `.spec.ts` 文件：

```typescript
// src/task/task.service.spec.ts
describe('TaskService', () => {
  let service: TaskService;
  let prisma: PrismaService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      providers: [
        TaskService,
        { provide: PrismaService, useValue: mockPrisma },
      ],
    }).compile();

    service = module.get<TaskService>(TaskService);
    prisma = module.get<PrismaService>(PrismaService);
  });
  // ... 测试用例
});
```

## 代码质量

- 遵循 `ESLint` 和 `Prettier` 配置。
- 优先使用构造函数注入 (Constructor Injection)。
- 保持逻辑在 Service 层，Resolver 仅负责分发和类型转换。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xfz1987) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
