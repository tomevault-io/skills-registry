---
name: ops-deployment
description: Procedures for building, verifying, and deploying the application using Vantus Systems specific scripts. Use when performing ops tasks, troubleshooting builds, or explaining the deployment process. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# Ops Deployment

This skill details the custom build and deployment pipeline for Project SENTINEL. It ensures system integrity through cryptographic proofs, automated server bootstrapping, and hardened security configurations.

## Quick Start

Deploy a new version in 4 steps:

1.  **Verify Environment**: Ensure all required secrets are set in `.env` or `/etc/default/vantus`.
2.  **Run Build Proof**: Execute `npm run build` to generate the cryptographic build proof and compile the app.
3.  **Apply Migrations**: Run `npx prisma migrate deploy` to update the production database.
4.  **Restart Services**: Restart the Systemd service and reload Nginx to apply changes.

```bash
# Example: Manual deployment sequence
npm run build
npx prisma migrate deploy
sudo systemctl restart vantus
sudo systemctl reload nginx
```

## Core Concepts

### 1. Build Proof System
We use a "Build Proof" system instead of a standard `next build` to ensure the integrity of the running code.
*   **Artifact**: `scripts/generate-build-proof.mjs` creates a cryptographic signature of the build.
*   **Verification**: The app exposes `/proof/build.json` for runtime integrity checks.

### 2. Automated Bootstrapping (Ubuntu 22.04)
Fresh production servers are configured using `scripts/bootstrap-ubuntu22.sh`.
*   **Security**: Creates an unprivileged `vantus` user and configures UFW.
*   **Stack**: Installs Node.js, Nginx, SQLite, and Certbot.

### 3. Environment Configuration
Interactive setup via `scripts/setup-env.js` ensures secure secret generation and correct database wiring.

## Ops Workflows

### Troubleshooting a Failed Build

**Step 1: Check Build Logs**
Review the output of `npm run build`. Look for errors in the `generate-build-proof` step or Next.js compilation.

**Step 2: Verify Dependencies**
Ensure `package-lock.json` is consistent and all dependencies are installed via `npm ci`.

**Step 3: Validate Environment**
Run `node scripts/setup-env.js --verify` to check for missing or invalid environment variables.

### Updating Nginx Configuration

**Step 1: Generate Config**
Run `npm run generate:nginx` to create a new configuration based on current environment variables.

**Step 2: Test Configuration**
Always run `sudo nginx -t` before reloading to prevent downtime due to syntax errors.

**Step 3: Apply Changes**
Execute `sudo systemctl reload nginx`.

## Advanced Patterns

### Zero-Downtime Migrations
For complex schema changes, use a dual-write strategy or ensure migrations are backward-compatible with the currently running code version.

### Integrity Monitoring
Integrate the `/proof/build.json` endpoint with external monitoring tools to detect unauthorized code changes or deployment drift.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
