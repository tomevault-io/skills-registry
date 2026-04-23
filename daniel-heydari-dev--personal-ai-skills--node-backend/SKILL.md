---
name: node-js-backend
description: Best practices for Node.js server applications Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Node.js Backend

Build robust, maintainable Node.js backend applications.

## Project Structure

### Rules

- ✅ DO: Separate concerns (routes, controllers, services, models)
- ✅ DO: Use dependency injection
- ✅ DO: Keep entry point minimal
- ❌ DON'T: Put all code in one file
- ❌ DON'T: Mix business logic with route handlers

### Example Structure

```
src/
├── index.ts              # Entry point
├── app.ts                # Express app setup
├── config/
│   └── index.ts          # Configuration
├── routes/
│   ├── index.ts          # Route aggregator
│   └── users.ts          # User routes
├── controllers/
│   └── users.ts          # Request handlers
├── services/
│   └── users.ts          # Business logic
├── models/
│   └── user.ts           # Data models
├── middleware/
│   ├── auth.ts
│   └── errorHandler.ts
├── utils/
│   └── logger.ts
└── types/
    └── index.ts
```

## Express Best Practices

### Rules

- ✅ DO: Use async error handling
- ✅ DO: Validate input at route level
- ✅ DO: Use middleware for cross-cutting concerns
- ✅ DO: Return consistent response format
- ❌ DON'T: Use `app.use()` for everything

### Examples

```typescript
// ✅ Good - clean route structure
// routes/users.ts
import { Router } from "express";
import { userController } from "../controllers/users";
import { validate } from "../middleware/validate";
import { CreateUserSchema, UpdateUserSchema } from "../schemas/user";
import { authenticate } from "../middleware/auth";

const router = Router();

router.get("/", authenticate, userController.list);
router.get("/:id", authenticate, userController.getById);
router.post("/", validate(CreateUserSchema), userController.create);
router.put(
  "/:id",
  authenticate,
  validate(UpdateUserSchema),
  userController.update,
);
router.delete("/:id", authenticate, userController.delete);

export default router;

// ✅ Good - controller
// controllers/users.ts
import { Request, Response, NextFunction } from "express";
import { userService } from "../services/users";

export const userController = {
  async list(req: Request, res: Response, next: NextFunction) {
    try {
      const users = await userService.findAll();
      res.json({ data: users });
    } catch (error) {
      next(error);
    }
  },

  async getById(req: Request, res: Response, next: NextFunction) {
    try {
      const user = await userService.findById(req.params.id);
      if (!user) {
        res.status(404).json({ error: "User not found" });
        return;
      }
      res.json({ data: user });
    } catch (error) {
      next(error);
    }
  },
};

// ✅ Good - service (business logic)
// services/users.ts
import { db } from "../db";
import { CreateUserDTO } from "../types";

export const userService = {
  async findAll() {
    return db.users.findMany();
  },

  async findById(id: string) {
    return db.users.findUnique({ where: { id } });
  },

  async create(data: CreateUserDTO) {
    const hashedPassword = await hashPassword(data.password);
    return db.users.create({
      data: { ...data, password: hashedPassword },
    });
  },
};
```

## Error Handling

### Rules

- ✅ DO: Use centralized error handler
- ✅ DO: Create custom error classes
- ✅ DO: Log errors with context
- ✅ DO: Return user-friendly messages
- ❌ DON'T: Expose stack traces in production
- ❌ DON'T: Swallow errors silently

### Examples

```typescript
// errors/AppError.ts
export class AppError extends Error {
  constructor(
    message: string,
    public statusCode: number = 500,
    public code: string = "INTERNAL_ERROR",
    public isOperational: boolean = true,
  ) {
    super(message);
    this.name = this.constructor.name;
    Error.captureStackTrace(this, this.constructor);
  }
}

export class NotFoundError extends AppError {
  constructor(resource: string) {
    super(`${resource} not found`, 404, "NOT_FOUND");
  }
}

export class ValidationError extends AppError {
  constructor(
    message: string,
    public errors: Record<string, string>,
  ) {
    super(message, 400, "VALIDATION_ERROR");
  }
}

// middleware/errorHandler.ts
import { Request, Response, NextFunction } from "express";
import { AppError } from "../errors/AppError";
import { logger } from "../utils/logger";

export function errorHandler(
  error: Error,
  req: Request,
  res: Response,
  next: NextFunction,
) {
  // Log error
  logger.error({
    message: error.message,
    stack: error.stack,
    path: req.path,
    method: req.method,
  });

  // Handle known errors
  if (error instanceof AppError) {
    res.status(error.statusCode).json({
      error: {
        code: error.code,
        message: error.message,
        ...(error instanceof ValidationError && { details: error.errors }),
      },
    });
    return;
  }

  // Handle unknown errors
  res.status(500).json({
    error: {
      code: "INTERNAL_ERROR",
      message:
        process.env.NODE_ENV === "production"
          ? "Something went wrong"
          : error.message,
    },
  });
}
```

## Input Validation

### Rules

- ✅ DO: Validate all external input
- ✅ DO: Use schema validation (Zod, Joi)
- ✅ DO: Validate at the edge (middleware)
- ✅ DO: Return helpful error messages
- ❌ DON'T: Trust any client input

### Examples

```typescript
// schemas/user.ts
import { z } from "zod";

export const CreateUserSchema = z.object({
  body: z.object({
    name: z.string().min(1).max(100),
    email: z.string().email(),
    password: z.string().min(8).max(100),
  }),
});

export const GetUserSchema = z.object({
  params: z.object({
    id: z.string().uuid(),
  }),
});

// middleware/validate.ts
import { Request, Response, NextFunction } from "express";
import { AnyZodObject, ZodError } from "zod";

export function validate(schema: AnyZodObject) {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      await schema.parseAsync({
        body: req.body,
        query: req.query,
        params: req.params,
      });
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        res.status(400).json({
          error: {
            code: "VALIDATION_ERROR",
            message: "Invalid input",
            details: error.flatten().fieldErrors,
          },
        });
        return;
      }
      next(error);
    }
  };
}
```

## Authentication

### Rules

- ✅ DO: Use established auth libraries
- ✅ DO: Store sessions securely (httpOnly cookies)
- ✅ DO: Implement rate limiting
- ✅ DO: Use HTTPS in production
- ❌ DON'T: Store passwords in plain text
- ❌ DON'T: Expose tokens in URLs

### Examples

```typescript
// middleware/auth.ts
import { Request, Response, NextFunction } from "express";
import { verifyToken } from "../utils/jwt";

export async function authenticate(
  req: Request,
  res: Response,
  next: NextFunction,
) {
  const token = req.cookies.session || req.headers.authorization?.split(" ")[1];

  if (!token) {
    res
      .status(401)
      .json({
        error: { code: "UNAUTHORIZED", message: "Authentication required" },
      });
    return;
  }

  try {
    const payload = await verifyToken(token);
    req.user = payload;
    next();
  } catch {
    res
      .status(401)
      .json({ error: { code: "UNAUTHORIZED", message: "Invalid token" } });
  }
}

export function authorize(...roles: string[]) {
  return (req: Request, res: Response, next: NextFunction) => {
    if (!req.user || !roles.includes(req.user.role)) {
      res
        .status(403)
        .json({
          error: { code: "FORBIDDEN", message: "Insufficient permissions" },
        });
      return;
    }
    next();
  };
}
```

## Configuration

### Rules

- ✅ DO: Use environment variables
- ✅ DO: Validate configuration at startup
- ✅ DO: Have sensible defaults for development
- ❌ DON'T: Commit secrets to version control
- ❌ DON'T: Hardcode configuration

### Examples

```typescript
// config/index.ts
import { z } from "zod";

const ConfigSchema = z.object({
  NODE_ENV: z
    .enum(["development", "test", "production"])
    .default("development"),
  PORT: z.coerce.number().default(3000),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  REDIS_URL: z.string().url().optional(),
});

function loadConfig() {
  const result = ConfigSchema.safeParse(process.env);

  if (!result.success) {
    console.error("❌ Invalid configuration:");
    console.error(result.error.flatten().fieldErrors);
    process.exit(1);
  }

  return result.data;
}

export const config = loadConfig();
```

## Logging

### Rules

- ✅ DO: Use structured logging (JSON)
- ✅ DO: Include context (requestId, userId)
- ✅ DO: Use appropriate log levels
- ✅ DO: Log errors with stack traces
- ❌ DON'T: Log sensitive data (passwords, tokens)

### Examples

```typescript
// utils/logger.ts
import pino from "pino";

export const logger = pino({
  level: process.env.LOG_LEVEL || "info",
  transport:
    process.env.NODE_ENV === "development"
      ? { target: "pino-pretty" }
      : undefined,
});

// Create child logger with context
export function createRequestLogger(requestId: string) {
  return logger.child({ requestId });
}

// middleware/requestLogger.ts
export function requestLogger(req: Request, res: Response, next: NextFunction) {
  const requestId = crypto.randomUUID();
  req.log = createRequestLogger(requestId);

  const start = Date.now();

  res.on("finish", () => {
    req.log.info({
      method: req.method,
      path: req.path,
      statusCode: res.statusCode,
      duration: Date.now() - start,
    });
  });

  next();
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
