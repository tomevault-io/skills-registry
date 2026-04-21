---
name: setup-deploy
description: Set up deployment pipeline and infrastructure for an application Use when this capability is needed.
metadata:
  author: luanps2
---

# Setup Deploy Skill

## Purpose

Configure automated deployment pipelines and infrastructure for reliable application delivery.

## Deployment Platforms

### Vercel (Frontend/Fullstack)

**For**: Next.js, React, Vue, Svelte, static sites

```bash
# Install Vercel CLI
npm install -g vercel

# Login
vercel login

# Deploy
vercel

# Deploy to production
vercel --prod
```

**Configuration**: `vercel.json`
```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "env": {
    "NEXT_PUBLIC_API_URL": "@api-url"
  }
}
```

### Render (Backend/Fullstack)

**For**: Node.js, Python, Docker

**Configuration**: `render.yaml`
```yaml
services:
  - type: web
    name: myapp-backend
    env: node
    buildCommand: npm install && npm run build
    startCommand: npm start
    envVars:
      - key: DATABASE_URL
        fromDatabase:
          name: myapp-db
          property: connectionString
      - key: JWT_SECRET
        generateValue: true
```

### Docker

**Dockerfile**:
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
```

**docker-compose.yml**:
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
      - NODE_ENV=production
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

## CI/CD Pipeline

### GitHub Actions

**.github/workflows/deploy.yml**:
```yaml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Run linter
        run: npm run lint

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
```

## Environment Variables

### Setup

1. **Development**: `.env.local` (gitignored)
2. **Staging**: Platform dashboard (Vercel, Render)
3. **Production**: Platform dashboard (encrypted)

**Example .env**:
```env
# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb

# Authentication
JWT_SECRET=your-secret-key-here
JWT_EXPIRES_IN=7d

# External Services
SENDGRID_API_KEY=your-sendgrid-key
STRIPE_SECRET_KEY=your-stripe-key

# App Config
NODE_ENV=production
PORT=3000
```

### Security

- ✅ **Never** commit `.env` files
- ✅ Use platform-specific secret management
- ✅ Rotate secrets regularly
- ✅ Use different secrets per environment

## Health Checks

**Endpoint**: `/health`
```typescript
app.get('/health', async (req, res) => {
  try {
    // Check database
    await db.query('SELECT 1');
    
    res.status(200).json({
      status: 'healthy',
      timestamp: new Date().toISOString(),
      uptime: process.uptime(),
      environment: process.env.NODE_ENV,
    });
  } catch (error) {
    res.status(500).json({
      status: 'unhealthy',
      error: error.message,
    });
  }
});
```

## Monitoring

### Application Monitoring

- **Sentry**: Error tracking
- **Datadog**: Performance monitoring
- **New Relic**: APM

**Setup Sentry**:
```typescript
import * as Sentry from '@sentry/node';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 1.0,
});
```

### Uptime Monitoring

- **UptimeRobot**: Free uptime monitoring
- **Pingdom**: Advanced monitoring
- **StatusCake**: Status pages

## Deployment Checklist

- [ ] All tests passing
- [ ] Environment variables configured
- [ ] Database migrations ready
- [ ] Health check endpoint working
- [ ] Monitoring and alerting configured
- [ ] Error tracking set up
- [ ] Backup strategy in place
- [ ] Rollback procedure documented
- [ ] SSL/HTTPS enabled
- [ ] Domain configured

## Rollback Procedure

### Vercel
```bash
# List deployments
vercel ls

# Roll back to specific deployment
vercel rollback <deployment-url>
```

### Docker
```bash
# Revert to previous image
docker pull myapp:previous-version
docker-compose up -d
```

### Database
```bash
# Run down migration
npm run migrate:down
```

## Related Skills

- `create_api_endpoint` - APIs to deploy
- `design_database_schema` - Database to deploy

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luanps2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
