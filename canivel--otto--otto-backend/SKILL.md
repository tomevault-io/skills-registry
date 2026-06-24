---
name: otto-backend
description: Backend specialist for Otto core engine development. Use when working on Express.js API, services, database schemas, workers, queues, or any server-side code in src/. Use when this capability is needed.
metadata:
  author: canivel
---

# Otto Backend Specialist

Expert context for developing the Otto Agent backend (Express.js API server).

## Critical Rule

**NEVER use `ANTHROPIC_API_KEY` or direct LLM API keys in Otto core.**

All AI interactions must use Claude Code OAuth tokens via:
```typescript
const env = {
  ...process.env,
  CLAUDE_CODE_OAUTH_TOKEN: oauthToken,
  NO_COLOR: '1',
};
const child = spawn('claude', ['--print'], { env, shell: true });
```

## Architecture Overview

```
src/
├── config/          # Environment configuration (config.ts)
├── db/schema/       # Drizzle ORM tables (37 schemas)
├── middleware/      # Express middleware (auth, rate-limit, org-context)
├── routes/          # API endpoints (45+ route files)
├── services/        # Business logic (70+ services)
├── workers/         # BullMQ job processors
├── queues/          # Queue definitions
├── websocket/       # Real-time handlers
├── utils/           # Logger, error handling
└── types/           # Express type extensions
```

## Database Patterns

### Schema Definition (Drizzle ORM)
Location: `src/db/schema/`

```typescript
import { pgTable, text, timestamp, uuid } from 'drizzle-orm/pg-core';

export const myTable = pgTable('my_table', {
  id: uuid('id').primaryKey().defaultRandom(),
  organizationId: uuid('organization_id').notNull().references(() => organizations.id),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull(),
});
```

**Conventions:**
- Always include `organizationId` for multi-tenant tables
- Use `uuid` for IDs with `.defaultRandom()`
- Include `createdAt` and `updatedAt` timestamps
- Export from `src/db/schema/index.ts`

### Database Queries
```typescript
import { db } from '../db';
import { myTable } from '../db/schema';
import { eq, and } from 'drizzle-orm';

// Select with org context
const items = await db.select()
  .from(myTable)
  .where(and(
    eq(myTable.organizationId, orgId),
    eq(myTable.status, 'active')
  ));

// Insert
const [newItem] = await db.insert(myTable)
  .values({ organizationId: orgId, name: 'test' })
  .returning();

// Update
await db.update(myTable)
  .set({ status: 'completed', updatedAt: new Date() })
  .where(eq(myTable.id, itemId));
```

## Service Patterns

Location: `src/services/`

```typescript
import { db } from '../db';
import { myTable } from '../db/schema';
import { logger } from '../utils/logger';

export const myService = {
  async create(orgId: string, data: CreateInput) {
    logger.info('Creating item', { orgId, data });

    const [item] = await db.insert(myTable)
      .values({ organizationId: orgId, ...data })
      .returning();

    return item;
  },

  async getById(orgId: string, id: string) {
    const [item] = await db.select()
      .from(myTable)
      .where(and(
        eq(myTable.id, id),
        eq(myTable.organizationId, orgId)
      ));

    return item || null;
  },
};
```

**Conventions:**
- Export as object with methods
- Always validate organization context
- Use Winston logger for all operations
- Return `null` for not found, don't throw

## Route Patterns

Location: `src/routes/`

```typescript
import { Router } from 'express';
import { requireAuth, requireOrgContext } from '../middleware/auth';
import { myService } from '../services/myService';

const router = Router();

// All routes require auth and org context
router.use(requireAuth);
router.use(requireOrgContext);

router.get('/', async (req, res, next) => {
  try {
    const orgId = req.organizationId!;
    const items = await myService.list(orgId);
    res.json({ items });
  } catch (error) {
    next(error);
  }
});

router.post('/', async (req, res, next) => {
  try {
    const orgId = req.organizationId!;
    const item = await myService.create(orgId, req.body);
    res.status(201).json(item);
  } catch (error) {
    next(error);
  }
});

export default router;
```

**Conventions:**
- Use `requireAuth` and `requireOrgContext` middleware
- Access org via `req.organizationId`
- Use try/catch with `next(error)` for error handling
- Register routes in `src/routes/index.ts`

## Worker Patterns (BullMQ)

Location: `src/workers/`

```typescript
import { Worker, Job } from 'bullmq';
import { redisConnection } from '../queues/connection';
import { logger } from '../utils/logger';

interface MyJobData {
  itemId: string;
  organizationId: string;
}

const processor = async (job: Job<MyJobData>) => {
  const { itemId, organizationId } = job.data;
  logger.info('Processing job', { jobId: job.id, itemId });

  try {
    // Process the job
    await doWork(itemId);
    return { success: true };
  } catch (error) {
    logger.error('Job failed', { jobId: job.id, error });
    throw error; // BullMQ will retry
  }
};

export const myWorker = new Worker('my-queue', processor, {
  connection: redisConnection,
  concurrency: 5,
});

myWorker.on('completed', (job) => {
  logger.info('Job completed', { jobId: job.id });
});

myWorker.on('failed', (job, error) => {
  logger.error('Job failed', { jobId: job?.id, error: error.message });
});
```

## Queue Patterns

Location: `src/queues/`

```typescript
import { Queue } from 'bullmq';
import { redisConnection } from './connection';

export const myQueue = new Queue('my-queue', {
  connection: redisConnection,
  defaultJobOptions: {
    attempts: 3,
    backoff: { type: 'exponential', delay: 1000 },
    removeOnComplete: 100,
    removeOnFail: 500,
  },
});

// Add job
await myQueue.add('process-item', {
  itemId: 'xxx',
  organizationId: 'yyy',
});
```

## WebSocket Patterns

Location: `src/websocket/`

```typescript
import { WebSocket } from 'ws';
import { logger } from '../utils/logger';

export function handleMyMessage(ws: WebSocket, data: any, userId: string) {
  const { itemId } = data;

  // Subscribe to updates
  subscriptions.set(ws, { itemId, userId });

  // Send confirmation
  ws.send(JSON.stringify({
    type: 'subscribed',
    data: { itemId },
  }));
}

export function broadcastUpdate(itemId: string, update: any) {
  for (const [ws, sub] of subscriptions) {
    if (sub.itemId === itemId && ws.readyState === WebSocket.OPEN) {
      ws.send(JSON.stringify({
        type: 'item.updated',
        data: update,
      }));
    }
  }
}
```

## Key Services Reference

| Service | Purpose | Location |
|---------|---------|----------|
| `orchestratorService` | Autonomous work picker (5s loop) | `src/services/orchestratorService.ts` |
| `healthMonitorService` | Stuck run detection (60s loop) | `src/services/healthMonitorService.ts` |
| `runService` | Agent run execution | `src/services/runService.ts` |
| `storyService` | Story CRUD + status | `src/services/storyService.ts` |
| `claudeAuthService` | OAuth token management | `src/services/claudeAuthService.ts` |
| `bugTriageService` | AI bug analysis | `src/services/bugTriageService.ts` |
| `k8sService` | Kubernetes pod lifecycle | `src/services/k8sService.ts` |
| `websocketService` | Real-time broadcasts | `src/services/websocketService.ts` |

## Testing

```bash
npm test                    # Run all tests
npm run test:watch          # Watch mode
npm test -- src/services/myService.test.ts  # Single file
```

Test file pattern: `*.test.ts` next to source file

```typescript
import { describe, it, expect, beforeEach } from 'vitest';
import { myService } from './myService';

describe('myService', () => {
  beforeEach(async () => {
    // Setup test data
  });

  it('should create item', async () => {
    const result = await myService.create('org-1', { name: 'test' });
    expect(result.name).toBe('test');
  });
});
```

## Environment Variables

Access via `src/config/config.ts`:
```typescript
import { config } from '../config/config';

const dbUrl = config.databaseUrl;
const redisUrl = config.redisUrl;
```

## Commands

```bash
npm run dev           # Start backend (port 3005)
npm run build         # Compile TypeScript
npm run db:migrate    # Apply migrations
npm run db:generate   # Generate migration from schema
npm run db:studio     # Drizzle Studio GUI
npm run typecheck     # Type validation
npm run lint          # ESLint
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
