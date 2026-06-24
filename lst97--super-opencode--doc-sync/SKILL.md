---
name: doc-sync
description: Documentation synchronization with code to prevent drift and ensure accuracy. Use when this capability is needed.
metadata:
  author: lst97
---

# Doc Sync Skill

## Purpose

To prevent **documentation drift**—the silent killer of maintainability. This skill ensures documentation stays synchronized with code changes, catching discrepancies before they become "source of truth" conflicts.

**ROI Metric**: Accurate docs reduce onboarding time by 50% and prevent costly misimplementations.

## When to Use

- **Trigger**: After any significant code change (new feature, API change, refactor).
- **Trigger**: When `self-check` flags documentation in implementation reports.
- **Trigger**: Before releases (ensures public docs are accurate).
- **Trigger**: When onboarding new team members (docs are tested).
- **Trigger**: When `pm-agent` schedules a "documentation audit."

## The Doc Sync Protocol (The 4-Step Process)

### 1. Discovery (What Docs Exist?)

**Scan for documentation:**
```
docs/
├── README.md              # Project overview
├── api/                   # API documentation
├── architecture/          # System design docs
├── setup.md              # Developer onboarding
├── deployment.md         # Deployment guide
└── adr/                  # Architecture decisions

README.md
CONTRIBUTING.md
AGENTS.md
CHANGELOG.md
```

**Identify doc types:**
- **API Docs**: OpenAPI/Swagger, README endpoints
- **Architecture Docs**: Diagrams, system descriptions
- **Setup Docs**: Installation, configuration
- **ADR Docs**: Decision records
- **Code Comments**: JSDoc, docstrings

### 2. Extraction (What Does Code Say?)

**Extract current state from code:**

**API Contracts:**
```typescript
// From backend code
@Post('/users')
async createUser(@Body() dto: CreateUserDto) {
  // Extract: POST /users accepts CreateUserDto
}

// From Zod schemas
export const CreateUserSchema = z.object({
  email: z.string().email(),  // Extract: email required, must be valid
  name: z.string().min(2),     // Extract: name required, min 2 chars
});
```

**Configuration:**
```typescript
// From config files
export const config = {
  database: {
    host: process.env.DB_HOST,     // Extract: DB_HOST env var required
    port: parseInt(process.env.DB_PORT || '5432'),  // Extract: DB_PORT optional, default 5432
  }
};
```

**Dependencies:**
```json
// From package.json
{
  "dependencies": {
    "react": "^18.2.0",  // Extract: React 18 required
    "next": "14.0.0"     // Extract: Next.js 14 required
  }
}
```

### 3. Comparison (Do They Match?)

**Compare extracted code state with documented state:**

| Source | Documented | Actual | Status |
|:-------|:-----------|:-------|:-------|
| API: POST /users | Accepts `name`, `email` | Accepts `name`, `email`, `role` | ⚠️ MISMATCH |
| DB: Host | Required env var | Has default `localhost` | ⚠️ MISMATCH |
| Node version | v16 required | v18 in Dockerfile | ⚠️ MISMATCH |
| Dependency: React | v17 | v18 | ❌ OUTDATED |

### 4. Synchronization (Update Docs)

**Update strategies:**

**A. Immediate Update**
- Update docs as part of the same PR that changes code.
- Include in Definition of Done: "Documentation updated."

**B. Scheduled Sync**
- Weekly/bi-weekly doc review sessions.
- Batch update accumulated drift.

**C. Release Gate**
- Block releases if doc sync check fails.
- Enforce accuracy before going public.

## Doc Sync Checklist Template

```markdown
# Doc Sync Report: [Feature/Release]

## Summary
**Scope**: [What changed?]  
**Date**: YYYY-MM-DD  
**Sync Status**: ✅ In Sync | ⚠️ Minor Drift | ❌ Major Drift

## Files Changed
| File | Change Type | Docs Updated? |
|:-----|:------------|:-------------:|
| src/api/users.ts | New endpoint | ⬜ |
| src/config/db.ts | New env var | ⬜ |
| package.json | Dependency bump | ⬜ |

## API Documentation Sync

| Endpoint | Doc Location | Doc Status | Code Status | Match? |
|:---------|:-------------|:-----------|:------------|:------:|
| POST /users | docs/api/users.md | v1.0 | v1.1 (added role) | ❌ |
| GET /users/:id | docs/api/users.md | v1.0 | v1.0 | ✅ |

### Required Updates
- [ ] Add `role` parameter to POST /users documentation
- [ ] Update example request/response
- [ ] Add validation rules for role field

## Environment Variables Sync

| Variable | Doc Location | Doc Status | Code Status | Match? |
|:---------|:-------------|:-----------|:------------|:------:|
| DB_HOST | README.md | Required | Required | ✅ |
| DB_PORT | README.md | Not documented | Optional (5432) | ⚠️ |
| REDIS_URL | README.md | Required | Optional | ❌ |

### Required Updates
- [ ] Document DB_PORT as optional with default
- [ ] Update REDIS_URL from required to optional

## Dependencies Sync

| Dependency | Doc Location | Doc Version | Actual Version | Match? |
|:-----------|:-------------|:------------|:---------------|:------:|
| Node.js | README.md | v16.x | v18.x | ❌ |
| React | README.md | v17.x | v18.2.x | ❌ |
| PostgreSQL | README.md | v13 | v15 | ⚠️ |

### Required Updates
- [ ] Update Node.js requirement to v18
- [ ] Update React requirement to v18
- [ ] Verify PostgreSQL compatibility doc

## Architecture Docs Sync

| Component | Doc Location | Doc Status | Code Status | Match? |
|:----------|:-------------|:-----------|:------------|:------:|
| Auth Service | docs/arch/auth.md | JWT | JWT + OAuth | ⚠️ |
| Database Schema | docs/arch/db.md | v1 | v2 (added orders) | ❌ |

### Required Updates
- [ ] Add OAuth flow to auth documentation
- [ ] Update database schema diagram
- [ ] Document orders table

## Action Items

### Immediate (Block Release)
- [ ] Update POST /users API docs
- [ ] Correct Node.js version requirement

### This Week
- [ ] Document missing env vars
- [ ] Update architecture diagram

### Next Sprint
- [ ] Full dependency audit
- [ ] Review all code comments

## Verification
- [ ] All API examples tested and working
- [ ] Setup instructions verified on clean machine
- [ ] Architecture diagrams match current system
- [ ] Code comments are accurate
```

## Execution Template

*Use this during code review or before release.*

```markdown
## 🔄 Doc Sync Check: [PR/Release Name]

### 1. Code Changes
| File | Lines Changed | Doc Impact |
|:-----|:-------------:|:-----------|
| api/auth.ts | +45, -12 | Authentication flow |
| config/app.ts | +8, -2 | New env var |
| types/user.ts | +23, -0 | User fields |

### 2. Extracted Code State
**API Changes:**
- New endpoint: `POST /auth/refresh` (added in auth.ts:67)
- New field: `User.lastLoginAt` (added in types/user.ts:12)

**Config Changes:**
- New env: `JWT_REFRESH_SECRET` (added in config/app.ts:23)

### 3. Documentation Audit

| Doc File | Section | Needs Update? | Action |
|:---------|:--------|:-------------:|:-------|
| docs/api/auth.md | Endpoints | ✅ Yes | Add refresh endpoint |
| docs/api/auth.md | Examples | ✅ Yes | Add refresh example |
| README.md | Env Vars | ✅ Yes | Add JWT_REFRESH_SECRET |
| docs/models/user.md | Schema | ✅ Yes | Add lastLoginAt field |

### 4. Sync Plan
**This PR:**
- [ ] Update docs/api/auth.md
- [ ] Update README.md
- [ ] Update docs/models/user.md

**Estimated Time**: 30 minutes

### 5. Definition of Done
- [ ] All code changes reflected in docs
- [ ] Examples tested and working
- [ ] Doc review completed
```

## Automation Strategies

### A. CI/CD Integration
```yaml
# .github/workflows/doc-sync.yml
name: Documentation Sync Check

on:
  pull_request:
    paths:
      - 'src/**'
      - 'docs/**'

jobs:
  doc-sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Check API Docs
        run: |
          # Extract API routes from code
          npm run extract-api > api-current.json
          # Compare with documented API
          npm run verify-docs api-current.json docs/api/
          
      - name: Check Type Definitions
        run: |
          # Verify TypeScript types match documented types
          npm run verify-types
```

### B. Pre-Commit Hooks
```bash
# .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

# If API files changed, remind to update docs
if git diff --cached --name-only | grep -q "src/api/"; then
  echo "⚠️  API files changed. Did you update the documentation?"
fi
```

### C. Living Documentation
Generate docs from code automatically:
```typescript
// Use tools like:
// - TypeDoc for TypeScript
// - JSDoc for JavaScript
// - Swagger/OpenAPI from code annotations
// - MkDocs with code extraction plugins
```

## Integration with Agents

- **`backend`**: Updates API docs when endpoints change.
- **`frontend`**: Updates component docs when props change.
- **`architect`**: Updates architecture docs when system changes.
- **`writer`**: Reviews and polishes documentation.
- **`pm-agent`**: Schedules doc sync audits, tracks completion.

## Common Doc Drift Scenarios

### Scenario 1: API Evolution
```
Week 1: API accepts { name, email }
Week 4: Code adds optional `role` field
Week 8: Client tries to use `role` from outdated docs → Error
```
**Fix**: Update API docs same week role is added.

### Scenario 2: Environment Changes
```
Original: DB_HOST required, DB_PORT defaults to 5432
Docs say: Both DB_HOST and DB_PORT are required
Result: Developer confusion, wasted time
```
**Fix**: Sync env var docs with actual config.

### Scenario 3: Dependency Updates
```
Docs say: Requires Node.js 16
Dockerfile: Uses Node.js 18
CI/CD: Uses Node.js 18
New dev: Installs Node 16, gets deprecation warnings
```
**Fix**: Update all version references together.

## Success Metrics

- **Doc Accuracy Score**: % of documented items that match code
- **Time to Onboard**: How long for new dev to get running
- **Support Tickets**: % related to doc confusion
- **API Error Rate**: % due to clients following outdated docs

Target: 95%+ doc accuracy, <1 day onboarding time.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lst97) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
