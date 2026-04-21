---
name: deployment-workflow
description: Staging and production deployment using Laravel Deployer with health checks, cache management, and rollback support (HUMAN-ONLY execution) Use when this capability is needed.
metadata:
  author: hackur
---

# Deployment Workflow

**⚠️ CRITICAL**: This skill documents deployment processes for **understanding and troubleshooting only**.

**Claude Code CANNOT execute deployment commands** - these are HUMAN-ONLY operations that can cause outages or data loss if misused.

## When to Use

- Understanding deployment architecture
- Troubleshooting deployment failures
- Reviewing deployment logs
- Planning deployment strategy
- Documenting deployment procedures

**NOT for**: Actually executing deployments (human-only)

## Scripts Overview

### Production Deployment

**Script**: `./scripts/prod.sh`
**Purpose**: Manage production environment
**Restrictions**: HUMAN-ONLY (never run via Claude)

```bash
# Commands available (HUMAN executes these)
./scripts/prod.sh deploy          # Full deployment
./scripts/prod.sh rollback        # Rollback to previous release
./scripts/prod.sh ssh             # SSH to production server
./scripts/prod.sh logs            # View deployment logs
./scripts/prod.sh health          # Check production health
```

### Staging Deployment

**Script**: `./scripts/staging.sh`
**Purpose**: Manage staging environment
**Restrictions**: HUMAN-ONLY (can run destructive commands)

```bash
# Commands available (HUMAN executes these)
./scripts/staging.sh deploy       # Full deployment
./scripts/staging.sh fresh        # Reset database (DESTRUCTIVE)
./scripts/staging.sh seed         # Seed database
./scripts/staging.sh ssh          # SSH to staging server
./scripts/staging.sh health       # Check staging health
```

## Deployment Architecture

### DRY Library System

**Modular libraries** eliminate code duplication:

**Configuration** (`scripts/lib/config.sh`):
- Single source of truth for all environment configs
- Production: `$PROD_HOST`, `$PROD_IP`, `$PROD_USER`, `$PROD_PATH`
- Staging: `$STAGING_HOST`, `$STAGING_USER`, `$STAGING_PATH`
- Helper functions: `get_remote_connection()`, `get_health_url()`

**Deployment** (`scripts/lib/deployment.sh`):
- Parameterized functions work for both prod AND staging
- `health_check(env)` - Unified health checking
- `deploy_to_environment(env)` - Complete workflow
- `rollback_deployment(env)` - Safe rollbacks
- `verify_deployment_environment(env)` - Pre-deployment validation

**Benefits**:
✅ ~250 lines of duplicate code eliminated
✅ Fix once, works everywhere
✅ SOLID/DRY/KISS principles

## Deployment Flow (12 Steps)

### Complete Deployment Sequence

```
1. deploy:update_code              # Git pull latest code
2. deploy:shared                   # Link shared files (.env, storage)
3. deploy:composer:clear-cache     # Clear Composer cache (verbose logging)
4. deploy:vendors                  # Install PHP dependencies (--prefer-source)
5. deploy:verify:packages          # Verify critical packages exist
6. artisan:storage:link            # Link storage directory
7. artisan:view:cache              # Cache Blade views
8. artisan:config:cache            # Cache configuration
9. artisan:route:cache             # Cache routes
10. artisan:migrate                # Run database migrations
11. deploy:npm_build               # Build frontend assets
12. deploy:symlink                 # Switch to new release
```

**Total Time**: ~2-3 minutes per deployment

### Enhanced Logging (October 2025)

Every deployment includes verbose output:

```bash
📦 Clearing Composer cache...
  Cache directory: /home/deploy/.cache/composer
  Files remaining in cache: 0
✅ Composer cache cleared

🎼 Installing PHP dependencies with Composer...
  Using --prefer-source to force git clones for VCS packages
✅ Composer dependencies installed

🔍 Verifying critical package files...
  ✓ helpers.php exists (vendor/pcrcard/nova-medialibrary-bounding-box-field/src/helpers.php)
  ✓ nova-menus package exists
✅ Package verification passed
```

## Cache Management

### Automatic Composer Cache Clearing

**Why**: Prevents cache corruption for forked VCS packages
**When**: Every deployment (staging + production)
**Impact**: +5 seconds per deployment

**Implementation** (`deploy.php`):

```php
task('deploy:composer:clear-cache', function () {
    writeln('📦 Clearing Composer cache...');
    $cacheDir = run('{{bin/composer}} config cache-dir 2>/dev/null || echo "~/.cache/composer"');
    writeln('  Cache directory: ' . trim($cacheDir));
    run('{{bin/composer}} clear-cache');
    $files = run('find ' . trim($cacheDir) . ' -type f 2>/dev/null | wc -l || echo "0"');
    writeln('  Files remaining in cache: ' . trim($files));
    writeln('✅ Composer cache cleared');
});
```

### Prefer Source Installation

**Why**: Forces git clones for VCS packages (ensures complete files)
**When**: Every vendor installation
**Impact**: +30 seconds per deployment

**Implementation** (`deploy.php`):

```php
task('deploy:vendors', function () {
    writeln('🎼 Installing PHP dependencies with Composer...');
    writeln('  Using --prefer-source to force git clones for VCS packages');
    run('cd {{release_path}} && {{bin/composer}} install --prefer-source --no-dev --optimize-autoloader --no-interaction');
    writeln('✅ Composer dependencies installed');
});
```

**Affected Packages**:
- `pcrcard/nova-menus`
- `pcrcard/nova-medialibrary-bounding-box-field`

**Other Packages**: Use fast Packagist dist archives (not affected)

## Package Verification

### Early Error Detection (October 2025)

**New task** catches missing files before autoload errors:

```php
task('deploy:verify:packages', function () {
    writeln('🔍 Verifying critical package files...');

    // Check nova-medialibrary-bounding-box-field helpers.php
    $helpersPath = '{{release_path}}/vendor/pcrcard/nova-medialibrary-bounding-box-field/src/helpers.php';
    if (!test("[ -f $helpersPath ]")) {
        throw new \Exception("Missing helpers.php in nova-medialibrary-bounding-box-field package!");
    }
    writeln('  ✓ helpers.php exists');

    // Check nova-menus package
    $menuPath = '{{release_path}}/vendor/pcrcard/nova-menus';
    if (!test("[ -d $menuPath ]")) {
        throw new \Exception("Missing nova-menus package!");
    }
    writeln('  ✓ nova-menus package exists');

    writeln('✅ Package verification passed');
});
```

**Benefits**:
✅ Fails early with clear diagnostics
✅ Prevents cascading autoload errors
✅ Shows exactly what's missing
✅ +2 seconds deployment time (worth it)

## Health Checks

### Automated Health Verification

**Function**: `health_check(env)`
**Purpose**: Verify application is responding after deployment
**Retry Logic**: 3 attempts with 5-second delays

```bash
# Staging health check
health_check "staging"
# Checks: https://staging.pcrcard.com/health

# Production health check
health_check "production"
# Checks: https://pcrcard.com/health
```

**Expected Response**:

```json
{
  "status": "ok",
  "services": {
    "database": "connected",
    "cache": "connected",
    "storage": "writable"
  }
}
```

**On Failure**: Deployment stops, rollback recommended

## Rollback Support

### Safe Recovery

**Function**: `rollback_deployment(env)`
**Purpose**: Revert to previous working release
**Preserves**: Database, uploaded files, .env configuration

```bash
# Rollback staging
./scripts/staging.sh rollback

# Rollback production (HUMAN-ONLY)
./scripts/prod.sh rollback
```

**How It Works**:

1. Deployer keeps last 3 releases in `releases/` directory
2. `current/` symlink points to active release
3. Rollback changes symlink to previous release
4. Takes ~5 seconds (no code download needed)

**Rollback Flow**:

```
releases/
  20251028120000/  ← Previous (rollback target)
  20251028140000/  ← Current (broken)

current/ → 20251028140000  # Before rollback
current/ → 20251028120000  # After rollback
```

## Performance Impact

### Reliability vs Speed Trade-off

| Operation | Time | Benefit |
|-----------|------|---------|
| Cache clear | +5s | Prevents corruption |
| Prefer source | +30s | Complete file integrity |
| Package verification | +2s | Early error detection |
| **Total** | **+37s** | **100% reliable deployments** |

**Trade-off**: Acceptable 37-second overhead for zero manual intervention

## Emergency Production Fix

### Manual Cache Clearing Script

**When to use**: Production deployment fails with package errors

**Script**: `./scripts/immediate-production-fix.sh`

```bash
#!/bin/bash
# Emergency production cache fix (HUMAN-ONLY)

echo "🚨 Emergency Production Cache Fix"
echo "=================================="
echo ""

# SSH to production and clear cache
ssh deploy@production.pcrcard.com << 'EOF'
  echo "📦 Clearing Composer cache..."
  composer clear-cache

  echo "📊 Cache status..."
  composer config cache-dir
  ls -la $(composer config cache-dir)

  echo "✅ Cache cleared"
EOF

echo ""
echo "📋 Next steps:"
echo "1. Retry deployment: ./scripts/prod.sh deploy"
echo "2. Verify packages: ssh deploy@production 'ls -la current/vendor/pcrcard/'"
echo "3. Check health: ./scripts/prod.sh health"
```

## Common Issues

### Issue 1: Missing Package Files

**Symptom**: `helpers.php not found` autoload error

**Cause**: Composer cache corruption

**Solution**:

```bash
# 1. Clear cache on server
ssh deploy@staging "composer clear-cache"

# 2. Redeploy with --prefer-source
./scripts/staging.sh deploy
```

### Issue 2: Deployment Timeout

**Symptom**: Deployment hangs at composer install

**Cause**: Network issues, large dependencies

**Solution**:

```bash
# Increase timeout in deploy.php
set('deploy_timeout', 600);  // 10 minutes

# Or manually SSH and install
ssh deploy@staging
cd releases/20251028140000
composer install --prefer-source
```

### Issue 3: Failed Health Check

**Symptom**: Health endpoint returns 500 error

**Cause**: Cache issues, permissions, database

**Solution**:

```bash
# SSH to server
./scripts/staging.sh ssh

# Check logs
tail -f storage/logs/laravel.log

# Clear application cache
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear

# Fix permissions
chmod -R 775 storage bootstrap/cache
chown -R deploy:www-data storage bootstrap/cache
```

## Documentation Links

- **Deployment Guide**: `docs/deployment/DEPLOYMENT-GUIDE.md`
- **Deployment Checklist**: `docs/deployment/DEPLOYMENT-CHECKLIST.md`
- **Latest Fix**: `docs/ci-cd/deployment/fixes/2025-10-24-production-deployment-enhancement.md`
- **CI/CD Hub**: `docs/ci-cd/README.md`
- **Laravel Deployer**: https://deployer.org/docs/7.x/getting-started

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hackur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
