---
name: cloudflare-wrangler-cli
description: Use when deploying Cloudflare Workers, managing R2 storage, or working with Cloudflare infrastructure
metadata:
  author: ntq78
---

# Wrangler CLI

**Purpose**: Comprehensive reference for the Wrangler CLI tool (Cloudflare's developer platform CLI), covering all commands AI agents can use for Worker development, R2 storage management, and infrastructure deployment.

## When to Use This Skill

Use this skill when you need to:

- Deploy Cloudflare Workers to staging or production
- Develop and test Workers locally
- Manage R2 buckets and objects
- Handle secrets and environment variables for Workers
- View Worker logs and deployment history
- Investigate Worker performance or errors
- Sync static assets to R2 storage
- Authenticate with Cloudflare platform

## Critical Rules

1. **ALWAYS check pnpm scripts first** - Use the **general-pnpm-scripts** skill to verify if a pnpm script exists before running CLI commands directly
2. **NEVER deploy to production without staging test** - Always test in staging environment first
3. **ALWAYS use --env flag for production** - Prevents accidental deployment to wrong environment
4. **NEVER commit secrets to wrangler.toml** - Use `wrangler secret put` for sensitive values
5. **ALWAYS use --remote flag for dev** - Local mode doesn't support R2 bindings properly

## CLI Installation & Authentication

### Version Check

```bash
# Check if Wrangler is installed
wrangler --version

# Expected: wrangler 3.x.x or higher
```

### Authentication

```bash
# Login to Cloudflare (opens browser)
wrangler login

# Verify authentication
wrangler whoami

# Logout (if needed)
wrangler logout
```

---

## Command Categories

### Worker Development

| Command                     | Purpose            | Example                                      | Key Flags                       |
| --------------------------- | ------------------ | -------------------------------------------- | ------------------------------- |
| `wrangler dev`              | Run worker locally | `wrangler dev --remote`                      | `--remote`, `--local`, `--port` |
| `wrangler deploy`           | Deploy worker      | `wrangler deploy --env staging`              | `--env`, `--dry-run`            |
| `wrangler tail`             | View live logs     | `wrangler tail --format pretty`              | `--env`, `--format`             |
| `wrangler deployments list` | List deployments   | `wrangler deployments list --env production` | `--env`                         |

**Common Usage:**

```bash
# Start local development (via pnpm - RECOMMENDED)
pnpm dev:w
# This runs: wrangler dev --remote

# Or direct CLI
wrangler dev --remote

# Deploy to staging
pnpm w:deploy:staging
# This runs: wrangler deploy --env staging

# Deploy to production
pnpm w:deploy:production
# This runs: wrangler deploy --env production

# View live logs
wrangler tail --env production --format pretty
```

---

### R2 Storage Management

| Command                     | Purpose          | Example                                         | Key Flags                  |
| --------------------------- | ---------------- | ----------------------------------------------- | -------------------------- |
| `wrangler r2 bucket create` | Create R2 bucket | `wrangler r2 bucket create my-bucket`           | `--jurisdiction`           |
| `wrangler r2 bucket list`   | List buckets     | `wrangler r2 bucket list`                       | -                          |
| `wrangler r2 object put`    | Upload object    | `wrangler r2 object put bucket/key --file path` | `--file`, `--content-type` |
| `wrangler r2 object get`    | Download object  | `wrangler r2 object get bucket/key --file path` | `--file`                   |
| `wrangler r2 object list`   | List objects     | `wrangler r2 object list bucket`                | `--limit`, `--prefix`      |
| `wrangler r2 object delete` | Delete object    | `wrangler r2 object delete bucket/key`          | -                          |

**Common Usage:**

```bash
# Create bucket
wrangler r2 bucket create lightcraft-assets-staging

# List buckets
wrangler r2 bucket list

# Upload file
wrangler r2 object put lightcraft-assets-staging/logo.png --file ./assets/logo.png

# List objects
wrangler r2 object list lightcraft-assets-staging --limit 100

# Download file
wrangler r2 object get lightcraft-assets-staging/logo.png --file ./downloaded.png
```

---

### Secret Management

| Command                  | Purpose           | Example                                           | Key Flags |
| ------------------------ | ----------------- | ------------------------------------------------- | --------- |
| `wrangler secret put`    | Set secret        | `wrangler secret put API_KEY --env production`    | `--env`   |
| `wrangler secret list`   | List secret names | `wrangler secret list --env production`           | `--env`   |
| `wrangler secret delete` | Delete secret     | `wrangler secret delete API_KEY --env production` | `--env`   |

**Common Usage:**

```bash
# Set secret (prompts for value)
wrangler secret put DATABASE_URL --env staging

# Or pipe value
echo "secret-value" | wrangler secret put API_KEY --env production

# List secrets (names only, not values)
wrangler secret list --env production

# Delete secret
wrangler secret delete OLD_KEY --env staging
```

---

### Account Management

| Command           | Purpose           | Example           | Key Flags |
| ----------------- | ----------------- | ----------------- | --------- |
| `wrangler login`  | Authenticate      | `wrangler login`  | -         |
| `wrangler whoami` | Show current user | `wrangler whoami` | -         |
| `wrangler logout` | Logout            | `wrangler logout` | -         |

---

## Environment Management

Wrangler uses environments defined in `wrangler.toml`:

| Environment       | Flag               | Use Case               |
| ----------------- | ------------------ | ---------------------- |
| **dev** (default) | (no flag)          | Local development      |
| **staging**       | `--env staging`    | Pre-production testing |
| **production**    | `--env production` | Live production        |

**Configuration Example (`wrangler.toml`):**

```toml
name = "file-delivery"
compatibility_date = "2024-11-01"

# Default (dev) - no R2 binding needed for local

[env.staging]
account_id = "your-account-id"
vars = { ENVIRONMENT = "staging" }

[[env.staging.r2_buckets]]
binding = "ASSETS"
bucket_name = "lightcraft-assets-staging"

[env.production]
account_id = "your-account-id"
vars = { ENVIRONMENT = "production" }

[[env.production.r2_buckets]]
binding = "ASSETS"
bucket_name = "lightcraft-assets-production"
```

**Key points:**

- Default environment (dev) is used when no `--env` flag is specified
- Each environment can have different bindings, variables, and configuration
- Secrets are environment-specific (set with `--env` flag)

---

## Integration with Related Skills

**Before using this skill:**

1. Use **general-pnpm-scripts** - Check if a pnpm script wrapper exists
2. If script exists, use it (e.g., `pnpm w:deploy:staging` instead of `wrangler deploy --env staging`)
3. If no script exists, use direct CLI commands documented here

**After using this skill:**

- **Review architecture docs** - See `docs/spark/backend/cloudflare/r2+workers.md` for system design
- **Review backend practices** - See `docs/spark/backend/cloudflare/practices.md` for workflows

---

## Common Workflows

### 1. Local Development

```bash
# 1. Start worker locally with remote bindings
pnpm dev:w
# This runs: wrangler dev --remote

# 2. Test at http://localhost:8787
# Make code changes (hot reload enabled)

# 3. Test worker endpoints
curl http://localhost:8787/health
curl http://localhost:8787/assets/logo.png
```

### 2. Deploying to Staging

```bash
# 1. Ensure code is committed
git status

# 2. Deploy to staging
pnpm w:deploy:staging
# This runs: wrangler deploy --env staging

# 3. Verify deployment
wrangler deployments list --env staging

# 4. Test staging worker
curl https://file-delivery-staging.your-subdomain.workers.dev/health

# 5. Monitor logs if needed
wrangler tail --env staging --format pretty
```

### 3. Deploying to Production

```bash
# 1. Test in staging first!
# (See workflow above)

# 2. Deploy to production
pnpm w:deploy:production
# This runs: wrangler deploy --env production

# 3. Verify deployment
wrangler deployments list --env production

# 4. Test production worker
curl https://file-delivery.your-subdomain.workers.dev/health

# 5. Monitor logs for errors
wrangler tail --env production --format pretty --status error
```

### 4. Managing R2 Assets

```bash
# 1. Generate asset manifest (if using asset sync)
pnpm f:assets:gen
# Creates manifest.json from local assets

# 2. Push to staging
pnpm f:assets:push:staging
# Syncs assets to R2 staging bucket

# 3. Verify assets in staging
wrangler r2 object list lightcraft-assets-staging --limit 10

# 4. Test asset delivery through worker
curl https://file-delivery-staging.workers.dev/assets/logo.png

# 5. If good, push to production
pnpm f:assets:push:production
```

---

## Troubleshooting

### Authentication fails

**Symptom:** `wrangler deploy` fails with "Not authenticated"

**Solution:**

```bash
# Logout and login again
wrangler logout
wrangler login

# Verify authentication
wrangler whoami
```

---

### Worker deployment fails

**Symptom:** Deploy command fails with validation error

**Solution:**

```bash
# Check wrangler.toml syntax
cat wrangler.toml

# Verify account_id is correct
wrangler whoami

# Check compatibility_date is valid
# Format: YYYY-MM-DD

# Try deploying with --dry-run to see errors
wrangler deploy --env staging --dry-run
```

---

### R2 bucket not found in worker

**Symptom:** Worker throws "R2 bucket not found" error at runtime

**Solution:**

```bash
# 1. Check wrangler.toml has correct binding
cat wrangler.toml | grep -A 3 "r2_buckets"

# 2. Verify bucket exists
wrangler r2 bucket list

# 3. Ensure deployed with correct --env flag
wrangler deploy --env staging  # Not production!

# 4. Check worker code uses correct binding name
# In code: env.ASSETS (should match binding in wrangler.toml)
```

---

### Secrets not working

**Symptom:** Worker can't access environment variables

**Solution:**

```bash
# For secrets (sensitive data)
wrangler secret put MY_SECRET --env production
# Then redeploy worker

# For non-secrets (public config)
# Add to wrangler.toml:
# [env.production.vars]
# MY_VAR = "value"
# Then redeploy

# List secrets to verify
wrangler secret list --env production
```

---

### Local dev mode issues with R2

**Symptom:** `wrangler dev` (local mode) can't access R2

**Solution:**

```bash
# ALWAYS use --remote flag for R2 testing
wrangler dev --remote

# Or use pnpm script (which includes --remote)
pnpm dev:w

# Local mode simulates bindings but doesn't connect to real R2
# Use --remote to test against actual Cloudflare infrastructure
```

---

## Best Practices

### ✅ 1. Use --remote for development

```bash
# ✅ Good: Test against real Cloudflare environment
wrangler dev --remote

# ❌ Bad: Local simulator may not match production behavior
wrangler dev --local
```

### ✅ 2. Always test in staging first

```bash
# ✅ Good workflow
wrangler deploy --env staging
# Test thoroughly
wrangler deploy --env production

# ❌ Bad: Skip staging
wrangler deploy --env production  # RISKY!
```

### ✅ 3. Use pnpm scripts when available

```bash
# ✅ Good: Uses project scripts (correct working directory)
pnpm w:deploy:staging

# 🤔 Acceptable: Direct CLI (if script doesn't exist)
wrangler deploy --env staging
```

### ✅ 4. Monitor logs after deployment

```bash
# After deployment
wrangler tail --env production --format pretty

# Watch for errors
wrangler tail --env production --status error
```

### ✅ 5. Use semantic commit messages

```bash
# Before deploying
git add .
git commit -m "feat(worker): Add asset caching logic"
git push

# Then deploy
pnpm w:deploy:staging
```

---

**See Also:**

- `examples.md` - Detailed workflow examples
- `reference.md` - Complete command reference and anti-patterns
- `docs/spark/backend/cloudflare/practices.md` - Backend-specific practices
- `docs/spark/backend/cloudflare/r2+workers.md` - Architecture documentation

<!-- Last compacted: 2025-01-17 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
