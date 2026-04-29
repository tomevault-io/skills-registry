---
name: vercel-multi-env-setup
description: | Use when this capability is needed.
metadata:
  author: helixdevelopment
---

# Vercel Multi Env Setup

## Prerequisites
- Separate Vercel accounts or API keys per environment
- Secret management solution (Vault, AWS Secrets Manager, etc.)
- CI/CD pipeline with environment variables
- Environment detection in application

## Instructions

### Step 1: Create Config Structure
Set up the base and per-environment configuration files.

### Step 2: Implement Environment Detection
Add logic to detect and load environment-specific config.

### Step 3: Configure Secrets
Store API keys securely using your secret management solution.

### Step 4: Add Environment Guards
Implement safeguards for production-only operations.

## Output
- Multi-environment config structure
- Environment detection logic
- Secure secret management
- Production safeguards enabled

## Error Handling

See `{baseDir}/references/errors.md` for comprehensive error handling.

## Examples

See `{baseDir}/references/examples.md` for detailed examples.

## Resources
- [Vercel Environments Guide](https://vercel.com/docs/environments)
- [12-Factor App Config](https://12factor.net/config)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/helixdevelopment) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
