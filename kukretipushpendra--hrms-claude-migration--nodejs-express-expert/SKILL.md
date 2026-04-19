---
name: nodejs-express-expert
description: Node.js/Express specialist for scalable TypeScript backend applications. Invoke for routes, controllers, services, middleware, mssql database. Keywords: Node.js, Express, TypeScript backend, REST API. Use when this capability is needed.
metadata:
  author: kukretipushpendra
---

# Node.js/Express Expert

Senior Node.js/Express specialist with deep expertise in enterprise-grade, scalable TypeScript backend applications.

## Role Definition

You are a senior Node.js engineer with 10+ years of backend experience. You specialize in Express.js architecture, middleware patterns, and building clean, testable REST APIs with TypeScript.

## When to Use This Skill

- Building Express.js REST APIs
- Implementing routes, controllers, and services
- Creating DTOs with Zod validation
- Setting up authentication (JWT, Passport)
- Implementing middleware (auth, validation, error handling)
- Database integration with mssql (SQL Server)
- Migrating from .NET WebAPI patterns

## .NET → Node.js/Express Pattern Mappings

### Architecture Mapping

| .NET WebAPI | Node.js/Express |
|-------------|-----------------|
| Controller | Controller class + Router |
| Action method | Route handler |
| Service | Service class |
| Repository/Dapper | Service with mssql |
| DbContext | mssql connection pool |
| Entity | TypeScript interface |
| DTO | Zod schema + TypeScript interface |
| Data Annotations | Zod validators |
| [Authorize] | authMiddleware |
| [HttpGet] | router.get() |
| [HttpPost] | router.post() |
| Middleware | Express middleware |
| DI (Dependency Injection) | Manual instantiation or TSyringe |
| ActionResult | res.json() / res.status() |
| IActionResult | Response with status code |

### Response Pattern Mapping

```csharp
// .NET Response Pattern
public class ApiResponseModel<T> {
    public int StatusCode { get; set; }
    public string Message { get; set; }
    public T Result { get; set; }
}
```

```typescript
// Express Response Pattern
interface ApiResponse<T> {
  statusCode: number;
  message: string;
  result: T;
}

// In controller
res.status(200).json({
  statusCode: 200,
  message: 'Success',
  result: data,
});
```

## Core Workflow

1. **Analyze .NET source** - Read legacy controllers, services, and repositories
2. **Map to Express patterns** - Convert .NET patterns to Express equivalents
3. **Design structure** - Plan module organization and routes
4. **Implement** - Create routes, controllers, services with TypeScript
5. **Secure** - Add auth middleware, validation
6. **Test** - Write unit tests and integration tests

## Project Structure

```
src/
├── app.ts                    # Express app setup
├── server.ts                 # Server entry point
├── config/
│   ├── database.ts           # mssql connection pool
│   └── env.ts                # Environment variables
├── middleware/
│   ├── auth.middleware.ts    # JWT verification
│   ├── validation.middleware.ts
│   └── error.middleware.ts   # Global error handler
├── modules/
│   └── {module}/
│       ├── {module}.routes.ts
│       ├── {module}.controller.ts
│       ├── {module}.service.ts
│       ├── dto/
│       │   ├── create-{entity}.dto.ts
│       │   └── update-{entity}.dto.ts
│       └── types/
│           └── {entity}.types.ts
├── types/
│   └── express.d.ts          # Express type extensions
└── utils/
    └── helpers.ts
```

## Express App Setup

```typescript
// src/app.ts
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import { errorMiddleware } from './middleware/error.middleware';
import healthRoutes from './modules/health/health.routes';
import authRoutes from './modules/auth/auth.routes';

const app = express();

// Middleware
app.use(helmet());
app.use(cors({
  origin: ['http://localhost:5173'],
  credentials: true,
}));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.use('/api/health', healthRoutes);
app.use('/api/auth', authRoutes);

// Error handling (must be last)
app.use(errorMiddleware);

export default app;
```

## Router Pattern

```typescript
// src/modules/{module}/{module}.routes.ts
import { Router } from 'express';
import { ModuleController } from './module.controller';
import { authMiddleware } from '../../middleware/auth.middleware';
import { validateDto } from '../../middleware/validation.middleware';
import { CreateEntityDto, UpdateEntityDto } from './dto';

const router = Router();
const controller = new ModuleController();

// Public routes
router.get('/', controller.findAll);
router.get('/:id', controller.findOne);

// Protected routes
router.post('/', authMiddleware, validateDto(CreateEntityDto), controller.create);
router.put('/:id', authMiddleware, validateDto(UpdateEntityDto), controller.update);
router.delete('/:id', authMiddleware, controller.remove);

export default router;
```

## Controller Pattern

```typescript
// src/modules/{module}/{module}.controller.ts
import { Request, Response, NextFunction } from 'express';
import { ModuleService } from './module.service';
import { AppError } from '../../utils/errors';

export class ModuleController {
  private service: ModuleService;

  constructor() {
    this.service = new ModuleService();
  }

  findAll = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
    try {
      const { page = 1, limit = 10, ...filters } = req.query;
      const result = await this.service.findAll({
        page: Number(page),
        limit: Number(limit),
        filters,
      });

      res.json({
        statusCode: 200,
        message: 'Success',
        result,
      });
    } catch (error) {
      next(error);
    }
  };

  findOne = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
    try {
      const { id } = req.params;
      const result = await this.service.findOne(Number(id));

      if (!result) {
        throw new AppError('Not found', 404);
      }

      res.json({
        statusCode: 200,
        message: 'Success',
        result,
      });
    } catch (error) {
      next(error);
    }
  };

  create = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
    try {
      const result = await this.service.create(req.body);

      res.status(201).json({
        statusCode: 201,
        message: 'Created successfully',
        result,
      });
    } catch (error) {
      next(error);
    }
  };

  update = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
    try {
      const { id } = req.params;
      const result = await this.service.update(Number(id), req.body);

      res.json({
        statusCode: 200,
        message: 'Updated successfully',
        result,
      });
    } catch (error) {
      next(error);
    }
  };

  remove = async (req: Request, res: Response, next: NextFunction): Promise<void> => {
    try {
      const { id } = req.params;
      await this.service.remove(Number(id));

      res.json({
        statusCode: 200,
        message: 'Deleted successfully',
        result: null,
      });
    } catch (error) {
      next(error);
    }
  };
}
```

## Service Pattern with mssql

```typescript
// src/modules/{module}/{module}.service.ts
import sql from 'mssql';
import { getPool } from '../../config/database';
import { CreateEntityDto, UpdateEntityDto } from './dto';
import type { Entity } from './types/entity.types';
import { AppError } from '../../utils/errors';

interface FindAllOptions {
  page: number;
  limit: number;
  filters: Record<string, unknown>;
}

export class ModuleService {
  async findAll(options: FindAllOptions) {
    const { page, limit } = options;
    const offset = (page - 1) * limit;
    const pool = await getPool();

    // Get total count
    const countResult = await pool.request()
      .query<{ total: number }>('SELECT COUNT(*) as total FROM Entities');
    const total = countResult.recordset[0].total;

    // Get paginated data
    const result = await pool.request()
      .input('offset', sql.Int, offset)
      .input('limit', sql.Int, limit)
      .query<Entity>(`
        SELECT * FROM Entities
        ORDER BY CreatedAt DESC
        OFFSET @offset ROWS FETCH NEXT @limit ROWS ONLY
      `);

    return {
      data: result.recordset,
      meta: {
        total,
        page,
        limit,
        totalPages: Math.ceil(total / limit),
      },
    };
  }

  async findOne(id: number): Promise<Entity | null> {
    const pool = await getPool();
    const result = await pool.request()
      .input('id', sql.Int, id)
      .query<Entity>('SELECT * FROM Entities WHERE Id = @id');
    return result.recordset[0] || null;
  }

  async create(dto: CreateEntityDto): Promise<Entity> {
    const pool = await getPool();
    const result = await pool.request()
      .input('name', sql.NVarChar(255), dto.name)
      .input('email', sql.NVarChar(255), dto.email)
      .input('isActive', sql.Bit, dto.isActive ?? true)
      .query<Entity>(`
        INSERT INTO Entities (Name, Email, IsActive, CreatedAt)
        OUTPUT INSERTED.*
        VALUES (@name, @email, @isActive, GETDATE())
      `);
    return result.recordset[0];
  }

  async update(id: number, dto: UpdateEntityDto): Promise<Entity> {
    const pool = await getPool();

    // Check if exists
    const existing = await this.findOne(id);
    if (!existing) {
      throw new AppError('Not found', 404);
    }

    const result = await pool.request()
      .input('id', sql.Int, id)
      .input('name', sql.NVarChar(255), dto.name ?? existing.name)
      .input('email', sql.NVarChar(255), dto.email ?? existing.email)
      .input('isActive', sql.Bit, dto.isActive ?? existing.isActive)
      .query<Entity>(`
        UPDATE Entities
        SET Name = @name, Email = @email, IsActive = @isActive, UpdatedAt = GETDATE()
        OUTPUT INSERTED.*
        WHERE Id = @id
      `);
    return result.recordset[0];
  }

  async remove(id: number): Promise<void> {
    const pool = await getPool();
    const result = await pool.request()
      .input('id', sql.Int, id)
      .query('DELETE FROM Entities WHERE Id = @id');

    if (result.rowsAffected[0] === 0) {
      throw new AppError('Not found', 404);
    }
  }
}
```

## TypeScript Interface Pattern

```typescript
// src/modules/{module}/types/{entity}.types.ts
export interface Entity {
  id: number;
  name: string;
  email: string;
  isActive: boolean;
  createdAt: Date;
  updatedAt: Date | null;
}
```

## DTO with Zod Validation

```typescript
// src/modules/{module}/dto/create-entity.dto.ts
import { z } from 'zod';

export const CreateEntitySchema = z.object({
  name: z.string().min(1, 'Name is required').max(255),
  email: z.string().email('Invalid email address'),
  isActive: z.boolean().optional().default(true),
});

export type CreateEntityDto = z.infer<typeof CreateEntitySchema>;

// src/modules/{module}/dto/update-entity.dto.ts
export const UpdateEntitySchema = CreateEntitySchema.partial();
export type UpdateEntityDto = z.infer<typeof UpdateEntitySchema>;
```

## Validation Middleware

```typescript
// src/middleware/validation.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { ZodSchema, ZodError } from 'zod';

export const validateDto = (schema: ZodSchema) => {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      req.body = schema.parse(req.body);
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        res.status(400).json({
          statusCode: 400,
          message: 'Validation failed',
          result: error.errors.map((e) => ({
            field: e.path.join('.'),
            message: e.message,
          })),
        });
        return;
      }
      next(error);
    }
  };
};
```

## Auth Middleware (JWT)

```typescript
// src/middleware/auth.middleware.ts
import { Request, Response, NextFunction } from 'express';
import jwt from 'jsonwebtoken';
import { AppError } from '../utils/errors';

interface JwtPayload {
  userId: number;
  email: string;
  role: string;
}

declare global {
  namespace Express {
    interface Request {
      user?: JwtPayload;
    }
  }
}

export const authMiddleware = (req: Request, res: Response, next: NextFunction) => {
  try {
    const authHeader = req.headers.authorization;

    if (!authHeader || !authHeader.startsWith('Bearer ')) {
      throw new AppError('Unauthorized', 401);
    }

    const token = authHeader.split(' ')[1];
    const decoded = jwt.verify(token, process.env.JWT_SECRET!) as JwtPayload;

    req.user = decoded;
    next();
  } catch (error) {
    if (error instanceof jwt.JsonWebTokenError) {
      next(new AppError('Invalid token', 401));
    } else {
      next(error);
    }
  }
};
```

## Error Handling Middleware

```typescript
// src/middleware/error.middleware.ts
import { Request, Response, NextFunction } from 'express';
import { AppError } from '../utils/errors';

export const errorMiddleware = (
  err: Error,
  req: Request,
  res: Response,
  _next: NextFunction
) => {
  console.error('Error:', err);

  if (err instanceof AppError) {
    res.status(err.statusCode).json({
      statusCode: err.statusCode,
      message: err.message,
      result: null,
    });
    return;
  }

  res.status(500).json({
    statusCode: 500,
    message: 'Internal server error',
    result: null,
  });
};

// src/utils/errors.ts
export class AppError extends Error {
  constructor(
    message: string,
    public statusCode: number = 500
  ) {
    super(message);
    this.name = 'AppError';
  }
}
```

## Database Configuration (mssql)

```typescript
// src/config/database.ts
import sql from 'mssql';

const config: sql.config = {
  server: process.env.DB_HOST!,
  database: process.env.DB_NAME!,
  user: process.env.DB_USER!,
  password: process.env.DB_PASSWORD!,
  port: Number(process.env.DB_PORT) || 1433,
  options: {
    instanceName: process.env.DB_INSTANCE,
    encrypt: process.env.DB_ENCRYPT === 'true',
    trustServerCertificate: process.env.DB_TRUST_SERVER_CERT === 'true',
    enableArithAbort: true,
  },
  pool: {
    min: Number(process.env.DB_POOL_MIN) || 0,
    max: Number(process.env.DB_POOL_MAX) || 10,
    idleTimeoutMillis: Number(process.env.DB_POOL_IDLE) || 30000,
  },
};

let pool: sql.ConnectionPool | null = null;

export const getPool = async (): Promise<sql.ConnectionPool> => {
  if (!pool) {
    pool = await sql.connect(config);
  }
  return pool;
};

export const initDatabase = async (): Promise<void> => {
  try {
    const connection = await getPool();
    await connection.request().query('SELECT 1');
    console.log('Database connected successfully');
  } catch (error) {
    console.error('Database connection failed:', error);
    process.exit(1);
  }
};

export const closeDatabase = async (): Promise<void> => {
  if (pool) {
    await pool.close();
    pool = null;
  }
};
```

## Health Check Endpoint

```typescript
// src/modules/health/health.routes.ts
import { Router } from 'express';
import { getPool } from '../../config/database';

const router = Router();

router.get('/', async (req, res) => {
  let dbStatus = 'disconnected';

  try {
    const pool = await getPool();
    await pool.request().query('SELECT 1');
    dbStatus = 'connected';
  } catch (error) {
    dbStatus = 'error';
  }

  res.json({
    status: 'ok',
    timestamp: new Date().toISOString(),
    database: {
      status: dbStatus,
    },
  });
});

export default router;
```

## Stored Procedure Pattern

```typescript
// Calling a stored procedure
async getByStoredProc(userId: number): Promise<Entity[]> {
  const pool = await getPool();
  const result = await pool.request()
    .input('UserId', sql.Int, userId)
    .execute<Entity>('sp_GetUserEntities');
  return result.recordset;
}

// Stored procedure with output parameter
async createWithOutput(dto: CreateEntityDto): Promise<{ id: number }> {
  const pool = await getPool();
  const result = await pool.request()
    .input('Name', sql.NVarChar(255), dto.name)
    .output('NewId', sql.Int)
    .execute('sp_CreateEntity');
  return { id: result.output.NewId };
}
```

## Transaction Pattern

```typescript
async transferWithTransaction(fromId: number, toId: number, amount: number): Promise<void> {
  const pool = await getPool();
  const transaction = new sql.Transaction(pool);

  try {
    await transaction.begin();
    const request = new sql.Request(transaction);

    // Deduct from source
    await request
      .input('fromId', sql.Int, fromId)
      .input('amount', sql.Decimal(18, 2), amount)
      .query('UPDATE Accounts SET Balance = Balance - @amount WHERE Id = @fromId');

    // Add to destination
    await request
      .input('toId', sql.Int, toId)
      .query('UPDATE Accounts SET Balance = Balance + @amount WHERE Id = @toId');

    await transaction.commit();
  } catch (error) {
    await transaction.rollback();
    throw error;
  }
}
```

## Required Dependencies

```json
{
  "dependencies": {
    "express": "^4.18.2",
    "cors": "^2.8.5",
    "helmet": "^7.1.0",
    "jsonwebtoken": "^9.0.2",
    "bcryptjs": "^2.4.3",
    "mssql": "^10.0.2",
    "zod": "^3.22.4",
    "dotenv": "^16.3.1"
  },
  "devDependencies": {
    "@types/express": "^4.17.21",
    "@types/cors": "^2.8.17",
    "@types/jsonwebtoken": "^9.0.5",
    "@types/bcryptjs": "^2.4.6",
    "@types/mssql": "^9.1.5",
    "@types/node": "^20.10.0",
    "typescript": "^5.3.2",
    "ts-node-dev": "^2.0.0",
    "eslint": "^8.55.0",
    "@typescript-eslint/eslint-plugin": "^6.13.1",
    "@typescript-eslint/parser": "^6.13.1",
    "prettier": "^3.1.0"
  }
}
```

## package.json Scripts

```json
{
  "scripts": {
    "dev": "ts-node-dev --respawn --transpile-only src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "lint": "eslint . --ext .ts --report-unused-disable-directives --max-warnings 0",
    "lint:fix": "eslint . --ext .ts --fix",
    "format": "prettier --write \"src/**/*.ts\"",
    "type-check": "tsc --noEmit",
    "db:test": "ts-node src/scripts/test-db-connection.ts"
  }
}
```

## Constraints

### MUST DO
- Use TypeScript with strict mode
- Validate all inputs with Zod
- Use proper error handling with AppError class
- Document APIs for frontend contracts
- Write clean, testable code with separation of concerns
- Use environment variables for configuration
- Match legacy .NET API response shapes exactly
- Use parameterized queries to prevent SQL injection

### MUST NOT DO
- Expose passwords or secrets in responses
- Trust user input without validation
- Use `any` type unless absolutely necessary
- Hardcode configuration values
- Skip error handling
- Modify legacy API response structure
- Build SQL queries with string concatenation

## Knowledge Reference

Node.js, Express.js, TypeScript, mssql (node-mssql), SQL Server, JWT, Passport, Zod, Bcrypt, Cors, Helmet, REST API design, Middleware patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kukretipushpendra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
