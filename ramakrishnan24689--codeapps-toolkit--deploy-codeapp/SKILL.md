---
name: deploy-codeapp
description: Deploy Code App to Power Platform with comprehensive pre-deployment validation, build verification, and post-deployment checks. Ensures vite.config.ts has base path configured. Use when this capability is needed.
metadata:
  author: ramakrishnan24689
---

# Deploy Code App to Power Platform

Deploy your Code App to Power Platform with integrated validation, build checks, and deployment verification.

## What This Skill Does

1. **Pre-deployment validation** (codeapps-validator skill)
2. **Build quality check** (build-lint-validator skill)
3. **Build application** with asset path verification
4. **Deploy to Power Platform** (`pac code push`)
5. **Post-deployment verification** to ensure successful deployment
6. **Provide deployment summary** with app URL and next steps

## Usage

### Deploy to Preferred Solution

```
/deploy-codeapp
```

### Deploy to Specific Solution

```
/deploy-codeapp MySolutionName
```

### Let Claude Invoke Automatically

```
Deploy my Code App to Power Platform
Push this app to production
Deploy to the "Marketing Apps" solution
```

## Deployment Workflow

### Phase 1: Pre-Deployment Validation

**CRITICAL**: Always validate BEFORE deploying to catch issues early.

#### Invoke codeapps-validator Skill

Automatically check:
- ✅ Package.json scripts configuration
- ✅ Port configuration (5173 and 8081)
- ✅ Dependencies installed
- ✅ Power Platform config (power.config.json valid)
- ✅ **Vite config has `base: './'`** ← CRITICAL for asset loading
- ✅ Project structure complete
- ✅ TypeScript compilation succeeds
- ✅ ESLint compliance

**If validation fails**:
1. Report specific issues found
2. Attempt automatic fixes (e.g., add `base: './'` to vite.config.ts)
3. Re-run validation
4. **STOP** deployment if critical issues remain

### Phase 2: Build Quality Check

#### Invoke build-lint-validator Skill

Verify code quality:
- ✅ TypeScript compiles without errors
- ✅ ESLint passes all rules
- ✅ No type mismatches
- ✅ No unused variables
- ✅ React Hook dependencies correct

**If build validation fails**:
1. Report specific TypeScript/ESLint errors
2. Guide user to fix errors
3. **STOP** deployment until issues resolved

### Phase 3: Build Application

```bash
npm run build
```

**Expected Output**:
```
vite v5.x.x building for production...
✓ XX modules transformed.
dist/index.html                  X.XX kB
dist/assets/index-xxxxx.js      XX.XX kB
✓ built in X.Xs
```

**Verify Build Success**:
- [ ] Build command exits with code 0
- [ ] `dist/` folder created
- [ ] `dist/index.html` exists
- [ ] `dist/assets/` contains JS and CSS files

#### Critical: Verify Asset Paths

Check that built HTML uses **relative paths** (not absolute):

```bash
# Check asset references in built index.html
cat dist/index.html | grep -E 'src=|href='
```

**Expected**: `src="./assets/index-xxxxx.js"` (relative path with `./`)
**Problematic**: `src="/assets/index-xxxxx.js"` (absolute path)

**If absolute paths found**:
```
❌ ERROR: vite.config.ts missing base: './'

This will cause 404 errors when deployed. Assets won't load.

Fixing automatically...
```

Add `base: './'` to vite.config.ts and rebuild:
```typescript
export default defineConfig({
  plugins: [react()],
  base: './',  // ✅ Add this line
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
  },
})
```

Then rebuild:
```bash
npm run build
```

### Phase 4: Deploy to Power Platform

#### Check Authentication

```bash
# Verify authenticated
pac auth list
```

If not authenticated:
```bash
pac auth create
pac env select --environment <environmentId>
```

#### Execute Deployment

**Option 1: Deploy to Preferred Solution** (default)
```bash
pac code push
```

**Option 2: Deploy to Specific Solution**
```bash
pac code push --solutionName "$ARGUMENTS[0]"
```

**Deployment Progress**:
```
Uploading code app package...
Publishing to Power Platform...
Code app published successfully.
```

### Phase 5: Post-Deployment Verification

#### List Deployed Apps

```bash
pac code list
```

Verify the app appears in the list with correct name and version.

#### Deployment Success Checklist

Provide user with verification steps:

```
✅ Deployment Verification Checklist:

1. Open Power Apps portal:
   https://make.powerapps.com

2. Navigate to Apps section

3. Find your app: $ARGUMENTS[0]

4. Click "Play" to launch the app

5. Verify in browser:
   - [ ] App loads (no blank screen)
   - [ ] No 404 errors in console (F12 → Console)
   - [ ] CSS styles applied correctly
   - [ ] JavaScript executes
   - [ ] Data operations work (if using Dataverse/connectors)

6. Check browser console for errors:
   - Open DevTools (F12)
   - Check Console tab
   - Check Network tab for failed requests
```

#### Common Post-Deployment Issues

**Issue: Blank screen**
- **Cause**: Missing `base: './'` in vite.config.ts
- **Symptom**: 404 errors for assets in console
- **Fix**: Add `base: './'`, rebuild, redeploy

**Issue: Assets fail to load**
- **Cause**: Absolute paths in index.html
- **Symptom**: `GET .../assets/index-xxx.js 404`
- **Fix**: Verify `base: './'` in vite.config.ts

**Issue: MIME type errors**
- **Symptom**: `Refused to apply style ... MIME type ('application/json')`
- **Cause**: Asset files returning 404 (interpreted as JSON error page)
- **Fix**: Ensure `base: './'` configured and assets built correctly

### Phase 6: Deployment Summary

```
🎉 Deployment Successful!

📦 App Name: $ARGUMENTS[0]
🌐 Solution: <solution-name or "Default">
⏰ Deployed: <timestamp>

🔗 Quick Links:
- Power Apps Portal: https://make.powerapps.com
- App URL: <app-url from pac code list>
- Environment: <environment-name>

✅ Verification Status:
- Build: Success
- Upload: Success
- Publish: Success

📋 Next Steps:
1. Test app functionality in deployed environment
2. Verify data operations work correctly
3. Share app with users (Power Apps portal → Share)
4. Monitor for errors or issues

💡 To update the app:
1. Make changes to code
2. Run: /deploy-codeapp
```

## ALM (Application Lifecycle Management)

### Deploy to Multiple Environments

**Recommended Pattern**: Dev → Test → Prod

```bash
# Deploy to Dev
pac env select --environment <dev-environment-id>
pac code push --solutionName "CodeApps-Dev"

# Export solution
pac solution export --path ./solution.zip --name "CodeApps-Dev"

# Import to Test
pac env select --environment <test-environment-id>
pac solution import --path ./solution.zip

# Update connection references in Test environment

# Test thoroughly, then promote to Prod
pac env select --environment <prod-environment-id>
pac solution import --path ./solution.zip
```

### Connection References (Recommended)

For better portability across environments, use connection references:
- Connections can be reconfigured per environment
- No code changes needed when promoting
- Better governance and security

```bash
# Add data source with connection reference
pac code add-data-source -a <apiId> -cr <connectionReferenceLogicalName> -s <solutionID>
```

## Error Handling

### Error: Authentication Required

**Symptom**: `pac code push` fails with authentication error

**Fix**:
```bash
pac auth clear
pac auth create
pac env select --environment <environmentId>
pac code push
```

### Error: Solution Not Found

**Symptom**: Can't find specified solution

**Fix**:
1. List solutions: `pac solution list`
2. Verify solution name (case-sensitive)
3. Create solution if doesn't exist in Power Apps portal

### Error: Build Fails

**Symptom**: `npm run build` exits with errors

**Fix**:
1. Read error messages from build output
2. Fix TypeScript compilation errors
3. Run `npm install` if dependencies missing
4. Retry build

### Error: Upload Size Limit

**Symptom**: Deployment fails with "size limit exceeded"

**Fix**:
1. Check bundle size: `ls -lh dist/assets/`
2. Optimize imports (remove unused dependencies)
3. Consider code splitting
4. Compress assets

### Error: Missing Power Config

**Symptom**: `pac code push` fails with "power.config.json not found"

**Fix**:
```bash
pac code init --displayName "AppName"
```

## Rollback Strategy

If deployment causes issues:

### Option 1: Quick Rollback
1. Open Power Apps portal
2. Find the app
3. Navigate to versions
4. Restore previous version

### Option 2: Redeploy Previous Version
```bash
# Checkout previous git commit
git checkout <previous-commit-hash>

# Rebuild and redeploy
npm run build
pac code push
```

### Option 3: Solution Rollback
1. Export previous solution version
2. Import to restore environment

## CI/CD Integration

This skill can be integrated into CI/CD pipelines:

### GitHub Actions
```yaml
- name: Deploy Code App
  run: |
    pac auth create --environment ${{ secrets.ENVIRONMENT_ID }}
    npm run build
    pac code push
```

### Azure DevOps
```yaml
- script: |
    pac auth create
    npm run build
    pac code push
  displayName: 'Deploy Code App'
```

## Validation Summary

Before allowing deployment, ensure:
- ✅ codeapps-validator passes all checks
- ✅ build-lint-validator passes all checks
- ✅ `npm run build` succeeds
- ✅ `dist/` folder contains built assets
- ✅ Asset paths are relative (./assets/)
- ✅ User is authenticated with Power Platform
- ✅ Target environment is selected

**If ANY check fails**: Stop deployment and fix issues first.

## Success Criteria

Deployment succeeds when:
- ✅ All validation passes
- ✅ Build completes without errors
- ✅ `pac code push` exits successfully
- ✅ App appears in `pac code list`
- ✅ App opens in Power Apps portal
- ✅ No 404 errors in browser console
- ✅ Assets load correctly
- ✅ Data operations work (if applicable)

## Related Skills

- **Validate project**: codeapps-validator (invoked automatically)
- **Build validation**: build-lint-validator (invoked automatically)
- **Initialize project**: `/init-codeapp`
- **Add data source**: `/add-datasource`
- **Fix issues**: `/fix-codeapp-issue`

---

**Pro Tip**: Always test in a Dev environment before deploying to Production. Use solutions and connection references for better ALM across environments.

**Documentation**: https://learn.microsoft.com/power-apps/developer/code-apps/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramakrishnan24689) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
