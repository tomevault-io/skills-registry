---
name: fullstack-validation
description: Comprehensive validation methodology for multi-component applications including backend, frontend, database, and infrastructure Use when this capability is needed.
metadata:
  author: neversight
---

## Overview

This skill provides systematic approaches for validating full-stack applications with multiple interconnected components. It enables automatic detection of project structure, parallel validation workflows, cross-component verification, and identification of integration issues.

**When to use**: Full-stack projects with backend + frontend, microservices, monorepos, Docker Compose setups, or any multi-technology application.

**Key innovation**: Parallel validation with cross-component awareness - validates each layer independently while ensuring they work together correctly.

## Project Structure Detection

### Detection Patterns

**Monorepo Indicators**:
- Root `package.json` with workspaces
- `lerna.json` or `nx.json` present
- Multiple `package.json` files in subdirectories
- `pnpm-workspace.yaml` present

**Separate Repos Indicators**:
- Single technology stack per repository
- Docker Compose references external services
- Git submodules present

**Technology Stack Detection**:
```
Backend:
- FastAPI: requirements.txt with 'fastapi', main.py with FastAPI app
- Django: manage.py, settings.py present
- Express: package.json with 'express', app.js/index.js
- Spring Boot: pom.xml or build.gradle with spring-boot

Frontend:
- React: package.json with 'react', src/App.tsx or src/App.jsx
- Vue: package.json with 'vue', src/App.vue
- Angular: package.json with '@angular/core', angular.json
- Svelte: package.json with 'svelte', src/App.svelte

Database:
- PostgreSQL: requirements.txt with 'psycopg2', docker-compose.yml with postgres
- MySQL: package.json with 'mysql2', docker-compose.yml with mysql
- MongoDB: package.json with 'mongoose', docker-compose.yml with mongo
- Redis: docker-compose.yml with redis, requirements.txt with 'redis'

Infrastructure:
- Docker: Dockerfile, docker-compose.yml present
- Kubernetes: k8s/ or kubernetes/ directory with .yaml files
- Terraform: .tf files present
- Nginx: nginx.conf present
```

## Validation Workflows

### Backend Validation Checklist

**Python/FastAPI Projects**:
1. Dependency validation
   - Check requirements.txt exists and is parseable
   - Verify all imports can be resolved
   - Check for version conflicts
   - Validate Python version compatibility

2. Type checking
   - Run mypy on all source files
   - Check for missing type hints
   - Validate Pydantic model definitions
   - Verify return type annotations

3. Test validation
   - Run pytest with coverage
   - Check test isolation (database cleanup)
   - Validate fixture dependencies
   - Ensure no test data pollution
   - Check for views/triggers blocking teardown

4. API schema validation
   - Extract OpenAPI/Swagger schema
   - Validate all endpoints have docstrings
   - Check request/response models
   - Verify authentication decorators

5. Database migration validation
   - Check Alembic migrations are sequential
   - Validate up/down migration pairs
   - Ensure migrations are reversible
   - Check for data loss risks

**Node.js/Express Projects**:
1. Dependency validation (npm/yarn/pnpm)
2. ESLint validation
3. Jest/Mocha test execution
4. API route validation
5. Database migration validation (Knex/Sequelize)

### Frontend Validation Checklist

**React + TypeScript Projects**:
1. TypeScript validation
   - Run tsc --noEmit for type checking
   - Detect unused imports (auto-fix available)
   - Check tsconfig.json strictness
   - Validate path aliases (@/ imports)
   - Generate missing .d.ts files (vite-env.d.ts, etc.)

2. Dependency validation
   - Check package.json for peer dependency warnings
   - Detect version mismatches (React Query vs React)
   - Validate ESM vs CommonJS consistency
   - Check for deprecated packages

3. Build validation
   - Run production build (npm run build / vite build)
   - Check bundle size (warn if > 1MB per chunk)
   - Validate environment variables
   - Check for build warnings
   - Validate asset optimization

4. Code quality
   - Run ESLint with auto-fix
   - Check for console.log statements in production
   - Validate React hooks usage
   - Check for deprecated React patterns
   - Detect old library syntax (React Query v4 → v5)

5. API client validation
   - Check all API calls have error handling
   - Validate API base URLs
   - Ensure loading/error states exist
   - Check authentication token handling

**Vue/Angular Projects**: Similar checklist adapted to framework specifics

### Database Validation Checklist

1. Schema validation
   - Check all tables exist
   - Validate foreign key constraints
   - Check for orphaned records
   - Validate indexes on frequently queried columns

2. Test isolation validation
   - Detect views dependent on test tables
   - Check for triggers that prevent cleanup
   - Validate CASCADE deletion works
   - Ensure test data doesn't leak to other tests

3. Query validation
   - Check for N+1 query problems
   - Validate JOIN efficiency
   - Check for missing indexes
   - Detect raw SQL strings (SQLAlchemy 2.0 requires text() wrapper)

### Infrastructure Validation Checklist

**Docker Compose Projects**:
1. Service health checks
   - Verify all services start successfully
   - Check healthcheck endpoints respond
   - Validate depends_on order is correct
   - Check restart policies

2. Port conflict detection
   - Ensure no duplicate port mappings
   - Check host ports are available
   - Validate internal service communication

3. Volume validation
   - Check mounted directories exist
   - Validate volume permissions
   - Ensure persistent data volumes are defined

4. Environment variable validation
   - Check .env.example matches required vars
   - Validate all services receive needed env vars
   - Check for hardcoded credentials
   - Ensure secrets are not committed

## Cross-Component Validation

### API Contract Validation

**Process**:
1. Extract backend API schema
   - FastAPI: GET /docs → openapi.json
   - Express: Parse route definitions
   - Django REST: GET /schema

2. Extract frontend API client calls
   - Search for axios/fetch calls
   - Find API client service files
   - Parse API endpoint strings

3. Cross-validate
   - Check every frontend call has matching backend endpoint
   - Validate HTTP methods match (GET/POST/PUT/DELETE)
   - Check parameter names and types match
   - Verify response types match frontend expectations
   - Detect missing error handling

**Auto-fix capabilities**:
- Generate missing TypeScript types from OpenAPI schema
- Generate missing API client methods
- Update deprecated endpoint calls
- Add missing error handling

### Environment Variable Consistency

**Process**:
1. Collect all env var references
   - Backend: os.getenv(), settings.py
   - Frontend: import.meta.env, process.env
   - Docker: docker-compose.yml env sections

2. Cross-validate
   - Check .env.example has all referenced vars
   - Ensure frontend vars have VITE_ or REACT_APP_ prefix
   - Validate no secrets in frontend code
   - Check env vars are documented

### Authentication Flow Validation

**Process**:
1. Identify auth mechanism (JWT, OAuth, Basic, API Key)
2. Check backend auth implementation
   - Token generation/validation
   - Password hashing
   - Session management
3. Check frontend auth implementation
   - Token storage (localStorage/sessionStorage/cookies)
   - Auth headers in API calls
   - Protected route guards
   - Token refresh logic
4. Cross-validate
   - Ensure token format matches backend expectations
   - Check expiration handling
   - Validate logout clears all auth data

## Parallel Validation Strategy

### Execution Plan

```
Phase 1: Detection (Sequential)
├─ Scan project structure
├─ Identify all components
└─ Determine validation workflows

Phase 2: Component Validation (Parallel)
├─ Backend validation (background)
├─ Frontend validation (background)
├─ Database validation (background)
└─ Infrastructure validation (background)

Phase 3: Cross-Component Validation (Sequential)
├─ API contract validation (requires Phase 2 complete)
├─ Environment variable validation
└─ Authentication flow validation

Phase 4: Reporting (Sequential)
├─ Aggregate results
├─ Prioritize issues
└─ Generate recommendations
```

### Priority Levels

**Critical (🔴)**: Blocks deployment, requires immediate fix
- Backend tests failing
- Frontend build failing
- API contract mismatches causing runtime errors
- Database migration failures
- Security vulnerabilities

**Warning (🟡)**: Should be fixed, doesn't block deployment
- Low test coverage (< 70%)
- Bundle size warnings
- Missing type hints
- Unused dependencies
- Performance issues

**Info (🟢)**: Nice to have, improves quality
- Code style inconsistencies
- Missing documentation
- Optimization opportunities
- Deprecated syntax (still functional)

## Auto-Fix Capabilities

### Automatic Fixes (No confirmation needed)

**TypeScript**:
- Remove unused imports
- Add missing semicolons
- Fix indentation
- Sort imports

**Python**:
- Format with Black
- Sort imports with isort
- Remove unused variables (prefix with _)
- Add text() wrapper to raw SQL strings

**Configuration**:
- Generate missing config files (vite-env.d.ts, tsconfig.json)
- Fix ESM/CommonJS conflicts
- Update deprecated config syntax

### Suggested Fixes (Requires confirmation)

**TypeScript**:
- Generate missing type definitions
- Update React Query v4 → v5 syntax
- Add missing error handling
- Migrate class components to hooks

**Python**:
- Add missing type hints
- Migrate to async/await
- Update deprecated SQLAlchemy patterns
- Add missing docstrings

**Database**:
- Add missing indexes
- Fix N+1 queries with joins
- Update cascade delete rules

## Pattern Learning Integration

### Patterns to Capture

**Project Structure Patterns**:
```json
{
  "project_type": "fullstack-monorepo",
  "backend": "fastapi",
  "frontend": "react-typescript",
  "database": "postgresql",
  "infrastructure": "docker-compose",
  "patterns_detected": {
    "api_versioning": "/api/v1",
    "auth_method": "jwt",
    "orm": "sqlalchemy",
    "state_management": "react-query"
  }
}
```

**Common Issue Patterns**:
```json
{
  "typescript_unused_imports": {
    "frequency": 12,
    "auto_fix_success_rate": 1.0,
    "common_files": ["src/components/*.tsx"]
  },
  "sqlalchemy_raw_sql": {
    "frequency": 5,
    "auto_fix_success_rate": 1.0,
    "pattern": "execute('SELECT ...') → execute(text('SELECT ...'))"
  },
  "react_query_v4_syntax": {
    "frequency": 3,
    "auto_fix_success_rate": 0.9,
    "pattern": "useQuery(['key'], fn) → useQuery({queryKey: ['key'], queryFn: fn})"
  }
}
```

**Validation Performance Patterns**:
```json
{
  "backend_validation_time": "15s",
  "frontend_validation_time": "45s",
  "bottlenecks": ["TypeScript compilation", "npm install"],
  "optimization_opportunities": ["Use turbo for builds", "Cache dependencies"]
}
```

## When to Apply This Skill

**Automatic triggers**:
- Project has both backend and frontend directories
- docker-compose.yml detected with multiple services
- Multiple package.json or requirements.txt files found
- User runs `/validate-fullstack` command

**Manual triggers**:
- User mentions "full-stack", "backend and frontend", "API integration"
- User reports issues across multiple components
- Deployment preparation
- CI/CD pipeline setup

## Integration with Other Skills

**Combines with**:
- `code-analysis`: For structural analysis of each component
- `quality-standards`: For quality benchmarks
- `testing-strategies`: For test coverage validation
- `pattern-learning`: For capturing project-specific patterns
- `validation-standards`: For tool usage validation

**Delegates to agents**:
- `frontend-analyzer`: For detailed TypeScript/React validation
- `api-contract-validator`: For API synchronization
- `build-validator`: For build configuration issues
- `test-engineer`: For test infrastructure fixes
- `quality-controller`: For comprehensive quality assessment

## Success Metrics

**Validation effectiveness**:
- Issue detection rate: % of issues found automatically
- False positive rate: < 5%
- Auto-fix success rate: > 80%
- Time savings vs manual validation: > 90%

**Quality improvements**:
- Issues caught before deployment: Track over time
- Deployment success rate: Should increase
- Time to fix issues: Should decrease
- Pattern reuse rate: Should increase for similar projects

## Example Validation Report

```
✅ Full-Stack Validation Complete (2m 34s)

📊 Component Status:
├─ Backend (FastAPI + PostgreSQL)
│  ├─ ✅ Dependencies: 42 packages, 0 conflicts
│  ├─ ✅ Type hints: 98% coverage
│  ├─ ⚠️  Tests: 45 passing, 42% coverage (target: 70%)
│  └─ ✅ API schema: 23 endpoints documented
│
├─ Frontend (React + TypeScript)
│  ├─ ✅ Type check: 0 errors (auto-fixed 16)
│  ├─ ✅ Build: 882KB bundle (optimized)
│  ├─ ✅ Dependencies: 124 packages, 0 peer warnings
│  └─ ✅ Unused imports: 0 (auto-removed 5)
│
└─ Integration
   ├─ ✅ API contract: 23/23 endpoints matched
   ├─ ✅ Environment vars: 15/15 documented
   └─ ✅ Auth flow: JWT tokens validated

🔧 Auto-Fixed Issues (11):
✓ Removed 5 unused TypeScript imports
✓ Generated vite-env.d.ts
✓ Added text() wrapper to 3 SQL queries
✓ Fixed 2 React Query v5 syntax issues

⚠️  Recommended Actions (2):
1. Increase test coverage to 70% (currently 42%)
2. Add indexes to users.email and projects.created_at

🎯 Overall Score: 87/100 (Production Ready)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
