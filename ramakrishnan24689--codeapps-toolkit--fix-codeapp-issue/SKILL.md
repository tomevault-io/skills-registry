---
name: fix-codeapp-issue
description: Troubleshoot and fix common Power Apps Code Apps issues including deployment errors, 404 asset errors, Dataverse OData errors, authentication issues, and build failures. Provides diagnostic guidance and automated fixes. Use when this capability is needed.
metadata:
  author: ramakrishnan24689
---

# Fix Code Apps Issues

Diagnose and fix common Power Apps Code Apps issues with guided troubleshooting and automated repairs.

## What This Skill Does

1. Diagnoses common CodeApps issues based on symptoms
2. Provides step-by-step troubleshooting guidance
3. Attempts automated fixes when possible
4. Validates fixes with integrated validation
5. Provides prevention tips

## Common Issues & Solutions

### Issue 1: 404 Errors for Assets (Blank Screen After Deployment)

**Symptoms:**
- Deployed app shows blank screen
- Console shows: `GET .../assets/index-xxx.js net::ERR_ABORTED 404`
- MIME type error: `Refused to apply style ... MIME type ('application/json')`

**Root Cause:** Missing `base: './'` in vite.config.ts

**Automated Fix:**
```typescript
// Check vite.config.ts
const config = await read('vite.config.ts');
if (!config.includes("base: './'")) {
  // Add base: './' to config
  const updated = config.replace(
    'export default defineConfig({',
    `export default defineConfig({\n  base: './',`
  );
  await write('vite.config.ts', updated);
  console.log('✅ Added base: "./" to vite.config.ts');

  // Rebuild
  await exec('npm run build');

  // Suggest redeploy
  console.log('💡 Run: pac code push');
}
```

### Issue 2: System-Managed Field Error (Dataverse)

**Symptoms:**
- `PrimitiveValue node with non-null value was found... 'ownerid'`
- Create/update operations fail

**Root Cause:** Sending system-managed fields (ownerid, statecode, timestamps, primary key)

**Fix:** Review service layer and remove system-managed fields:
```typescript
// ❌ WRONG
const payload = {
  name: dto.name,
  ownerid: '',  // Remove this
  statecode: 0, // Remove this
};

// ✅ CORRECT
const payload = {
  name: dto.name,
  // Dataverse sets ownerid, statecode automatically
};
```

### Issue 3: Boolean Field Type Mismatch

**Symptoms:**
- `Cannot convert the literal '1' to expected type 'Edm.Boolean'`
- Update operations fail with Boolean fields

**Root Cause:** Generated types suggest numeric (0|1) but Dataverse expects boolean

**Fix:** Use `as any` type assertion:
```typescript
// ❌ WRONG
updates.booleanField = value ? 1 : 0;

// ✅ CORRECT
updates.booleanField = value as any;
```

### Issue 4: Authentication Failures

**Symptoms:**
- `pac code push` fails with authentication error
- Connector API calls return 401

**Fix:**
```bash
pac auth clear
pac auth create
pac env select --environment <environmentId>
```

### Issue 5: Build Failures

**Symptoms:**
- `npm run build` exits with errors
- TypeScript compilation errors

**Diagnostic Steps:**
1. Read build output for specific errors
2. Check for missing imports
3. Verify dependencies installed: `npm install`
4. Run TypeScript check: `npx tsc --noEmit`
5. Run ESLint: `npm run lint`

**Common Causes:**
- Missing dependencies
- Import path errors
- Type mismatches
- Unused variables

### Issue 6: Connection Not Found

**Symptoms:**
- `pac code add-data-source` fails
- API calls fail with connection errors

**Fix:**
```bash
# List connections
pac connection list

# If missing, user must create in Power Apps portal
echo "Create connection at: https://make.powerapps.com → Connections"
```

### Issue 7: Port Conflicts

**Symptoms:**
- `npm run dev` fails with "port already in use"

**Fix:**
```bash
# Find process using port
netstat -ano | findstr :5173  # Windows
lsof -i :5173                 # Mac/Linux

# Kill process or change port in package.json
```

### Issue 8: pac code init Failures

**Symptoms:**
- `pac code init` fails
- power.config.json not created

**Fix:**
```bash
# Verify authentication
pac auth list

# Re-authenticate if needed
pac auth create
pac env select --environment <environmentId>

# Retry init
pac code init --displayName "AppName"
```

## Diagnostic Workflow

### Step 1: Identify Issue Category

Ask user about symptoms:
- Deployment issues (404s, blank screen)
- Data operation failures (Dataverse/connector errors)
- Build/compilation failures
- Authentication problems
- Development environment issues

### Step 2: Run Diagnostics

Based on category, run appropriate checks:

**For Deployment Issues:**
```bash
# Check vite.config.ts
cat vite.config.ts | grep "base:"

# Check built assets
cat dist/index.html | grep -E 'src=|href='

# Verify power.config.json
cat power.config.json
```

**For Data Issues:**
- Review service layer for system-managed fields
- Check Boolean field handling
- Verify connection exists: `pac connection list`

**For Build Issues:**
```bash
# Run validation
Invoke codeapps-validator skill
Invoke build-lint-validator skill
```

### Step 3: Apply Fixes

Attempt automated fixes when possible:
- Add missing `base: './'`
- Fix import paths
- Remove system-managed fields from payloads
- Add `as any` to Boolean fields

### Step 4: Validate Fix

```bash
# Rebuild
npm run build

# Run validators
Invoke codeapps-validator skill

# Test locally
npm run dev
```

### Step 5: Prevention Guidance

Provide tips to avoid future issues:
- Always use codeapps-validator before deployment
- Never modify src/generated/ files
- Use service layer wrappers (never call generated services directly)
- Test in local dev before deploying
- Use connection references for better ALM

## Quick Diagnosis Commands

```bash
# Full project validation
Invoke codeapps-validator skill

# Build validation
npm run build
npm run lint

# Check authentication
pac auth list

# Check connections
pac connection list

# Check deployed apps
pac code list

# Check vite config
cat vite.config.ts | grep "base:"

# Check asset paths
cat dist/index.html | grep -E 'src=|href='
```

## Output Format

```
🔍 Diagnostic Report

❌ Issues Found:
1. [Issue description]
   - Cause: [root cause]
   - Impact: [what's affected]

🔧 Applied Fixes:
1. [Fix applied]
   - File: [file path]
   - Change: [what changed]

✅ Validation:
- Build: Pass/Fail
- TypeScript: Pass/Fail
- ESLint: Pass/Fail

💡 Prevention Tips:
- [Tip 1]
- [Tip 2]

📋 Next Steps:
1. [Action item]
2. [Action item]
```

## Related Skills

- **/init-codeapp**: Reinitialize project if severely broken
- **/deploy-codeapp**: Redeploy after fixes
- **codeapps-validator**: Comprehensive validation
- **build-lint-validator**: Build quality check

---

**Usage:** Invoke when encountering errors or issues during development, build, or deployment. Provides diagnostic guidance and automated fixes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramakrishnan24689) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
