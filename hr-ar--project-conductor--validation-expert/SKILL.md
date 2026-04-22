---
name: validation-expert
description: Comprehensive validation for Project Conductor using deep research for best practices. Use when user mentions "validate", "check quality", "review code", "test coverage", or before deployment. Use when this capability is needed.
metadata:
  author: hr-ar
---

# Validation Expert (Project Conductor)

**Deep research-driven validation skill specific to Project Conductor's architecture.**

## When to Use

Invoke this skill when:
- User mentions: "validate", "check", "review", "test"
- Before deployment to staging/production
- After major feature implementation
- When quality issues are suspected
- Before creating a pull request
- When user asks "is this production ready?"

## Project Conductor Context

**Tech Stack:**
- Node.js 20+ with TypeScript 5.2.2
- Express.js 4.18.2
- Socket.io 4.7.2 (real-time)
- PostgreSQL 15 (database)
- Redis 7 (caching)
- Jest 29.6.4 (testing)

**Architecture:**
- 7-module workflow (Onboarding → BRD → PRD → Design → Conflicts → Implementation → History)
- RESTful APIs (12 major APIs, 100+ endpoints)
- Real-time collaboration (WebSocket)
- Document-centric (Markdown + YAML frontmatter)

## Validation Methodology

### Step 1: Quick Health Check

```bash
# Run immediate checks
npm run build          # TypeScript compilation
npm run lint          # ESLint
npm test              # Jest test suite
npm run typecheck     # TypeScript strict checks

# Check docker services
docker-compose ps     # PostgreSQL, Redis status
```

### Step 2: Launch Deep Research for Best Practices

**Use codex-deep-research agent** to validate against industry standards:

```markdown
Invoke Task tool with subagent_type="codex-deep-research"

Prompt:
"Research the best practices, validation techniques, and quality standards for a production-ready TypeScript + Express.js + PostgreSQL application similar to Project Conductor.

Project Conductor is a requirements management and workflow orchestration platform with:
- 12 RESTful APIs
- Real-time WebSocket collaboration
- PostgreSQL database with complex queries
- Redis caching
- 7-module workflow system
- Document-centric architecture (Markdown + YAML)

Please provide comprehensive validation checklist covering:

1. **Code Quality:**
   - TypeScript best practices (strict mode, no 'any', proper typing)
   - ESLint rules for Node.js + Express
   - Code organization patterns (controllers, services, models)
   - Error handling patterns
   - Logging best practices (Pino, Winston)

2. **API Design:**
   - RESTful conventions (status codes, response formats)
   - Input validation (express-validator patterns)
   - Rate limiting strategies
   - API versioning (/api/v1)
   - Error response standards

3. **Database:**
   - PostgreSQL query optimization
   - Index strategy validation
   - Connection pool configuration
   - Migration best practices
   - Data integrity constraints

4. **Security:**
   - OWASP Top 10 compliance
   - SQL injection prevention
   - XSS prevention
   - Rate limiting
   - Authentication/authorization (JWT, RBAC)
   - Secrets management

5. **Performance:**
   - Response time targets (p95 <200ms)
   - Caching hit rate (>80%)
   - Database query performance (<50ms)
   - WebSocket latency (<50ms)
   - Memory leak detection

6. **Testing:**
   - Test coverage targets (>75%)
   - Unit test patterns
   - Integration test strategies
   - E2E test scenarios
   - WebSocket testing approaches

7. **Production Readiness:**
   - Environment configuration
   - Graceful shutdown
   - Health check endpoints
   - Monitoring setup (Prometheus, DataDog)
   - Error tracking (Sentry)
   - Logging infrastructure

8. **Deployment (Render specific):**
   - Static file serving validation
   - Environment variable checks
   - Build process verification
   - Path resolution correctness
   - 404 prevention strategies

Provide specific validation commands, test cases, and checklists for each category."
```

### Step 3: Check for AI-Powered Validation Tools

**Use gemini-research-analyst agent** to find modern validation tools:

```markdown
Invoke Task tool with subagent_type="gemini-research-analyst"

Prompt:
"Research if Google's Gemini AI provides any code validation, quality analysis, or automated testing tools that could benefit Project Conductor.

Specifically investigate:
1. Gemini-powered code review tools
2. Automated test generation
3. Security vulnerability scanning
4. Performance profiling
5. API contract testing
6. TypeScript type safety analysis
7. Database query optimization suggestions
8. Real-time monitoring and anomaly detection

If Gemini has relevant tools:
- How to integrate with TypeScript + Express.js
- Cost implications
- Accuracy/reliability compared to traditional tools
- Production-ready status
- Examples of companies using these tools

Also research Google Cloud tools that could enhance validation:
- Cloud Build (CI/CD)
- Error Reporting
- Cloud Monitoring
- Cloud Profiler
- Security Command Center"
```

### Step 4: Execute Comprehensive Validation

After research agents return, run this complete validation suite:

#### 4A. Code Quality Validation

```bash
#!/bin/bash
# comprehensive-validation.sh

echo "📊 Project Conductor Validation Suite"
echo "======================================"

# 1. TypeScript Compilation
echo "\n1️⃣ TypeScript Compilation..."
npm run build
if [ $? -ne 0 ]; then
  echo "❌ TypeScript compilation failed"
  exit 1
fi
echo "✅ TypeScript compilation passed"

# 2. Type Checking (strict mode)
echo "\n2️⃣ TypeScript Strict Checks..."
npx tsc --noEmit --strict
if [ $? -ne 0 ]; then
  echo "❌ Type checking failed"
  exit 1
fi
echo "✅ Type checking passed"

# 3. Linting
echo "\n3️⃣ ESLint..."
npm run lint
if [ $? -ne 0 ]; then
  echo "⚠️  Linting issues found"
fi

# 4. Find 'any' types (anti-pattern)
echo "\n4️⃣ Checking for 'any' types..."
ANY_COUNT=$(grep -r ": any" src/ --include="*.ts" | wc -l)
echo "Found $ANY_COUNT instances of 'any' type"
if [ $ANY_COUNT -gt 10 ]; then
  echo "⚠️  Consider reducing 'any' usage (found $ANY_COUNT, target <10)"
fi

# 5. Find console.log (should use logger)
echo "\n5️⃣ Checking for console.log..."
CONSOLE_COUNT=$(grep -r "console\." src/ --include="*.ts" | grep -v "// " | wc -l)
echo "Found $CONSOLE_COUNT console statements"
if [ $CONSOLE_COUNT -gt 0 ]; then
  echo "⚠️  Replace console.log with logger (found $CONSOLE_COUNT)"
fi

# 6. Check for hardcoded secrets
echo "\n6️⃣ Checking for hardcoded secrets..."
if grep -r "password\s*=\s*['\"]" src/ --include="*.ts" | grep -v "process.env"; then
  echo "❌ Hardcoded passwords found!"
  exit 1
fi
echo "✅ No hardcoded secrets detected"

# 7. Test Suite
echo "\n7️⃣ Running Test Suite..."
npm test -- --coverage
if [ $? -ne 0 ]; then
  echo "❌ Tests failed"
  exit 1
fi
echo "✅ Tests passed"

# 8. Test Coverage Check
echo "\n8️⃣ Checking Test Coverage..."
COVERAGE=$(npm test -- --coverage --silent | grep "All files" | awk '{print $10}' | sed 's/%//')
if (( $(echo "$COVERAGE < 75" | bc -l) )); then
  echo "⚠️  Test coverage is $COVERAGE% (target: 75%)"
else
  echo "✅ Test coverage is $COVERAGE%"
fi
```

#### 4B. API Validation

```typescript
// tests/validation/api-validation.test.ts
import request from 'supertest';
import { app } from '../../src/index';

describe('API Validation', () => {
  describe('Response Format Standards', () => {
    it('should return consistent JSON structure', async () => {
      const res = await request(app).get('/api/v1/health');

      expect(res.status).toBe(200);
      expect(res.body).toHaveProperty('success');
      expect(res.body).toHaveProperty('data');
      expect(typeof res.body.success).toBe('boolean');
    });

    it('should handle errors with proper format', async () => {
      const res = await request(app).get('/api/v1/requirements/invalid-id');

      expect(res.status).toBeGreaterThanOrEqual(400);
      expect(res.body).toHaveProperty('success', false);
      expect(res.body).toHaveProperty('message');
    });
  });

  describe('API Versioning', () => {
    it('all endpoints should be under /api/v1', async () => {
      const res = await request(app).get('/api/v1/health');
      expect(res.status).not.toBe(404);
    });
  });

  describe('Rate Limiting', () => {
    it('should enforce rate limits', async () => {
      const requests = Array(100).fill(null).map(() =>
        request(app).get('/api/v1/requirements')
      );

      const responses = await Promise.all(requests);
      const rateLimited = responses.some(r => r.status === 429);

      expect(rateLimited).toBe(true);
    });
  });

  describe('Input Validation', () => {
    it('should reject invalid input', async () => {
      const res = await request(app)
        .post('/api/v1/requirements')
        .send({ title: '', priority: 'invalid' }); // Invalid data

      expect(res.status).toBe(400);
      expect(res.body.success).toBe(false);
    });

    it('should sanitize SQL injection attempts', async () => {
      const res = await request(app)
        .get('/api/v1/requirements')
        .query({ sortBy: "'; DROP TABLE requirements; --" });

      expect(res.status).not.toBe(500); // Should handle gracefully
    });
  });
});
```

#### 4C. Database Validation

```sql
-- scripts/validate-database.sql
-- Run this to validate database health

-- 1. Check for missing indexes
SELECT
  schemaname,
  tablename,
  indexname
FROM pg_indexes
WHERE schemaname = 'public'
ORDER BY tablename;

-- 2. Find slow queries (from pg_stat_statements)
SELECT
  query,
  calls,
  total_time,
  mean_time,
  max_time
FROM pg_stat_statements
WHERE mean_time > 100 -- Queries slower than 100ms
ORDER BY mean_time DESC
LIMIT 20;

-- 3. Check table sizes
SELECT
  schemaname,
  tablename,
  pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS size
FROM pg_tables
WHERE schemaname = 'public'
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;

-- 4. Check for unused indexes
SELECT
  schemaname,
  tablename,
  indexname,
  idx_scan as index_scans
FROM pg_stat_user_indexes
WHERE idx_scan = 0
  AND indexrelname NOT LIKE '%_pkey'
ORDER BY pg_relation_size(indexrelid) DESC;

-- 5. Connection pool health
SELECT
  count(*),
  state
FROM pg_stat_activity
WHERE datname = current_database()
GROUP BY state;
```

#### 4D. Security Validation

```bash
# scripts/security-audit.sh

echo "🔒 Security Audit"
echo "================"

# 1. Check for known vulnerabilities
echo "\n1️⃣ Checking npm dependencies for vulnerabilities..."
npm audit --audit-level=moderate
if [ $? -ne 0 ]; then
  echo "⚠️  Vulnerabilities found in dependencies"
fi

# 2. Check for exposed secrets
echo "\n2️⃣ Scanning for exposed secrets..."
if command -v gitleaks &> /dev/null; then
  gitleaks detect --source . --verbose
else
  echo "⚠️  gitleaks not installed (recommended: brew install gitleaks)"
fi

# 3. Check OWASP dependencies
echo "\n3️⃣ OWASP Dependency Check..."
if command -v dependency-check &> /dev/null; then
  dependency-check --project "Project Conductor" --scan .
else
  echo "⚠️  OWASP dependency-check not installed"
fi

# 4. Check environment variables
echo "\n4️⃣ Environment Variable Check..."
REQUIRED_ENVS=("DATABASE_URL" "REDIS_URL" "JWT_SECRET" "NODE_ENV")
for env in "${REQUIRED_ENVS[@]}"; do
  if [ -z "${!env}" ]; then
    echo "⚠️  Missing environment variable: $env"
  else
    echo "✅ $env is set"
  fi
done

# 5. SSL/TLS Configuration
echo "\n5️⃣ Checking SSL/TLS..."
if [ "$NODE_ENV" = "production" ]; then
  if [ -z "$SSL_CERT" ] || [ -z "$SSL_KEY" ]; then
    echo "⚠️  SSL certificates not configured for production"
  else
    echo "✅ SSL configured"
  fi
fi
```

#### 4E. Performance Validation

```typescript
// tests/validation/performance.test.ts
import request from 'supertest';
import { app } from '../../src/index';

describe('Performance Validation', () => {
  describe('Response Times', () => {
    it('API should respond in <200ms (p95)', async () => {
      const times: number[] = [];

      // Make 100 requests
      for (let i = 0; i < 100; i++) {
        const start = Date.now();
        await request(app).get('/api/v1/requirements');
        times.push(Date.now() - start);
      }

      times.sort((a, b) => a - b);
      const p95 = times[Math.floor(times.length * 0.95)];

      console.log(`P95 response time: ${p95}ms`);
      expect(p95).toBeLessThan(200);
    });

    it('cached requests should be <50ms', async () => {
      // Prime cache
      await request(app).get('/api/v1/requirements?page=1');

      // Measure cached request
      const start = Date.now();
      await request(app).get('/api/v1/requirements?page=1');
      const duration = Date.now() - start;

      expect(duration).toBeLessThan(50);
    });
  });

  describe('Memory Leaks', () => {
    it('should not leak memory over 1000 requests', async () => {
      const initialMemory = process.memoryUsage().heapUsed;

      // Make 1000 requests
      for (let i = 0; i < 1000; i++) {
        await request(app).get('/api/v1/health');
      }

      // Force garbage collection if available
      if (global.gc) {
        global.gc();
      }

      const finalMemory = process.memoryUsage().heapUsed;
      const memoryIncrease = finalMemory - initialMemory;

      // Memory should not increase by more than 50MB
      expect(memoryIncrease).toBeLessThan(50 * 1024 * 1024);
    });
  });
});
```

#### 4F. Deployment Validation (Render Specific)

```bash
# scripts/render-deployment-validation.sh
# Based on lessons learned from production deployment

echo "🚀 Render Deployment Validation"
echo "==============================="

# 1. Check for files in /public
echo "\n1️⃣ Validating static files..."
PUBLIC_FILES=$(ls -la public/*.html 2>/dev/null | wc -l)
if [ $PUBLIC_FILES -gt 0 ]; then
  echo "✅ Found $PUBLIC_FILES HTML files in /public"
else
  echo "⚠️  No HTML files in /public directory"
fi

# 2. Check for explicit routes that might override static middleware
echo "\n2️⃣ Checking for route conflicts..."
EXPLICIT_ROUTES=$(grep -n "\.sendFile.*publicDir" src/index.ts | wc -l)
if [ $EXPLICIT_ROUTES -gt 0 ]; then
  echo "⚠️  Found $EXPLICIT_ROUTES explicit routes for /public files"
  echo "   These may override static middleware - review needed"
  grep -n "\.sendFile.*publicDir" src/index.ts
fi

# 3. Check for hardcoded localhost URLs
echo "\n3️⃣ Checking for hardcoded URLs..."
if grep -r "localhost:3000" public/ src/ --include="*.ts" --include="*.js" --include="*.html"; then
  echo "⚠️  Hardcoded localhost URLs found - will break on Render"
else
  echo "✅ No hardcoded localhost URLs"
fi

# 4. Verify environment variables are used
echo "\n4️⃣ Checking environment variable usage..."
if grep -r "process.env.PORT" src/index.ts > /dev/null; then
  echo "✅ PORT uses environment variable"
else
  echo "❌ PORT is hardcoded - Render requires process.env.PORT"
fi

# 5. Build test
echo "\n5️⃣ Testing production build..."
npm run build
if [ $? -eq 0 ]; then
  echo "✅ Production build successful"
else
  echo "❌ Production build failed"
  exit 1
fi

# 6. Check git tracking
echo "\n6️⃣ Checking git status..."
UNTRACKED=$(git ls-files --others --exclude-standard public/ | wc -l)
if [ $UNTRACKED -gt 0 ]; then
  echo "⚠️  Untracked files in /public:"
  git ls-files --others --exclude-standard public/
  echo "   These files won't deploy to Render!"
else
  echo "✅ All /public files are tracked in git"
fi
```

### Step 5: Generate Validation Report

```markdown
## Validation Report

**Project**: Project Conductor
**Date**: [ISO timestamp]
**Validator**: validation-expert skill
**Status**: [PASS/FAIL/WARNING]

### Summary
- **Code Quality**: [PASS/FAIL] - [details]
- **API Design**: [PASS/FAIL] - [details]
- **Database**: [PASS/FAIL] - [details]
- **Security**: [PASS/FAIL] - [details]
- **Performance**: [PASS/FAIL] - [details]
- **Testing**: [PASS/FAIL] - [details]
- **Deployment**: [PASS/FAIL] - [details]

### Critical Issues (must fix before production)
1. [Issue with severity]
2. [Issue with severity]

### Warnings (recommended to fix)
1. [Warning with recommendation]
2. [Warning with recommendation]

### Passed Validations
- ✅ [Check description]
- ✅ [Check description]

### Metrics
- **Test Coverage**: [percentage]%
- **TypeScript Strict**: [pass/fail]
- **API Response Time (p95)**: [ms]
- **Database Query Time (p95)**: [ms]
- **Security Vulnerabilities**: [count]
- **Linting Issues**: [count]

### Next Steps
1. Fix critical issues: [list]
2. Address warnings: [list]
3. Re-run validation: `npm run validate`
4. If all pass: Ready for deployment ✅

### Recommendations Based on Research
[Insert findings from codex-deep-research agent]
- Industry best practices not yet implemented
- Tools to consider adding
- Patterns to adopt
```

## Validation Commands

```json
// Add to package.json scripts
{
  "scripts": {
    "validate": "bash scripts/comprehensive-validation.sh",
    "validate:security": "bash scripts/security-audit.sh",
    "validate:deploy": "bash scripts/render-deployment-validation.sh",
    "validate:db": "psql $DATABASE_URL -f scripts/validate-database.sql",
    "validate:all": "npm run validate && npm run validate:security && npm run validate:deploy"
  }
}
```

## Success Criteria

Validation PASSES when:
- ✅ All tests pass with >75% coverage
- ✅ TypeScript compilation succeeds (strict mode)
- ✅ No critical security vulnerabilities
- ✅ API response times <200ms (p95)
- ✅ Database queries <50ms (p95)
- ✅ No hardcoded secrets or passwords
- ✅ All /public files tracked in git
- ✅ Production build succeeds
- ✅ No route conflicts for static files

## Integration with CI/CD

```yaml
# .github/workflows/validation.yml
name: Validation Pipeline

on: [push, pull_request]

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Run comprehensive validation
        run: npm run validate:all

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
```

## Example Invocation

User: "Is Project Conductor ready for production deployment?"

This skill will:
1. Run all validation scripts
2. Research best practices via codex-deep-research
3. Check for AI-powered validation tools via gemini-research-analyst
4. Generate comprehensive validation report
5. Provide specific recommendations with fixes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hr-ar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
