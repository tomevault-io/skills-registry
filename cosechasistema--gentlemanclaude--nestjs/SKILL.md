---
name: nestjs
description: NestJS 11 patterns, modules, decorators, guards, pipes, CQRS. Use when this capability is needed.
metadata:
  author: cosechasistema
---
---
name: nestjs
description: >
  NestJS 11 patterns, modules, decorators, guards, pipes, CQRS.
  Trigger: When writing NestJS backend code - controllers, services, modules, DTOs.
license: Apache-2.0
metadata:
  author: gentleman-programming
  version: "1.0"
---

## When to Use

- Creating or modifying NestJS controllers, services, modules
- Writing DTOs with class-validator
- Implementing guards, interceptors, pipes, exception filters
- Setting up authentication (JWT/Passport)
- Working with CQRS pattern
- Writing NestJS unit or e2e tests

---

## Module Organization (Feature-Based)

```
src/
├── main.ts
├── app.module.ts
├── common/                 # Shared guards, interceptors, pipes, filters
│   ├── decorators/
│   ├── guards/
│   ├── interceptors/
│   ├── pipes/
│   └── filters/
├── config/
├── modules/
│   └── users/
│       ├── users.module.ts
│       ├── users.controller.ts
│       ├── users.service.ts
│       ├── dto/
│       │   ├── create-user.dto.ts
│       │   └── update-user.dto.ts
│       └── entities/
└── shared/                 # Global modules (database, cache)
    └── prisma/
```

---

## Critical Patterns

### Controller + Service

```typescript
@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Post()
  create(@Body() dto: CreateUserDto) {
    return this.usersService.create(dto);
  }

  @Get(':id')
  findOne(@Param('id', ParseIntPipe) id: number) {
    return this.usersService.findOne(id);
  }
}
```

### DTOs with class-validator (REQUIRED)

```typescript
import { IsEmail, IsNotEmpty, IsString, MinLength, IsOptional } from 'class-validator';
import { Transform } from 'class-transformer';

export class CreateUserDto {
  @IsString()
  @IsNotEmpty()
  name: string;

  @IsEmail()
  email: string;

  @IsString()
  @MinLength(8)
  password: string;

  @IsOptional()
  @Transform(({ value }) => value?.trim())
  bio?: string;
}
```

### Global ValidationPipe (main.ts)

```typescript
app.useGlobalPipes(new ValidationPipe({
  whitelist: true,
  forbidNonWhitelisted: true,
  transform: true,
}));
```

### Execution Order

```
Middleware → Guards → Before Interceptors → Pipes → Controller → Service → After Interceptors → Exception Filters
```

### Guard

```typescript
@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const roles = this.reflector.get<string[]>('roles', context.getHandler());
    if (!roles) return true;
    const request = context.switchToHttp().getRequest();
    return roles.some(role => request.user.roles?.includes(role));
  }
}
```

### Custom Decorator

```typescript
export const CurrentUser = createParamDecorator(
  (data: unknown, ctx: ExecutionContext) => {
    return ctx.switchToHttp().getRequest().user;
  },
);

export const Roles = (...roles: string[]) => SetMetadata('roles', roles);
```

### Exception Filter

```typescript
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const status = exception.getStatus();

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: ctx.getRequest<Request>().url,
      message: exception.message,
    });
  }
}
```

---

## Authentication (JWT + Passport)

```typescript
// jwt.strategy.ts
@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor() {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: process.env.JWT_SECRET,
    });
  }

  async validate(payload: any) {
    return { userId: payload.sub, email: payload.email };
  }
}

// auth.module.ts
@Module({
  imports: [
    PassportModule,
    JwtModule.register({
      secret: process.env.JWT_SECRET,
      signOptions: { expiresIn: '1h' },
    }),
  ],
  providers: [AuthService, JwtStrategy, LocalStrategy],
})
export class AuthModule {}
```

---

## CQRS Pattern

```typescript
// command
export class CreateUserCommand {
  constructor(public readonly name: string, public readonly email: string) {}
}

// handler
@CommandHandler(CreateUserCommand)
export class CreateUserHandler implements ICommandHandler<CreateUserCommand> {
  async execute(command: CreateUserCommand) { /* ... */ }
}

// query
export class GetUserQuery {
  constructor(public readonly id: number) {}
}

@QueryHandler(GetUserQuery)
export class GetUserHandler implements IQueryHandler<GetUserQuery> {
  async execute(query: GetUserQuery) { /* ... */ }
}

// event
export class UserCreatedEvent {
  constructor(public readonly userId: number) {}
}

@EventsHandler(UserCreatedEvent)
export class UserCreatedHandler implements IEventHandler<UserCreatedEvent> {
  handle(event: UserCreatedEvent) { /* ... */ }
}
```

---

## Testing

```typescript
// Unit test
describe('UsersService', () => {
  let service: UsersService;
  let prisma: PrismaService;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [
        UsersService,
        { provide: PrismaService, useValue: { user: { create: jest.fn(), findUnique: jest.fn() } } },
      ],
    }).compile();

    service = module.get(UsersService);
    prisma = module.get(PrismaService);
  });
});

// E2E test
describe('UsersController (e2e)', () => {
  let app: INestApplication;

  beforeEach(async () => {
    const module = await Test.createTestingModule({ imports: [AppModule] }).compile();
    app = module.createNestApplication();
    await app.init();
  });

  it('/users (POST)', () => {
    return request(app.getHttpServer())
      .post('/users')
      .send({ name: 'Test', email: 'test@test.com' })
      .expect(201);
  });
});
```

---

## Decision Tree

```
Need authentication?      → JWT + Passport strategy
Need authorization?       → Custom Guard + Roles decorator
Need request validation?  → DTO + class-validator + ValidationPipe
Need response transform?  → Interceptor
Need error formatting?    → Exception Filter
Need request logging?     → Middleware or Interceptor
Need CQRS?               → @nestjs/cqrs (commands, queries, events)
```

---

## Commands

```bash
nest new project-name            # Create project
nest g res modules/users         # Generate full resource (module+controller+service+dto)
nest g mo modules/auth           # Generate module
nest g co modules/auth           # Generate controller
nest g s modules/auth            # Generate service
nest g guard common/guards/roles # Generate guard
npm run start:dev                # Dev with watch
npm run test                     # Unit tests
npm run test:e2e                 # E2E tests
```

---

## NestJS 11 Notes

- Express v5: Wildcards must be named (`*splat` not `*`)
- `ParseDatePipe` available in @nestjs/common
- JSON logging built-in for containers
- Prisma integration: set `moduleFormat = "cjs"` in schema.prisma (Prisma 7 is ESM default)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cosechasistema) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
