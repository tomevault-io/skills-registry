---
name: codeapps-validator
description: Comprehensive Power Platform Code Apps validation. Validates package.json scripts, port configuration, dependencies, project structure, TypeScript compilation, and Power Platform configuration. Use after setup or before deployment. Use when this capability is needed.
metadata:
  author: ramakrishnan24689
---

# CodeApps Validator Skill

## Purpose

Systematically validate Power Platform Code Apps projects to ensure proper configuration, structure, and readiness for development or deployment. This skill validates all critical aspects of a CodeApps project including npm scripts, port configuration, dependencies, and Power Platform integration.

## When to Use This Skill

**ALWAYS use after**:
- Initial project setup (after Step 1 in workshop)
- Adding data sources or connectors
- Modifying package.json scripts
- Before deployment to Power Platform
- When troubleshooting configuration issues

**Use to validate**:
- package.json dev scripts configuration
- Port assignments (5173 for Vite, 8081 for pac code run)
- Required dependencies installation
- Power Platform configuration (power.config.json)
- Project structure and folders
- TypeScript compilation success
- ESLint compliance

## Validation Framework

This skill performs **8 critical checks**:

### 1. Package.json Scripts Validation
Ensures dev scripts are properly configured for dual-server setup.

### 2. Port Configuration Check
Validates correct port usage (5173 for Vite, 8081 for pac code run).

### 3. Dependencies Validation
Confirms all required packages are installed.

### 4. Power Platform Configuration Check
Verifies power.config.json exists and has proper structure.

### 5. Vite Configuration Check (CRITICAL)
Validates vite.config.ts has `base: './'` for correct asset paths in deployment.

### 6. Project Structure Validation
Ensures required folders and files exist.

### 7. TypeScript Compilation Check
Validates code compiles without errors.

### 8. ESLint Compliance Check
Verifies code passes linting rules.

## Validation Process

### Step 1: Validate package.json Scripts

**Check for proper dev script configuration**:

```bash
# Read package.json
cat package.json | grep -A 5 '"scripts"'
```

**Required scripts**:
```json
{
  "scripts": {
    "dev": "concurrently \"npm:dev:vite\" \"npm:dev:pac\"",
    "dev:vite": "vite --port 5173",
    "dev:pac": "pac code run --port 8081 --appUrl http://localhost:5173",
    "build": "tsc && vite build",
    "lint": "eslint . --ext ts,tsx"
  }
}
```

**Validation checks**:
- [ ] `dev` script exists and uses concurrently
- [ ] `dev:vite` script exists with port 5173
- [ ] `dev:pac` script exists with port 8081
- [ ] `dev:pac` references correct appUrl (http://localhost:5173)
- [ ] `build` script includes TypeScript compilation
- [ ] `lint` script exists

**Common issues**:
- Missing concurrently in dev script
- Wrong ports specified
- Missing npm: prefix in concurrently commands
- Incorrect appUrl in dev:pac

### Step 2: Validate Port Configuration

**Extract and verify ports from scripts**:

```bash
# Check Vite port
grep -o 'vite.*--port [0-9]*' package.json

# Check pac code run port
grep -o 'pac code run.*--port [0-9]*' package.json

# Check appUrl port
grep -o 'appUrl http://localhost:[0-9]*' package.json
```

**Expected configuration**:
- Vite dev server: **5173**
- pac code run: **8081**
- appUrl reference: **5173**

**Port conflict checks**:
- [ ] Vite and pac code run use different ports
- [ ] appUrl references Vite port (5173)
- [ ] No hardcoded localhost:3000 references

### Step 3: Validate Dependencies

**Check for required packages**:

```bash
# Read dependencies
cat package.json | grep -A 20 '"dependencies"'
cat package.json | grep -A 20 '"devDependencies"'
```

**Required dependencies**:
```json
{
  "dependencies": {
    "@microsoft/power-apps": "latest",
    "@fluentui/react-components": "^9.x",
    "@tanstack/react-query": "^5.x",
    "react": "^18.x || ^19.x",
    "react-dom": "^18.x || ^19.x"
  },
  "devDependencies": {
    "concurrently": "^8.0.0",
    "typescript": "^5.x",
    "vite": "^5.x",
    "eslint": "^8.x || ^9.x"
  }
}
```

**Validation checks**:
- [ ] @microsoft/power-apps is installed
- [ ] concurrently is installed
- [ ] React 18/19 is installed
- [ ] TypeScript is installed
- [ ] Vite is installed
- [ ] Fluent UI v9 is installed (if applicable)
- [ ] React Query is installed (if using data operations)

**Verify installation**:
```bash
# Check node_modules
ls node_modules/@microsoft/power-apps
ls node_modules/concurrently
ls node_modules/vite
```

### Step 4: Validate Power Platform Configuration

**Check power.config.json**:

```bash
# Verify file exists
test -f power.config.json && echo "✅ power.config.json exists" || echo "❌ power.config.json missing"

# Read configuration
cat power.config.json
```

**Expected structure**:
```json
{
  "displayName": "App Name",
  "description": "App description",
  "appUrl": "http://localhost:5173",
  "buildPath": "dist",
  "region": "prod",
  "dataSources": []
}
```

**Validation checks**:
- [ ] power.config.json exists
- [ ] displayName is set
- [ ] appUrl matches Vite port (5173)
- [ ] buildPath is set to "dist"
- [ ] Valid JSON structure (no syntax errors)

**Warning signs**:
- ❌ power.config.json missing (run `pac code init`)
- ❌ appUrl references wrong port
- ❌ buildPath incorrect (should be "dist" for Vite)

### Step 5: Validate Vite Configuration (CRITICAL)

**Check vite.config.ts for base path**:

```bash
# Read vite.config.ts
cat vite.config.ts | grep -A 3 'export default defineConfig'
```

**Required configuration**:
```typescript
export default defineConfig({
  plugins: [react()],
  base: './',  // ✅ CRITICAL: Required for Power Apps deployment
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
  },
})
```

**Validation checks**:
- [ ] vite.config.ts exists
- [ ] `base: './'` is configured
- [ ] `build.outDir` is set to 'dist'
- [ ] `build.assetsDir` is set to 'assets'

**Common issues**:
- ❌ Missing `base: './'` - causes 404 errors on deployed assets
- ❌ `base: '/'` (absolute path) - same issue as missing base
- ❌ Wrong outDir - pac code push looks for ./dist by default

**Why This is Critical**:

Without `base: './'`, the built index.html will reference assets with **absolute paths** (`/assets/index.js`), which fail when deployed to Power Apps. The browser tries to load from:
```
https://{environment}.powerplatformusercontent.com/assets/index.js
```

But Power Apps serves files from a nested path. With `base: './'`, assets use **relative paths** (`./assets/index.js`), which resolve correctly.

**Symptoms of Missing base Configuration**:
- 404 errors for CSS/JS files in deployed app
- `Refused to apply style ... MIME type ('application/json') is not a supported stylesheet MIME type`
- App works locally but fails in production
- Blank screen when launching deployed app

**Verification**:
```bash
# After build, check if paths are relative
cat dist/index.html | grep 'src=\|href='
# Should show: src="./assets/..." NOT src="/assets/..."
```

**Auto-fix if missing**:
If `base: './'` is not found, add it to vite.config.ts and rebuild:
```bash
# Add base: './' to vite.config.ts
# Then rebuild
npm run build
```

### Step 6: Validate Project Structure

**Check for required folders and files**:

```bash
# Check project structure
ls -la

# Verify critical paths
test -d src && echo "✅ src/ exists" || echo "❌ src/ missing"
test -d src/generated && echo "✅ src/generated/ exists" || echo "⚠️ src/generated/ missing (expected if no data sources)"
test -f src/App.tsx && echo "✅ src/App.tsx exists" || echo "❌ src/App.tsx missing"
test -f vite.config.ts && echo "✅ vite.config.ts exists" || echo "❌ vite.config.ts missing"
test -f tsconfig.json && echo "✅ tsconfig.json exists" || echo "❌ tsconfig.json missing"
```

**Required structure**:
```
project-root/
├── src/
│   ├── App.tsx
│   ├── main.tsx
│   └── generated/          (if data sources added)
│       ├── models/
│       └── services/
├── package.json
├── power.config.json
├── vite.config.ts
├── tsconfig.json
└── node_modules/
```

**Validation checks**:
- [ ] src/ folder exists
- [ ] src/App.tsx exists
- [ ] src/main.tsx exists
- [ ] vite.config.ts exists
- [ ] tsconfig.json exists
- [ ] package.json exists
- [ ] power.config.json exists
- [ ] node_modules/ exists (dependencies installed)

### Step 6: Validate TypeScript Compilation

**Run TypeScript compiler**:

```bash
npm run build
```

**Validation checks**:
- [ ] Build exits with code 0 (success)
- [ ] No TypeScript compilation errors
- [ ] dist/ folder created with build artifacts
- [ ] index.html generated in dist/

**Common TypeScript errors in CodeApps**:
- Missing @microsoft/power-apps types
- Generated service import errors
- React Query type mismatches
- Power Platform SDK initialization issues

### Step 7: Validate ESLint Compliance

**Run ESLint check**:

```bash
npm run lint
```

**Validation checks**:
- [ ] Lint exits with code 0 (success)
- [ ] No ESLint errors
- [ ] No critical warnings

**Common ESLint issues in CodeApps**:
- Unused imports from generated services
- React Hook dependency warnings
- Console statements in production code

## Validation Report Format

```markdown
# CodeApps Validation Report
**Date**: [current date]
**Project**: [project name from power.config.json]
**Status**: ✅ READY | ⚠️ WARNINGS | ❌ FAILED

## Summary
- **Scripts Configuration**: ✅ PASS | ❌ FAIL
- **Port Configuration**: ✅ PASS | ❌ FAIL
- **Dependencies**: ✅ PASS | ❌ FAIL (X missing)
- **Power Platform Config**: ✅ PASS | ❌ FAIL
- **Vite Configuration**: ✅ PASS | ❌ FAIL (base path missing)
- **Project Structure**: ✅ PASS | ❌ FAIL
- **TypeScript Compilation**: ✅ PASS | ❌ FAIL (X errors)
- **ESLint Check**: ✅ PASS | ❌ FAIL (X errors)

## 1. Package.json Scripts Analysis

### Dev Scripts
```json
{
  "dev": "...",
  "dev:vite": "...",
  "dev:pac": "..."
}
```

**Issues Found**:
- ❌ Missing concurrently in dev script
- ❌ Wrong port in dev:vite (expected 5173, found 3000)
- ✅ dev:pac correctly configured

**Recommendations**:
1. Update dev script to use concurrently
2. Change Vite port to 5173

## 2. Port Configuration Analysis

**Current Configuration**:
- Vite: 5173 ✅
- pac code run: 8081 ✅
- appUrl: http://localhost:5173 ✅

**Issues Found**: None

## 3. Dependencies Analysis

**Installed Dependencies**:
- @microsoft/power-apps: 1.0.0 ✅
- concurrently: 8.2.0 ✅
- react: 19.0.0 ✅
- vite: 5.4.0 ✅

**Missing Dependencies**:
- ❌ @tanstack/react-query (required for data operations)

**Recommendations**:
1. Install missing dependencies: `npm install @tanstack/react-query`

## 4. Power Platform Configuration Analysis

**power.config.json**:
```json
{
  "displayName": "todo-demo",
  "appUrl": "http://localhost:5173",
  "buildPath": "dist"
}
```

**Issues Found**: None

## 5. Vite Configuration Analysis

**vite.config.ts**:
```typescript
export default defineConfig({
  plugins: [react()],
  base: './', // ✅ Present
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
  },
})
```

**Validation**:
- base path: ✅ Configured as './' (relative paths)
- outDir: ✅ Set to 'dist'
- assetsDir: ✅ Set to 'assets'

**Issues Found**: None

**Deployment Readiness**: ✅ Assets will load correctly in Power Apps

## 6. Project Structure Analysis

**Structure Validation**:
- src/: ✅ Exists
- src/App.tsx: ✅ Exists
- src/generated/: ⚠️ Not found (no data sources added yet)
- vite.config.ts: ✅ Exists
- tsconfig.json: ✅ Exists
- power.config.json: ✅ Exists

**Issues Found**: None (expected state for new project)

## 7. TypeScript Compilation Results

**Build Status**: ✅ PASS

**Build Output**:
```
vite v5.4.0 building for production...
✓ 32 modules transformed.
dist/index.html                  0.45 kB
dist/assets/index-abc123.js     45.32 kB
✓ built in 1.2s
```

**Issues Found**: None

## 8. ESLint Results

**Lint Status**: ✅ PASS

**Issues Found**: None

## Overall Assessment

### ✅ Ready for Development
Project is properly configured and ready for development.

### Next Steps
- [ ] Start development server: `npm run dev`
- [ ] Add Dataverse data sources if needed
- [ ] Begin building features

### Optional Improvements
1. Consider adding @tanstack/react-query for better data management
2. Add pre-commit hooks for automatic validation
```

## Usage Pattern

**Manual invocation**:
```
User: "/codeapps-validator"
System: Runs full CodeApps validation
```

**After project setup**:
```
User: "Validate my CodeApps project configuration"
Agent: Invokes codeapps-validator skill
Agent: Reports validation results with specific fixes
```

**Before deployment**:
```
User: "Check if my app is ready to deploy"
Agent: Invokes codeapps-validator skill
Agent: Confirms readiness or identifies blockers
```

## Common Issues and Fixes

### Issue 1: Missing concurrently
**Symptom**: dev script doesn't start both servers
**Fix**:
```bash
npm install --save-dev concurrently
```
Update package.json:
```json
"dev": "concurrently \"npm:dev:vite\" \"npm:dev:pac\""
```

### Issue 2: Wrong ports
**Symptom**: Port conflicts or connection errors
**Fix**: Update scripts to use standard ports:
- Vite: 5173
- pac code run: 8081

### Issue 3: power.config.json missing
**Symptom**: pac code commands fail
**Fix**:
```bash
pac code init --displayName "App Name"
```

### Issue 4: Missing @microsoft/power-apps
**Symptom**: Import errors for Power Apps SDK
**Fix**:
```bash
npm install @microsoft/power-apps
```

### Issue 5: appUrl mismatch
**Symptom**: "Local Play" URL doesn't work
**Fix**: Ensure power.config.json appUrl matches Vite port:
```json
"appUrl": "http://localhost:5173"
```

### Issue 6: Missing base: './' in vite.config.ts (CRITICAL)
**Symptom**: Deployed app shows blank screen, 404 errors for CSS/JS files, MIME type errors
**Fix**: Add base: './' to vite.config.ts:
```typescript
export default defineConfig({
  plugins: [react()],
  base: './', // ✅ Add this line
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
  },
})
```
Then rebuild and redeploy:
```bash
npm run build
pac code push
```

## Success Criteria

**Project is READY when**:
- ✅ All required scripts present in package.json
- ✅ Ports correctly configured (5173 and 8081)
- ✅ All dependencies installed
- ✅ power.config.json exists and valid
- ✅ vite.config.ts has base: './' configured
- ✅ Project structure complete
- ✅ TypeScript compiles successfully
- ✅ ESLint passes

**Project has WARNINGS when**:
- ⚠️ Optional dependencies missing (e.g., React Query)
- ⚠️ ESLint warnings present but no errors
- ⚠️ src/generated/ missing (if no data sources added yet)

**Project FAILED when**:
- ❌ Required scripts missing
- ❌ Wrong ports configured
- ❌ Missing critical dependencies
- ❌ power.config.json invalid or missing
- ❌ TypeScript compilation errors
- ❌ ESLint errors present

## Integration with Workshop

**Step 1 Validation** (After project creation):
```
User: "Validate the project setup"
Agent: Runs codeapps-validator
Agent: Confirms proper configuration or suggests fixes
```

**Step 6 Validation** (Before testing with Dataverse):
```
User: "/codeapps-validator"
Agent: Checks all configuration including new data sources
Agent: Validates generated/ folder structure
```

**Step 8 Validation** (Before deployment):
```
User: "Validate before deploying"
Agent: Full validation including build check
Agent: Confirms production readiness
```

---

**Usage**: Invoke this skill after project setup, configuration changes, or before deployment to ensure proper CodeApps configuration and readiness.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramakrishnan24689) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
