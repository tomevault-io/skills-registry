---
name: deployment
description: Use when setting up or troubleshooting deployments to Vercel, Railway, or AWS. Covers environment configuration, rollback strategies, health checks, and zero-downtime deployment patterns.
metadata:
  author: canivel
---

# Deployment Patterns

## Vercel (Next.js / Static Sites)

```bash
# Deploy from CLI
npx vercel deploy            # preview deployment
npx vercel deploy --prod     # production deployment

# Link to project
npx vercel link

# Set environment variables
npx vercel env add DATABASE_URL production
npx vercel env add NEXT_PUBLIC_API_URL production
```

```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "nextjs",
  "regions": ["iad1"],
  "headers": [
    {
      "source": "/api/(.*)",
      "headers": [
        { "key": "Cache-Control", "value": "no-store" }
      ]
    }
  ],
  "rewrites": [
    { "source": "/api/:path*", "destination": "/api/:path*" }
  ]
}
```

### Vercel Rollback

```bash
# List deployments
npx vercel ls

# Promote a previous deployment to production
npx vercel promote <deployment-url>
```

## Railway (Node.js Backend)

```bash
# Deploy from CLI
railway login
railway link
railway up

# Set environment variables
railway variables set DATABASE_URL="postgresql://..."
railway variables set NODE_ENV=production
```

```json
// railway.json
{
  "build": {
    "builder": "NIXPACKS",
    "buildCommand": "npm ci && npm run build"
  },
  "deploy": {
    "startCommand": "node dist/server.js",
    "healthcheckPath": "/health",
    "healthcheckTimeout": 30,
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 3
  }
}
```

## AWS ECS (Container-Based)

```yaml
# task-definition.json
{
  "family": "otto-api",
  "containerDefinitions": [{
    "name": "api",
    "image": "ghcr.io/org/otto-api:latest",
    "portMappings": [{ "containerPort": 3000 }],
    "environment": [
      { "name": "NODE_ENV", "value": "production" }
    ],
    "secrets": [
      { "name": "DATABASE_URL", "valueFrom": "arn:aws:ssm:us-east-1:123:parameter/otto/db-url" }
    ],
    "healthCheck": {
      "command": ["CMD-SHELL", "wget -qO- http://localhost:3000/health || exit 1"],
      "interval": 30,
      "timeout": 5,
      "retries": 3,
      "startPeriod": 60
    },
    "logConfiguration": {
      "logDriver": "awslogs",
      "options": {
        "awslogs-group": "/ecs/otto-api",
        "awslogs-region": "us-east-1",
        "awslogs-stream-prefix": "api"
      }
    }
  }],
  "cpu": "256",
  "memory": "512"
}
```

## Environment Variables Management

```bash
# Local development
# .env.local (never committed)
DATABASE_URL=postgresql://localhost:5432/otto_dev
JWT_SECRET=dev-secret-change-in-prod

# .env.example (committed, documents required vars)
DATABASE_URL=
JWT_SECRET=
REDIS_URL=
SMTP_HOST=
```

```ts
// config.ts - validate env vars at startup
import { z } from 'zod';

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']),
  DATABASE_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  PORT: z.coerce.number().default(3000),
});

export const env = envSchema.parse(process.env);
// App crashes immediately if env vars are missing, not at first use
```

## Zero-Downtime Deployment

```yaml
# AWS ECS rolling update
deploymentConfiguration:
  maximumPercent: 200
  minimumHealthyPercent: 100
  deploymentCircuitBreaker:
    enable: true
    rollback: true
```

Key requirements:
1. Health check endpoint must return 200 when the app is ready to serve traffic.
2. Graceful shutdown: handle SIGTERM, finish in-flight requests, close DB connections.
3. Database migrations must be backward-compatible (additive only, no destructive changes during deployment).

```ts
// Graceful shutdown
process.on('SIGTERM', async () => {
  console.log('SIGTERM received. Shutting down gracefully...');
  server.close(async () => {
    await db.$client.end();
    process.exit(0);
  });
  // Force exit after timeout
  setTimeout(() => process.exit(1), 30000);
});
```

## Rollback Strategies

| Platform | Rollback Method |
|----------|----------------|
| Vercel | `vercel promote <previous-deployment-url>` |
| Railway | Redeploy previous commit from dashboard |
| AWS ECS | Update service to previous task definition revision |
| Docker | `docker service update --image org/app:previous-tag` |

## Health Check Endpoint

```ts
app.get('/health', async (req, res) => {
  const checks = {
    database: false,
    redis: false,
  };

  try {
    await db.execute(sql`SELECT 1`);
    checks.database = true;
  } catch {}

  try {
    await redis.ping();
    checks.redis = true;
  } catch {}

  const healthy = Object.values(checks).every(Boolean);
  res.status(healthy ? 200 : 503).json({
    status: healthy ? 'healthy' : 'degraded',
    checks,
    timestamp: new Date().toISOString(),
    version: process.env.APP_VERSION || 'unknown',
  });
});
```

## Anti-Patterns

- NEVER deploy without a health check endpoint. Load balancers need it to route traffic.
- NEVER run destructive database migrations during deployment. Use expand-contract pattern.
- NEVER store secrets in code, Docker images, or version control. Use platform secret managers.
- NEVER skip graceful shutdown handling. In-flight requests will be dropped.
- NEVER deploy directly to production without a staging environment or preview deployment.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
