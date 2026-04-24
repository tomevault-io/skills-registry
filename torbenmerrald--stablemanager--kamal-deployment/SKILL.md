---
name: kamal-deployment
description: Deploy Jmix/Vaadin applications with Kamal 2 to production servers. Use this skill when setting up Kamal configuration for new projects. Use this skill when deploying new versions to production. Use this skill when configuring bootBuildImage with required Kamal labels. Use this skill when troubleshooting deployment issues. Use this skill when working with PostgreSQL accessories in Kamal. Use when this capability is needed.
metadata:
  author: torbenmerrald
---

# Kamal Deployment

## When to use this skill

- When setting up Kamal 2 deployment configuration for the first time
- When deploying new versions to dev or production
- When configuring Dockerfile builds with required Kamal labels
- When troubleshooting Kamal deployment issues
- When working with PostgreSQL as a Kamal accessory
- When restoring database backups to servers
- When migrating from bootBuildImage to Dockerfile builds

## Project Configuration

### File Structure

```
config/
  deploy.yml          # Main Kamal configuration (shared settings)
  deploy.dev.yml      # Dev-specific overrides (servers, hosts)
  deploy.prod.yml     # Prod-specific overrides (servers, hosts)
.kamal/
  secrets.dev         # Dev environment secrets (gitignored)
  secrets.prod        # Prod environment secrets (gitignored)
  hooks/
    pre-deploy        # Pre-deploy backup script
    post-deploy       # Post-deploy cron setup script
```

### config/deploy.yml

```yaml
service: stablemanager
image: torbenmerrald/stablemanager

servers:
  web:
    hosts:
      - 65.109.138.192

registry:
  server: docker.io
  username: torbenmerrald
  password:
    - KAMAL_REGISTRY_PASSWORD

builder:
  arch: amd64

env:
  clear:
    SPRING_PROFILES_ACTIVE: prod
    SQL_URL: jdbc:postgresql://stablemanager-db:5432/stablemanager
    SPRING_QUARTZ_PROPERTIES_ORG_QUARTZ_JOBSTORE_DRIVERDELEGATECLASS: org.quartz.impl.jdbcjobstore.PostgreSQLDelegate
  secret:
    - SQL_USERNAME
    - SQL_PASSWORD
    - S3_ACCESSKEY
    - S3_SECRET_ACCESSKEY
    - S3_REGION
    - S3_BUCKET
    - S3_ENDPOINT
    - SMS_GATEWAYAPI_KEY
    - SMS_GATEWAYAPI_SECRET
    - OPENAI_API_KEY

deploy_timeout: 120

proxy:
  ssl: false
  app_port: 8080
  healthcheck:
    path: /
    interval: 5
    timeout: 10

accessories:
  db:
    image: postgres:16
    host: 65.109.138.192
    port: 5432
    env:
      secret:
        - POSTGRES_PASSWORD
      clear:
        POSTGRES_USER: stablemanager
        POSTGRES_DB: stablemanager
    directories:
      - data:/var/lib/postgresql/data
```

### build.gradle - bootBuildImage Configuration

```groovy
bootBuildImage {
    imageName = 'torbenmerrald/stablemanager:1.241'
    publish = false
    imagePlatform = 'linux/amd64'
    environment = [
            "BP_IMAGE_LABELS"  : "service=stablemanager",  // REQUIRED for Kamal
            "BP_OCI_LAYOUT"    : "true",
            "BP_APT_ENABLED"   : "true",
            "BP_JVM_HEAP_SIZE" : "1024m",
            "JAVA_TOOL_OPTIONS": "-Xmx1024m -Xms512m"
    ]
}
```

**Critical**: Use `BP_IMAGE_LABELS` (not `BP_OCI_LABELS`) for the service label. Kamal requires this label to identify containers.

## Multi-Destination Deployment

This project uses destination-based configuration for dev and prod environments.

### Destination Config Files

**config/deploy.dev.yml** - Dev environment overrides:
```yaml
servers:
  web:
    hosts:
      - dev.stablemanager.dk

proxy:
  host: dev.stablemanager.dk

accessories:
  db:
    host: dev.stablemanager.dk
  keycloak:
    host: dev.stablemanager.dk
    env:
      clear:
        KC_HOSTNAME: auth-dev.stablemanager.dk
    proxy:
      host: auth-dev.stablemanager.dk
  dozzle:
    host: dev.stablemanager.dk
    proxy:
      host: logs-dev.stablemanager.dk
```

**config/deploy.prod.yml** - Production environment overrides:
```yaml
servers:
  web:
    hosts:
      - 46.62.223.123

proxy:
  host: stablemanager.dk

accessories:
  db:
    host: 46.62.223.123
  keycloak:
    host: 46.62.223.123
    env:
      clear:
        KC_HOSTNAME: auth.stablemanager.dk
    proxy:
      host: auth.stablemanager.dk
  dozzle:
    host: 46.62.223.123
    proxy:
      host: logs.stablemanager.dk
```

## Deployment Workflow

### Deploy to Dev

```bash
# Full deploy (builds, pushes, deploys)
kamal deploy -d dev

# Deploy existing image (skip build)
kamal deploy -d dev --skip-push
```

### Deploy to Production

```bash
# Full deploy
kamal deploy -d prod

# Deploy existing image
kamal deploy -d prod --skip-push
```

### First-Time Server Setup

```bash
# Install Docker and deploy all accessories (db, keycloak, dozzle)
kamal setup -d dev    # For dev server
kamal setup -d prod   # For prod server
```

### Boot Accessories Separately

```bash
# Boot specific accessory
kamal accessory boot keycloak -d prod
kamal accessory boot dozzle -d prod

# Reboot accessory (re-register with proxy)
kamal accessory reboot keycloak -d prod
```

### Useful Commands

```bash
# View application logs
kamal app logs
kamal app logs --since 5m

# Access container shell
kamal app exec -i bash

# Check deployment status
kamal details

# Rollback to previous version
kamal rollback

# Remove old containers/images
kamal prune all
```

## Common Issues and Solutions

### Quartz PostgreSQL Error

**Error**: `Bad value for type long` or Quartz job store errors

**Solution**: Add PostgreSQL driver delegate in deploy.yml env:
```yaml
env:
  clear:
    SPRING_QUARTZ_PROPERTIES_ORG_QUARTZ_JOBSTORE_DRIVERDELEGATECLASS: org.quartz.impl.jdbcjobstore.PostgreSQLDelegate
```

### Missing Service Label

**Error**: `Image is missing the 'service' label`

**Solution**: Ensure `BP_IMAGE_LABELS` is set in bootBuildImage:
```groovy
environment = [
    "BP_IMAGE_LABELS": "service=stablemanager",
    // ... other settings
]
```

### Health Check Timeout

**Error**: Container fails health check during slow startup

**Solution**: Increase `deploy_timeout` in deploy.yml:
```yaml
deploy_timeout: 120  # seconds
```

### Docker Disk Space

**Error**: `no space left on device` during build

**Solution**: Clean up Docker:
```bash
docker system prune -af --volumes
```

### Host Settings Conflict with Another Service

**Error**: `Error: host settings conflict with another service`

**Cause**: Old proxy registrations conflict with new deployment (e.g., mixing dev/prod on same server, or stale registrations from previous deploys).

**Solution**: Remove stale proxy registrations:
```bash
# Check current registrations
ssh root@<server> "docker exec kamal-proxy kamal-proxy list"

# Remove conflicting service
ssh root@<server> "docker exec kamal-proxy kamal-proxy remove <service-name>"

# Example: Remove all stale registrations
ssh root@<server> "docker exec kamal-proxy kamal-proxy remove stablemanager-web"
ssh root@<server> "docker exec kamal-proxy kamal-proxy remove stablemanager-keycloak"
ssh root@<server> "docker exec kamal-proxy kamal-proxy remove stablemanager-dozzle"

# Then redeploy
kamal deploy -d prod --skip-push
```

### Accessory Not Registered with Proxy

**Symptom**: Accessory container is running but not accessible via its hostname.

**Solution**: Reboot the accessory to re-register with kamal-proxy:
```bash
# If reboot fails due to "service not found", remove and boot fresh:
ssh root@<server> "docker rm <accessory-container-name>"
kamal accessory boot <accessory> -d prod
```

## Database Operations

### Restore Database from Backup

When restoring a backup from a different environment (e.g., DigitalOcean managed database):

```bash
# 1. Stop the application first
ssh root@<server> "docker stop <app-container>"

# 2. Copy backup to server
scp /path/to/backup.bak root@<server>:/tmp/

# 3. Copy into database container and restore
ssh root@<server> "docker cp /tmp/backup.bak stablemanager-db:/tmp/"
ssh root@<server> "docker exec stablemanager-db pg_restore -U stablemanager -d stablemanager --clean --if-exists --no-owner /tmp/backup.bak"

# 4. Start the application
kamal deploy -d prod --skip-push
```

**Note**: Use `--no-owner` to avoid errors about missing roles (e.g., `doadmin` from DigitalOcean).

### Liquibase Changelog Path Migration

When migrating from `bootBuildImage` to Dockerfile builds, the Liquibase changelog paths stored in `databasechangelog` table need updating.

**bootBuildImage format**: `BOOT-INF/classes/dk/merrald/sm/liquibase/...`
**Dockerfile format**: `dk/merrald/sm/liquibase/...`

```bash
# Update all changelog paths in database
ssh root@<server> "docker exec stablemanager-db psql -U stablemanager -d stablemanager -c \"UPDATE databasechangelog SET filename = REPLACE(filename, 'BOOT-INF/classes/', '') WHERE filename LIKE 'BOOT-INF/classes/%';\""

# Verify the update
ssh root@<server> "docker exec stablemanager-db psql -U stablemanager -d stablemanager -c \"SELECT filename FROM databasechangelog WHERE filename LIKE 'dk/merrald/sm/%' LIMIT 5;\""
```

**Why this is needed**: bootBuildImage creates an exploded JAR structure, while Dockerfile with `java -jar` uses nested JAR. Liquibase records the classpath location of changelog files, which differs between these approaches.

## Pre-Deploy Backup Hook

The `.kamal/hooks/pre-deploy` script creates database backups before each deployment:

```bash
#!/bin/bash
set -e

# Read server IP from deploy.yml
SERVER=$(grep -A2 "^  web:" config/deploy.yml | grep -oE '[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+' | head -1)

if [ -z "$SERVER" ]; then
  echo "Could not determine server IP from deploy.yml"
  exit 1
fi

echo "Creating database backup before deploy on $SERVER..."
ssh root@$SERVER "mkdir -p /opt/backups && docker exec stablemanager-db pg_dump -U stablemanager stablemanager 2>/dev/null | gzip > /opt/backups/pre-deploy-\$(date +%Y%m%d_%H%M%S).sql.gz" || echo "Backup skipped (first deploy or no db yet)"
```

## Post-Deploy Cron Setup Hook

The `.kamal/hooks/post-deploy` script ensures a daily backup cron job exists on the server with S3 upload:

```bash
#!/bin/bash
set -e

DEST="${KAMAL_DESTINATION:-}"
S3_BUCKET="stablemanager-${DEST}"

ssh root@$SERVER "crontab -l 2>/dev/null | grep -v 'stablemanager-db pg_dump' | crontab - 2>/dev/null; (crontab -l 2>/dev/null; echo \"0 3 * * * BACKUP=daily-\\\$(date +\\%Y\\%m\\%d).sql.gz && docker exec stablemanager-db pg_dump -U stablemanager stablemanager 2>/dev/null | gzip > /opt/backups/\\\$BACKUP && s3cmd put /opt/backups/\\\$BACKUP s3://$S3_BUCKET/backups/\\\$BACKUP && find /opt/backups -name \\\"daily-*.sql.gz\\\" -mtime +7 -delete\") | crontab -"
```

**What it does**:
- Runs daily at 3 AM server time
- Creates compressed backup: `daily-YYYYMMDD.sql.gz`
- Uploads backup to S3 bucket (`s3://stablemanager-dev/backups/` or `s3://stablemanager-prod/backups/`)
- Deletes local backups older than 7 days
- Replaces existing cron job (idempotent)

**Note**: Both hooks read the server IP from the destination-specific deploy file.

## S3 Backup Configuration

The backup system requires `s3cmd` to be installed and configured on each server.

### Server Requirements

Each server needs:
1. `s3cmd` installed: `apt install s3cmd`
2. S3 configuration at `~/.s3cfg`

### s3cmd Configuration

```ini
# ~/.s3cfg on each server
[default]
access_key = <S3_ACCESS_KEY>
secret_key = <S3_SECRET_KEY>
host_base = hel1.your-objectstorage.com
host_bucket = %(bucket)s.hel1.your-objectstorage.com
use_https = True
signature_v2 = False
```

### Verify Backup System

To verify backups are working on a server:

```bash
# Check cron job exists
ssh root@<server> "crontab -l | grep backup"

# Check local backups
ssh root@<server> "ls -la /opt/backups/"

# Check S3 backups
ssh root@<server> "s3cmd ls s3://stablemanager-<env>/backups/"
```

### Troubleshooting S3 Uploads

If S3 uploads are failing silently:

1. **Check s3cmd is installed**: `ssh root@<server> "which s3cmd"`
2. **Check s3cmd config exists**: `ssh root@<server> "cat ~/.s3cfg"`
3. **Test S3 access**: `ssh root@<server> "s3cmd ls s3://stablemanager-<env>/"`
4. **Manually upload missing backups**:
   ```bash
   ssh root@<server> "s3cmd put /opt/backups/daily-*.sql.gz s3://stablemanager-<env>/backups/"
   ```

## Key Points

1. **Kamal 2** uses `kamal-proxy` (not Traefik) for zero-downtime deploys
2. **PostgreSQL accessory** containers are named `<service>-db` (e.g., `stablemanager-db`)
3. **Secrets** in `.kamal/secrets` should be gitignored
4. **Labels** must be added via `BP_IMAGE_LABELS` in bootBuildImage, not via `labels` property
5. **Version tags** are recommended over `latest` for traceability

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/torbenmerrald) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
