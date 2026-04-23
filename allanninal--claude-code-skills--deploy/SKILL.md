---
name: deploy
description: Deploy application to specified environment with pre-checks and rollback capability. Use for production deployments, staging updates, or release management. Use when this capability is needed.
metadata:
  author: allanninal
---

# Automated Deployment

Deploy applications with pre-deployment checks, health validation, and automatic rollback on failure.

## Arguments
- `$0`: Environment - `production`, `staging`, `qa`, `development` (required)

## Pre-Deployment Checklist

Before deploying, verify:

1. **Git Status Clean**
   ```bash
   git status --porcelain
   # Must be empty or only untracked files
   ```

2. **On Correct Branch**
   - production: `main` or `master`
   - staging: `staging` or `DEVELOPMENT`
   - qa: `qa` or `DEVELOPMENT`

3. **Tests Pass**
   ```bash
   # Run test suite first
   /test all
   ```

4. **Environment Variables**
   - Check `.env.$ENVIRONMENT` exists
   - Validate required variables are set

## Deployment Strategies by Project

### FastAPI Projects (IONOS VPS)

**eruditiontx-services-mvp, mathmatterstx-services:**

```bash
# 1. Connect to server
ssh user@server

# 2. Pull latest code
cd /path/to/project
git pull origin $BRANCH

# 3. Install dependencies
uv sync

# 4. Run migrations (if any)
uv run alembic upgrade head

# 5. Restart service
sudo systemctl restart erudition-service

# 6. Health check
curl -f http://localhost:8000/health || exit 1

# 7. Verify logs
journalctl -u erudition-service -n 20 --no-pager
```

### Next.js Projects (Vercel)

**bocs-turbo apps, naiomi-frontend:**

```bash
# Using Vercel CLI
vercel --prod
# or for preview
vercel
```

### Docker Projects

**agila-tax-management:**

```bash
# 1. Build new image
docker-compose -f docker-compose.$ENV.yml build

# 2. Stop old containers
docker-compose -f docker-compose.$ENV.yml down

# 3. Start new containers
docker-compose -f docker-compose.$ENV.yml up -d

# 4. Health check
docker-compose -f docker-compose.$ENV.yml ps
curl -f http://localhost:PORT/health
```

### AWS Lambda (Serverless)

**bocs-serverless:**

```bash
cd bocs-serverless
./deployment.sh $ENVIRONMENT
```

## Rollback Procedure

If deployment fails:

1. **Git Rollback**
   ```bash
   git checkout $PREVIOUS_COMMIT
   ```

2. **Restart Services**
   ```bash
   sudo systemctl restart $SERVICE
   # or
   docker-compose -f docker-compose.$ENV.yml up -d
   ```

3. **Verify Rollback**
   ```bash
   curl -f http://localhost:PORT/health
   ```

## Post-Deployment

1. **Verify Health Endpoints**
   ```bash
   curl http://localhost:PORT/health
   curl http://localhost:PORT/v1/health
   ```

2. **Check Logs for Errors**
   ```bash
   journalctl -u $SERVICE -n 50 --no-pager | grep -i error
   # or
   docker logs $CONTAINER --tail 50
   ```

3. **Send Notification** (if configured)
   - Slack webhook
   - Email notification

## Output Format

```
Deployment: [project-name]
Environment: [production/staging/qa]
Branch: [branch-name]
Commit: [short-hash]

Pre-checks:
  Git status: CLEAN
  Branch: CORRECT
  Tests: PASSED
  Env vars: VALIDATED

Deploying...
[Deployment output]

Post-deployment:
  Health check: PASSED
  Service status: RUNNING

Deployment completed successfully!
URL: [deployed-url]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
