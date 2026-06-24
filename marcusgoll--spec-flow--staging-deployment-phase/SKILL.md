---
name: staging-deployment-phase
description: Deploys features to staging environment with database migrations, health checks, and deployment verification. Use during /ship-staging phase or when deploying to staging environments for validation before production release.
metadata:
  author: marcusgoll
---

<objective>
Deploy features to staging environment with database migrations, health checks, and automated deployment verification, ensuring features are validated in a production-like environment before final release.
</objective>

<quick_start>
<staging_deployment_workflow>
**Prerequisite-driven staging deployment:**

1. **Verify PR merged**: Confirm pull request merged to main branch
2. **Run migrations**: Execute database migrations if schema changes exist
3. **Trigger deployment**: Deploy to staging environment via CI/CD or manual trigger
4. **Monitor deployment**: Watch deployment logs and progress
5. **Run health checks**: Verify all services and endpoints operational
6. **Generate report**: Create staging-ship-report.md with deployment metadata

**Example workflow:**

```
Verifying PR merged... ✅
  Merge commit: abc123f "Merge pull request #47"

Checking for migrations...
  Found: migrations/2025_11_add_user_profile.sql
  Running migrations... ✅

Triggering staging deployment...
  Platform: Vercel/Netlify/Railway
  Deployment ID: dpl_abc123
  Status: Building... → Deploying... → Live ✅

Running health checks...
  API: https://staging.example.com/health → 200 OK ✅
  Database: Connected ✅
  Cache: Operational ✅

Staging deployment successful!
Ship report: specs/NNN-slug/staging-ship-report.md
Staging URL: https://staging.example.com
```

</staging_deployment_workflow>

<trigger_conditions>
**Auto-invoke when:**

- `/ship-staging` command executed
- User mentions "deploy to staging", "ship to staging", "staging deployment"
- state.yaml shows current_phase: ship-staging
- Deployment model is staging-prod (not direct-prod or local-only)

**Prerequisites:**

- PR successfully merged to main branch
- CI/CD passing on main branch
- Staging environment configured
- Database migrations prepared (if needed)
  </trigger_conditions>
  </quick_start>

<workflow>
<step number="1" name="verify_pr_merged">
**1. Verify PR Merged to Main**

Confirm pull request successfully merged before deploying.

**Validation:**

```bash
# Check recent commits for merge commit
git log --oneline -10 | grep "Merge pull request"

# Verify feature branch merged
FEATURE_SLUG="user-profile-editing"
git log --oneline --all | grep -i "$FEATURE_SLUG"

# Confirm on main branch
CURRENT_BRANCH=$(git branch --show-current)
if [ "$CURRENT_BRANCH" != "main" ]; then
  echo "❌ Not on main branch (currently on $CURRENT_BRANCH)"
  echo "Switch to main: git checkout main && git pull"
  exit 1
fi

echo "✅ PR merged to main"
```

**Blocking conditions:**

- Not on main branch → Switch to main first
- No recent merge commit → Feature branch not merged yet
- CI failing on main → Fix CI before deploying

**Output:**

```
✅ PR merged to main
  Merge commit: abc123f "Merge pull request #47: Add user profile editing"
  Author: @username
  Merged: 2 minutes ago
```

</step>

<step number="2" name="run_database_migrations">
**2. Run Database Migrations**

Execute database migrations if schema changes exist.

**Detection:**

```bash
# Check for migration files (Node.js example)
if [ -d "migrations" ] && [ -n "$(ls -A migrations/*.sql 2>/dev/null)" ]; then
  echo "✅ Migrations detected"

  # List migrations
  ls -1 migrations/*.sql

  # Run migrations
  npm run migrate:staging

  # Or with migration tool
  # knex migrate:latest --env staging
  # prisma migrate deploy --schema=./schema.prisma

  # Verify migrations applied
  echo "✅ Migrations completed"
else
  echo "ℹ️  No migrations to run"
fi
```

**Error handling:**

```bash
# If migration fails
if [ $? -ne 0 ]; then
  echo "❌ Migration failed"
  echo "Review migration logs for errors"
  echo "DO NOT proceed with deployment"
  echo ""
  echo "Common issues:"
  echo "  - Syntax errors in SQL"
  echo "  - Constraint violations"
  echo "  - Missing columns in dependent tables"
  exit 1
fi
```

**Rollback procedure:**

```bash
# If deployment fails after migration
# Rollback migrations
npm run migrate:rollback:staging

# Or manually revert last migration
# psql -h staging-db.example.com -d mydb -f migrations/rollback/2025_11_revert.sql
```

</step>

<step number="3" name="trigger_staging_deployment">
**3. Trigger Staging Deployment**

Deploy to staging environment based on deployment platform.

**Platform-specific commands:**

**Vercel:**

```bash
# Deploy to staging (preview environment)
vercel --yes

# Or trigger via git push
git push origin main
# Vercel auto-deploys main → staging
```

**Netlify:**

```bash
# Deploy to staging environment
netlify deploy --prod=false

# Or via git push to staging branch
git push origin main:staging
```

**Railway:**

```bash
# Trigger deployment
railway up --environment staging
```

**GitHub Actions:**

```bash
# Trigger workflow
gh workflow run deploy-staging.yml

# Monitor workflow
gh run watch
```

**Docker/Custom:**

```bash
# Build and deploy
docker build -t myapp:staging .
docker push registry.example.com/myapp:staging

# SSH to staging server
ssh staging.example.com
docker pull registry.example.com/myapp:staging
docker-compose up -d
```

**Monitor deployment:**

```bash
# Get deployment ID/URL
DEPLOYMENT_ID="dpl_abc123xyz"
STAGING_URL="https://staging.example.com"

echo "Deployment ID: $DEPLOYMENT_ID"
echo "Staging URL: $STAGING_URL"
echo ""
echo "Monitoring deployment..."
# Platform-specific monitoring commands
```

</step>

<step number="4" name="run_health_checks">
**4. Run Health Checks**

Verify all services and endpoints operational after deployment.

**Critical checks:**

```bash
echo "Running health checks..."

# Check 1: API health endpoint
API_STATUS=$(curl -s -o /dev/null -w "%{http_code}" "$STAGING_URL/health")
if [ "$API_STATUS" = "200" ]; then
  echo "  ✅ API: $API_STATUS OK"
else
  echo "  ❌ API: $API_STATUS FAILED"
  HEALTH_FAILED=true
fi

# Check 2: Database connectivity
DB_STATUS=$(curl -s "$STAGING_URL/health" | jq -r '.database')
if [ "$DB_STATUS" = "connected" ]; then
  echo "  ✅ Database: Connected"
else
  echo "  ❌ Database: $DB_STATUS"
  HEALTH_FAILED=true
fi

# Check 3: Cache/Redis
CACHE_STATUS=$(curl -s "$STAGING_URL/health" | jq -r '.cache')
if [ "$CACHE_STATUS" = "operational" ]; then
  echo "  ✅ Cache: Operational"
else
  echo "  ❌ Cache: $CACHE_STATUS"
  HEALTH_FAILED=true
fi

# Check 4: Version matches
DEPLOYED_VERSION=$(curl -s "$STAGING_URL/api/version" | jq -r '.version')
EXPECTED_VERSION=$(node -p "require('./package.json').version")
if [ "$DEPLOYED_VERSION" = "$EXPECTED_VERSION" ]; then
  echo "  ✅ Version: $DEPLOYED_VERSION"
else
  echo "  ⚠️  Version mismatch: deployed=$DEPLOYED_VERSION, expected=$EXPECTED_VERSION"
fi

# Fail if any health check failed
if [ "$HEALTH_FAILED" = "true" ]; then
  echo ""
  echo "❌ Health checks failed - deployment may be broken"
  echo "Review logs and consider rollback"
  exit 1
fi

echo ""
echo "✅ All health checks passed"
```

**Health check endpoints:**

- `/health` - Overall system health
- `/api/version` - Deployed version
- `/api/status` - Detailed service status
- `/api/db/ping` - Database connectivity
  </step>

<step number="5" name="generate_ship_report">
**5. Generate Staging Ship Report**

Create staging-ship-report.md with deployment metadata.

**Report generation:**

```bash
FEATURE_SLUG="user-profile-editing"
REPORT_PATH="specs/001-$FEATURE_SLUG/staging-ship-report.md"

cat > "$REPORT_PATH" <<EOF
# Staging Ship Report: $FEATURE_SLUG

## Deployment Summary
- **Deployed at**: $(date -Iseconds)
- **Deployment ID**: $DEPLOYMENT_ID
- **Staging URL**: $STAGING_URL
- **Branch**: main
- **Commit**: $(git rev-parse HEAD)
- **Author**: $(git log -1 --format='%an <%ae>')

## Health Checks
- API endpoints: ✅ All passing
- Database: ✅ Connected
- Cache: ✅ Operational
- Version: ✅ $DEPLOYED_VERSION

## Database Migrations
$(if [ -d "migrations" ]; then
  echo "- Migrations run: $(ls migrations/*.sql | wc -l) files"
  ls -1 migrations/*.sql | sed 's/^/  - /'
else
  echo "- No migrations required"
fi)

## Next Steps
- Manual validation: Test feature on staging environment
- Run: /validate-staging to approve for production
- Staging URL: $STAGING_URL

## Rollback Procedure
1. Revert deployment: [platform-specific command]
2. Rollback migrations (if any): npm run migrate:rollback:staging
3. Verify rollback successful

---

Generated by /ship-staging on $(date)
EOF

echo "✅ Ship report generated: $REPORT_PATH"
```

**Update workflow state:**

```bash
# Update state.yaml
STATE_FILE="specs/001-$FEATURE_SLUG/state.yaml"

# Add deployment metadata
yq eval ".deployment.staging.url = \"$STAGING_URL\"" -i "$STATE_FILE"
yq eval ".deployment.staging.deployment_id = \"$DEPLOYMENT_ID\"" -i "$STATE_FILE"
yq eval ".deployment.staging.deployed_at = \"$(date -Iseconds)\"" -i "$STATE_FILE"
yq eval ".current_phase = \"validate-staging\"" -i "$STATE_FILE"

echo "✅ Workflow state updated"
```

</step>

<step number="6" name="display_summary">
**6. Display Deployment Summary**

Show deployment results and next steps.

**Summary format:**

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🚀 Staging Deployment Successful!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Staging URL: https://staging.example.com
Deployment ID: dpl_abc123xyz
Deployed at: 2025-11-19T22:45:00Z

Health Checks: ✅ All passing
  - API: 200 OK
  - Database: Connected
  - Cache: Operational
  - Version: 2.7.0

Ship Report: specs/001-user-profile-editing/staging-ship-report.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Next: Manual Validation
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Test feature on staging: https://staging.example.com
2. Verify all acceptance criteria met
3. Run: /validate-staging to approve for production

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

</step>
</workflow>

<anti_patterns>
**Avoid these staging deployment mistakes:**

<pitfall name="deploy_without_pr_merge_verification">
**❌ Deploying without verifying PR merged**
```bash
# BAD: Skip merge check, deploy immediately
/ship-staging
# May deploy incomplete feature if PR not merged
```
**✅ Always verify PR merged before deploying**
```bash
# GOOD: Check for merge commit
git log --oneline -10 | grep "Merge pull request"
# Confirm merge exists, then deploy
```
**Impact**: Deploying feature branch code instead of merged code, missing integration changes
**Prevention**: Always check git log for merge commit before triggering deployment
</pitfall>

<pitfall name="skip_database_migrations">
**❌ Deploying with schema changes without running migrations**
```bash
# BAD: Added new column to User table, forgot to run migration
git push origin main  # Deploys code expecting new column
# Staging crashes: "column 'profile_picture' does not exist"
```
**✅ Always check for and run migrations before deployment**
```bash
# GOOD: Check for migration files
if [ -d "migrations" ] && [ -n "$(ls migrations/*.sql)" ]; then
  npm run migrate:staging  # Run migrations first
fi
# Then deploy code
```
**Impact**: Application crashes due to schema mismatch, broken staging environment
**Prevention**: Make migrations part of deployment checklist, run before code deployment
</pitfall>

<pitfall name="ignore_health_check_failures">
**❌ Assuming deployment succeeded without verification**
```bash
# BAD: Deploy and assume success
vercel --yes
echo "Deployed!" # But didn't check if it actually works
```
**✅ Always run health checks after deployment**
```bash
# GOOD: Verify deployment with health checks
vercel --yes
sleep 10  # Wait for deployment to stabilize

# Check health endpoint

curl -f https://staging.example.com/health || {
echo "❌ Health check failed - deployment broken"
exit 1
}

````
**Impact**: Broken staging deployment goes unnoticed, blocks validation
**Prevention**: Always run automated health checks, verify 200 status and service health
</pitfall>

<pitfall name="deploy_on_failing_ci">
**❌ Deploying when CI is failing on main**
```bash
# BAD: CI shows red, deploy anyway
# "Tests failing but it's probably fine..."
/ship-staging
````

**✅ Always wait for CI to pass before deploying**

```bash
# GOOD: Check CI status first
gh run list --branch main --limit 1 --json conclusion --jq '.[0].conclusion'
# If "success", proceed with deployment
# If "failure", fix CI first
```

**Impact**: Deploying broken code to staging, wasting time debugging obvious failures
**Prevention**: Make CI passing a prerequisite gate for staging deployment
</pitfall>

<pitfall name="no_rollback_plan">
**❌ Deploying without knowing how to rollback**
```bash
# BAD: Deploy, hope it works
vercel --yes
# If broken, panic and don't know how to revert
```
**✅ Document rollback procedure before deploying**
```bash
# GOOD: Know rollback command for your platform
# Vercel: vercel rollback <deployment-url>
# Netlify: netlify rollback
# Railway: railway rollback

# Test rollback works during /ship-staging

# Before /ship-prod

````
**Impact**: Unable to recover quickly from broken deployment, extended downtime
**Prevention**: Document and test rollback procedure, include in ship report
</pitfall>

<pitfall name="missing_deployment_metadata">
**❌ Not recording deployment details**
```bash
# BAD: Deploy but don't save deployment ID, URL, timestamp
vercel --yes
# Later: "What version is on staging? When was it deployed?"
````

**✅ Always generate ship report with metadata**

```bash
# GOOD: Capture and save deployment info
DEPLOYMENT_ID=$(vercel --yes | grep -oP 'https://[^ ]+')
echo "Deployment ID: $DEPLOYMENT_ID" >> staging-ship-report.md
echo "Deployed at: $(date -Iseconds)" >> staging-ship-report.md
```

**Impact**: No audit trail, can't correlate staging issues with deployments
**Prevention**: Always generate staging-ship-report.md with full metadata
</pitfall>
</anti_patterns>

<success_criteria>
**Staging deployment successful when:**

- ✓ PR successfully merged to main branch (verified via git log)
- ✓ CI/CD passing on main branch
- ✓ Database migrations completed without errors (if applicable)
- ✓ Staging deployment triggered and completed successfully
- ✓ Deployment ID/URL captured
- ✓ All health check endpoints return 200 status
- ✓ API, database, cache services confirmed operational
- ✓ Deployed version matches expected version
- ✓ staging-ship-report.md generated in specs/NNN-slug/ directory
- ✓ state.yaml updated with staging deployment metadata
- ✓ Next action displayed (/validate-staging)

**Quality gates passed:**

- CI green on main branch before deployment
- No migration errors during schema updates
- All health checks passing post-deployment
- No critical errors in deployment logs
- Rollback procedure documented and tested
  </success_criteria>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marcusgoll) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
