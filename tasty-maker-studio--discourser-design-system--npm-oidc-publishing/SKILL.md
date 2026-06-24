---
name: npm-oidc-publishing
description: Set up and troubleshoot npm Trusted Publishing with OIDC in GitHub Actions. Use when configuring passwordless npm publishing, debugging ENEEDAUTH errors, or migrating from 90-day npm tokens to OIDC authentication. Use when this capability is needed.
metadata:
  author: tasty-maker-studio
---

# npm OIDC Trusted Publishing Setup

## Overview

This skill helps configure npm Trusted Publishing using OpenID Connect (OIDC), eliminating the need for 90-day rotating npm tokens in GitHub Actions. Instead, GitHub cryptographically proves the workflow's identity to npm.

## When to Use This Skill

- Setting up GitHub Actions to publish npm packages without tokens
- Debugging `ENEEDAUTH` or `404 Not Found` errors during npm publish
- Migrating from npm token-based to OIDC-based authentication
- Configuring automated versioning with Changesets

## Prerequisites

1. **npm Account with 2FA enabled** (required for Trusted Publishing)
2. **GitHub repository** with Actions enabled
3. **Node.js 24+** (includes npm 11.5.1+ which supports OIDC)
4. **Package already published** to npm at least once

## Step-by-Step Setup

### 1. Configure npm Trusted Publisher

Go to your package settings on npmjs.com:

**URL**: `https://www.npmjs.com/package/@YOUR_SCOPE/PACKAGE_NAME/access`

1. Scroll to **"Trusted Publisher"** section
2. Select **"GitHub Actions"** as publisher
3. Fill in the form **EXACTLY** (case-sensitive, no extra spaces):

| Field | Value | Common Mistakes |
|-------|-------|-----------------|
| **Organization/User** | Your GitHub org (e.g., `Tasty-Maker-Studio`) | ❌ lowercase<br />❌ different org name |
| **Repository** | Repository name (e.g., `Discourser-Design-System`) | ❌ lowercase<br />❌ wrong repo |
| **Workflow** | `release.yml` | ❌ ` release.yml` (space before)<br />❌ `release.yml ` (space after)<br />❌ `.github/workflows/release.yml` |
| **Environment** | **Leave completely empty** | ❌ typing "blank"<br />❌ any text or spaces |

4. **CRITICAL**: Click **"Set up connection"** button to activate
5. Verify you see the active connection listed with GitHub icon

### 2. Configure GitHub Actions Workflow

Create `.github/workflows/release.yml`:

```yaml
name: Release

on:
  push:
    branches: [main]

permissions:
  contents: write
  pull-requests: write
  id-token: write  # Required for OIDC

jobs:
  release:
    name: Release
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: pnpm/action-setup@v4
        with:
          version: 9

      - uses: actions/setup-node@v4
        with:
          node-version: 24
          cache: 'pnpm'
          # DO NOT set registry-url - conflicts with OIDC

      - run: pnpm install --frozen-lockfile
      - run: pnpm build

      - name: Publish to npm with OIDC
        run: npm publish --provenance --access public
        env:
          # No NPM_TOKEN needed - OIDC handles auth
```

**Key Points**:
- ✅ `id-token: write` permission is **required**
- ✅ Node.js 24+ for npm 11.5.1+ (OIDC support)
- ❌ **DO NOT** set `registry-url` in setup-node (creates .npmrc that conflicts)
- ❌ **DO NOT** use `NODE_AUTH_TOKEN` environment variable
- ✅ `--provenance` flag helps npm detect OIDC

### 3. Verify No Conflicting Configuration

Check for files that might interfere:

1. **No npm tokens in GitHub Secrets**:
   - Settings → Secrets → Actions
   - Should have NO `NPM_TOKEN` secret

2. **No auth in .npmrc**:
   - `.npmrc` should only have config, not authentication
   - Remove any `//registry.npmjs.org/:_authToken=` lines

3. **Correct repository URL in package.json**:
   ```json
   {
     "repository": {
       "type": "git",
       "url": "https://github.com/YOUR_ORG/YOUR_REPO.git"
     }
   }
   ```

## Troubleshooting

### Error: `ENEEDAUTH - need auth`

**Cause**: npm cannot authenticate with OIDC

**Solutions**:
1. **Verify Trusted Publisher is ACTIVE** (not just configured)
   - Go to npmjs.com package settings
   - You should see the connection listed with Edit/Delete buttons
   - If you see an empty form, you need to click "Set up connection"

2. **Check for typos in npm configuration**:
   - Click "Edit" on your Trusted Publisher
   - Verify NO extra spaces in workflow field
   - Verify exact capitalization matches GitHub

3. **Verify OIDC environment**:
   Add debug step to workflow:
   ```yaml
   - name: Debug OIDC
     run: |
       npm --version
       echo "OIDC URL set: ${{ env.ACTIONS_ID_TOKEN_REQUEST_URL != '' }}"
   ```

### Error: `404 Not Found - PUT https://registry.npmjs.org/@scope/package`

**Cause**: npm Trusted Publisher configuration doesn't match running workflow

**Solutions**:
1. **Configuration mismatch** - Verify EXACT match:
   - Organization: must match GitHub URL (case-sensitive)
   - Repository: must match repo name (case-sensitive)
   - Workflow: must be exactly `release.yml` (no path, no spaces)
   - Environment: must be empty (not "blank", not spaces)

2. **Workflow file renamed**:
   - If you renamed `release.yml` to something else, update npm config

3. **Environment field has value**:
   - If npm config has environment set to "production", add to workflow:
     ```yaml
     jobs:
       release:
         environment: production  # Must match npm config
     ```

### Error: `Access token expired or revoked`

**Cause**: npm is trying to use token auth instead of OIDC

**Solutions**:
1. Remove `registry-url` from setup-node:
   ```yaml
   # ❌ WRONG - creates .npmrc with token
   - uses: actions/setup-node@v4
     with:
       registry-url: 'https://registry.npmjs.org'

   # ✅ CORRECT - allows OIDC
   - uses: actions/setup-node@v4
     with:
       node-version: 24
       cache: 'pnpm'
   ```

2. Delete `NPM_TOKEN` from GitHub Secrets

3. Check for `.npmrc` with auth token in repository

## Automated Versioning with Changesets

### Install Changesets

```bash
pnpm add -D @changesets/cli @changesets/changelog-github
pnpm changeset init
```

Configure `.changeset/config.json`:
```json
{
  "changelog": ["@changesets/changelog-github", {
    "repo": "YOUR_ORG/YOUR_REPO"
  }],
  "access": "public",
  "baseBranch": "main"
}
```

### Workflow with Changesets

```yaml
- name: Create Release PR or Publish
  uses: changesets/action@v1
  with:
    version: pnpm changeset version
    publish: pnpm changeset publish
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    # No NPM_TOKEN - using OIDC
```

### Using Changesets

**1. Make code changes**

**2. Create a changeset** (not a version bump!):
```bash
pnpm changeset
# Select: patch | minor | major
# Write summary of changes
```

**3. Commit the changeset file**:
```bash
git add .changeset/*.md
git commit -m "feat: add new feature"
git push
```

**4. Workflow creates "Version Packages" PR** automatically

**5. Merge PR → Auto-publishes** to npm via OIDC

## Benefits of OIDC Publishing

✅ **No token management** - No 90-day rotation
✅ **Better security** - Short-lived, workflow-specific tokens
✅ **No secrets** - Nothing to leak or expose
✅ **Automatic provenance** - Cryptographic proof of origin
✅ **Easier onboarding** - Team members don't need npm tokens

## Verification Checklist

Before asking for help, verify:

- [ ] npm package has Trusted Publisher ACTIVE (not just configured)
- [ ] Clicked "Set up connection" button on npmjs.com
- [ ] Workflow has `id-token: write` permission
- [ ] Using Node.js 24+ in workflow
- [ ] NO `registry-url` in setup-node
- [ ] NO `NPM_TOKEN` in GitHub Secrets
- [ ] Organization/Repository/Workflow match EXACTLY (case-sensitive)
- [ ] Environment field is completely empty (not "blank")
- [ ] Workflow file is named exactly `release.yml`

## Common Questions

**Q: Do I still need npm tokens for local development?**
A: Yes, OIDC only works in CI/CD. Local `npm publish` requires `npm login`.

**Q: Can I use OIDC with private packages?**
A: Yes, OIDC works with both public and private packages.

**Q: Does OIDC work with monorepos?**
A: Yes, but each package needs its own Trusted Publisher configuration.

**Q: Can I have multiple workflows publish the same package?**
A: Yes, configure multiple Trusted Publishers (one per workflow file).

## Resources

- [npm Trusted Publishing Docs](https://docs.npmjs.com/trusted-publishers/)
- [GitHub OIDC Documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)
- [Changesets Documentation](https://github.com/changesets/changesets)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tasty-maker-studio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
