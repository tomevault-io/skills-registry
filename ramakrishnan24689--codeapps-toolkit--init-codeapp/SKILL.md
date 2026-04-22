---
name: init-codeapp
description: Initialize a new Power Apps Code App project with proper configuration and validation. Sets up dual-server development, configures vite.config.ts with base path, and validates setup. Use when this capability is needed.
metadata:
  author: ramakrishnan24689
---

# Initialize Code App Project

Initialize a new Power Apps Code App with proper configuration for development and deployment.

## What This Skill Does

1. Creates project from official Microsoft template
2. Configures package.json for dual-server development (Vite + pac code run)
3. Sets up vite.config.ts with **`base: './'`** (critical for deployment)
4. Installs all dependencies including concurrently
5. Runs pac code init to register with Power Platform
6. Validates entire setup with integrated validation

## Usage

```
/init-codeapp my-app-name
```

or let Claude invoke automatically:

```
Initialize a new Code App called todo-app
```

## Workflow

### Step 1: Check Prerequisites

Verify required tools are installed:

```bash
# Check Node.js
node --version

# Check Power Platform CLI
pac --version

# Check Git
git --version
```

If any tools are missing, inform the user and provide installation links:
- Node.js: https://nodejs.org/
- Power Platform CLI: https://learn.microsoft.com/power-platform/developer/cli/introduction
- Git: https://git-scm.com/

### Step 2: Authentication Check

```bash
# Check if user is authenticated
pac auth list
```

If not authenticated or environment not selected:
```bash
pac auth create
pac env select --environment <environmentId>
```

### Step 3: Create Project from Template

```bash
# Use official Microsoft template
npx degit github:microsoft/PowerAppsCodeApps/templates/vite $ARGUMENTS[0]
cd $ARGUMENTS[0]
```

**Naming Best Practice**: For shared environments, suggest including username:
```
Suggested name: $ARGUMENTS[0]-{username}
Would you like to use this naming pattern? (Prevents conflicts in shared environments)
```

### Step 4: Install Dependencies

```bash
npm install

# Install required dev dependencies
npm install --save-dev concurrently

# Install recommended dependencies
npm install @tanstack/react-query
```

### Step 5: Configure package.json Scripts

Check if scripts need updating:

```bash
cat package.json | grep -A 5 '"scripts"'
```

If not configured for dual-server, update scripts:

```json
{
  "scripts": {
    "dev": "concurrently \"npm:dev:vite\" \"npm:dev:pac\"",
    "dev:vite": "vite --port 5173",
    "dev:pac": "pac code run --port 8081 --appUrl http://localhost:5173",
    "build": "tsc && vite build",
    "lint": "eslint . --ext ts,tsx --report-unused-disable-directives --max-warnings 0"
  }
}
```

### Step 6: Configure vite.config.ts

**CRITICAL**: Ensure `base: './'` is present:

```bash
cat vite.config.ts
```

If missing, add `base: './'`:

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  base: './',  // ✅ CRITICAL: Required for Power Apps deployment
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
  },
})
```

**Why This Matters**:
Without `base: './'`, deployed apps show blank screen with 404 errors for assets.

### Step 7: Initialize Code App with Power Platform

```bash
pac code init --displayName "$ARGUMENTS[0]" --description "Code App created via init-codeapp skill"
```

This creates `power.config.json` with app metadata.

### Step 8: Integrated Validation

**Automatically invoke codeapps-validator skill**:

The codeapps-validator skill checks:
- ✅ Package.json scripts (dev, dev:vite, dev:pac)
- ✅ Port configuration (5173 for Vite, 8081 for pac)
- ✅ Dependencies (@microsoft/power-apps, concurrently)
- ✅ Power Platform config (power.config.json)
- ✅ **Vite config (base: './')** ← CRITICAL
- ✅ Project structure
- ✅ TypeScript compilation
- ✅ ESLint compliance

If validation fails, **report specific issues** and fix them before proceeding.

### Step 9: Test Development Server

```bash
npm run dev
```

This starts:
- Vite dev server on http://localhost:5173
- pac code run on http://localhost:8081

**Instruct user**: Use the "Local Play" URL from pac code run (http://localhost:8081)

### Step 10: Success Summary

Provide user with:

```
✅ Code App initialized successfully!

📁 Project: $ARGUMENTS[0]
🚀 Development server: npm run dev
🌐 Local Play URL: http://localhost:8081
📦 Build command: npm run build
🚢 Deploy command: pac code push

Next steps:
1. Start development: npm run dev
2. Add Dataverse table: /add-datasource dataverse <table-name>
3. Add connector: /add-datasource connector <api-id>
4. Deploy to Power Platform: /deploy-codeapp

Documentation: https://learn.microsoft.com/power-apps/developer/code-apps/
```

## Error Handling

### Error: degit failed

**Cause**: Network issue or template not accessible

**Fix**: Clone manually:
```bash
git clone https://github.com/microsoft/PowerAppsCodeApps
cp -r PowerAppsCodeApps/templates/vite $ARGUMENTS[0]
```

### Error: pac auth required

**Cause**: User not authenticated

**Fix**: Guide through authentication:
```bash
pac auth create
pac env select --environment <environmentId>
```

### Error: npm install fails

**Cause**: Network issues or package registry problems

**Fix**:
```bash
npm cache clean --force
npm install --verbose
```

### Error: pac code init fails

**Cause**: Not authenticated or environment not selected

**Fix**: Re-authenticate and select environment

## Validation Integration

This skill automatically validates the setup using the **codeapps-validator** skill. If validation fails:

1. **Report specific issues** found by validator
2. **Attempt to fix** automatically (e.g., add missing base: './')
3. **Re-run validation** after fixes
4. **Only mark as complete** when validation passes

## Success Criteria

Project is ready when:
- ✅ All files created from template
- ✅ Dependencies installed (including concurrently)
- ✅ package.json scripts configured for dual-server
- ✅ vite.config.ts has `base: './'`
- ✅ pac code init completed successfully
- ✅ power.config.json exists and valid
- ✅ codeapps-validator passes all checks
- ✅ TypeScript compiles without errors
- ✅ ESLint passes
- ✅ npm run dev starts both servers successfully

## Related Skills

- **Add data source**: `/add-datasource`
- **Deploy app**: `/deploy-codeapp`
- **Validate project**: invoke codeapps-validator skill automatically
- **Generate service**: `/gen-service`
- **Generate hook**: `/gen-hook`

---

**Pro Tip**: After initialization, use specialized agents for specific tasks:
- Adding Dataverse tables → codeapps-dataverse agent
- Adding connectors → codeapps-connector agent
- Deployment → codeapps-deploy agent

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramakrishnan24689) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
