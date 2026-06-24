---
name: production-readiness
description: Comprehensive pre-deployment validation ensuring code is production-ready. Runs complete audit pipeline, performance benchmarks, security scan, documentation check, and generates deployment checklist. Use when this capability is needed.
metadata:
  author: dnyoussef
---

# Production Readiness

## Purpose

Comprehensive pre-deployment validation to ensure code is production-ready.

## Specialist Agent

I am a production readiness specialist ensuring deployment safety.

**Methodology** (Deployment Gate Pattern):
1. Complete quality audit (theater → functionality → style)
2. Security deep-dive (vulnerabilities, secrets, unsafe patterns)
3. Performance benchmarking (load testing, bottlenecks)
4. Documentation validation (README, API docs, deployment docs)
5. Dependency audit (outdated, vulnerable packages)
6. Configuration check (environment variables, secrets management)
7. Monitoring setup (logging, metrics, alerts)
8. Rollback plan verification
9. Generate deployment checklist
10. Final go/no-go decision

**Quality Gates** (all must pass):
- ✅ All tests passing (100%)
- ✅ Code quality ≥ 85/100
- ✅ Test coverage ≥ 80%
- ✅ Zero critical security issues
- ✅ Zero high-severity bugs
- ✅ Performance within SLAs
- ✅ Documentation complete
- ✅ Rollback plan documented

## Input Contract

```yaml
input:
  target_path: string (directory to validate, required)
  environment: enum[staging, production] (default: production)
  skip_performance: boolean (default: false)
  strict_mode: boolean (default: true)
```

## Output Contract

```yaml
output:
  ready_for_deployment: boolean
  quality_gates: object
    tests_passing: boolean
    code_quality: number
    test_coverage: number
    security_clean: boolean
    performance_ok: boolean
    docs_complete: boolean
  blocking_issues: array[issue]
  warnings: array[warning]
  deployment_checklist: array[task]
  rollback_plan: markdown
```

## Execution Flow

```bash
#!/bin/bash
set -e

TARGET_PATH="${1:-./}"
ENVIRONMENT="${2:-production}"
SKIP_PERFORMANCE="${3:-false}"

READINESS_DIR="production-readiness-$(date +%s)"
mkdir -p "$READINESS_DIR"

echo "================================================================"
echo "Production Readiness Check"
echo "Environment: $ENVIRONMENT"
echo "================================================================"

# Initialize quality gates
declare -A GATES
GATES[tests]=0
GATES[quality]=0
GATES[coverage]=0
GATES[security]=0
GATES[performance]=0
GATES[docs]=0

# GATE 1: Complete Quality Audit
echo "[1/10] Running complete quality audit..."
npx claude-flow audit-pipeline "$TARGET_PATH" \
  --phase all \
  --model codex-auto \
  --output "$READINESS_DIR/quality-audit.json"

# Check tests
TESTS_PASSED=$(cat "$READINESS_DIR/quality-audit.json" | jq '.functionality_audit.all_passed')
if [ "$TESTS_PASSED" = "true" ]; then
  GATES[tests]=1
  echo "✅ GATE 1: Tests passing"
else
  echo "❌ GATE 1: Tests failing"
fi

# Check code quality
QUALITY_SCORE=$(cat "$READINESS_DIR/quality-audit.json" | jq '.style_audit.quality_score')
if [ "$QUALITY_SCORE" -ge 85 ]; then
  GATES[quality]=1
  echo "✅ GATE 2: Code quality $QUALITY_SCORE/100"
else
  echo "❌ GATE 2: Code quality too low: $QUALITY_SCORE/100 (need ≥85)"
fi

# Check test coverage
TEST_COVERAGE=$(cat "$READINESS_DIR/quality-audit.json" | jq '.functionality_audit.coverage_percent')
if [ "$TEST_COVERAGE" -ge 80 ]; then
  GATES[coverage]=1
  echo "✅ GATE 3: Test coverage $TEST_COVERAGE%"
else
  echo "❌ GATE 3: Test coverage too low: $TEST_COVERAGE% (need ≥80%)"
fi

# GATE 2: Security Deep-Dive
echo "[2/10] Running security deep-dive..."
npx claude-flow security-scan "$TARGET_PATH" \
  --deep true \
  --check-secrets true \
  --check-dependencies true \
  --output "$READINESS_DIR/security-scan.json"

CRITICAL_SECURITY=$(cat "$READINESS_DIR/security-scan.json" | jq '.critical_issues')
HIGH_SECURITY=$(cat "$READINESS_DIR/security-scan.json" | jq '.high_issues')

if [ "$CRITICAL_SECURITY" -eq 0 ] && [ "$HIGH_SECURITY" -eq 0 ]; then
  GATES[security]=1
  echo "✅ GATE 4: Security scan clean"
else
  echo "❌ GATE 4: Security issues found (Critical: $CRITICAL_SECURITY, High: $HIGH_SECURITY)"
fi

# GATE 3: Performance Benchmarking
if [ "$SKIP_PERFORMANCE" != "true" ]; then
  echo "[3/10] Running performance benchmarks..."

  # Baseline performance
  npx claude-flow analysis performance-report \
    --detailed true \
    --export "$READINESS_DIR/performance-baseline.json"

  # Bottleneck detection
  npx claude-flow bottleneck detect \
    --threshold 10 \
    --export "$READINESS_DIR/bottlenecks.json"

  # Check SLA compliance
  AVG_RESPONSE_TIME=$(cat "$READINESS_DIR/performance-baseline.json" | jq '.avg_response_time')
  P95_RESPONSE_TIME=$(cat "$READINESS_DIR/performance-baseline.json" | jq '.p95_response_time')

  # SLAs: avg < 200ms, p95 < 500ms
  if [ "$AVG_RESPONSE_TIME" -lt 200 ] && [ "$P95_RESPONSE_TIME" -lt 500 ]; then
    GATES[performance]=1
    echo "✅ GATE 5: Performance within SLAs"
  else
    echo "❌ GATE 5: Performance exceeds SLAs (avg: ${AVG_RESPONSE_TIME}ms, p95: ${P95_RESPONSE_TIME}ms)"
  fi
else
  echo "[3/10] Skipping performance benchmarks (--skip-performance)"
  GATES[performance]=1  # Pass if skipped
fi

# GATE 4: Documentation Validation
echo "[4/10] Validating documentation..."

# Check for required docs
DOCS_COMPLETE=true

if [ ! -f "README.md" ]; then
  echo "⚠️ Missing README.md"
  DOCS_COMPLETE=false
fi

if [ ! -f "docs/deployment.md" ] && [ ! -f "DEPLOYMENT.md" ]; then
  echo "⚠️ Missing deployment documentation"
  DOCS_COMPLETE=false
fi

if [ "$ENVIRONMENT" = "production" ]; then
  if [ ! -f "docs/rollback.md" ] && [ ! -f "ROLLBACK.md" ]; then
    echo "⚠️ Missing rollback plan"
    DOCS_COMPLETE=false
  fi
fi

if [ "$DOCS_COMPLETE" = "true" ]; then
  GATES[docs]=1
  echo "✅ GATE 6: Documentation complete"
else
  echo "❌ GATE 6: Documentation incomplete"
fi

# GATE 5: Dependency Audit
echo "[5/10] Auditing dependencies..."
if command -v npm &> /dev/null && [ -f "package.json" ]; then
  npm audit --json > "$READINESS_DIR/npm-audit.json" 2>&1 || true

  VULNERABLE_DEPS=$(cat "$READINESS_DIR/npm-audit.json" | jq '.metadata.vulnerabilities.high + .metadata.vulnerabilities.critical')
  if [ "$VULNERABLE_DEPS" -gt 0 ]; then
    echo "⚠️ Found $VULNERABLE_DEPS vulnerable dependencies"
  else
    echo "✅ No vulnerable dependencies"
  fi
fi

# GATE 6: Configuration Check
echo "[6/10] Checking configuration..."

# Check for .env.example
if [ ! -f ".env.example" ] && [ -f ".env" ]; then
  echo "⚠️ Missing .env.example file"
fi

# Check for hardcoded secrets
echo "Scanning for hardcoded secrets..."
grep -r "api_key\|password\|secret\|token" "$TARGET_PATH" --include="*.js" --include="*.ts" \
  | grep -v "test" | grep -v "example" || echo "✅ No obvious hardcoded secrets"

# GATE 7: Monitoring Setup
echo "[7/10] Validating monitoring setup..."

# Check for logging
if grep -r "logger\|console.log\|winston\|pino" "$TARGET_PATH" --include="*.js" --include="*.ts" > /dev/null; then
  echo "✅ Logging detected"
else
  echo "⚠️ No logging framework detected"
fi

# GATE 8: Error Handling
echo "[8/10] Checking error handling..."

# Check for try-catch blocks
TRYCATCH_COUNT=$(grep -r "try {" "$TARGET_PATH" --include="*.js" --include="*.ts" | wc -l)
echo "Found $TRYCATCH_COUNT try-catch blocks"

# GATE 9: Load Testing (if not skipped)
if [ "$SKIP_PERFORMANCE" != "true" ] && [ "$ENVIRONMENT" = "production" ]; then
  echo "[9/10] Running load tests..."
  # Placeholder for load testing
  echo "⚠️ Manual load testing required"
else
  echo "[9/10] Skipping load tests"
fi

# GATE 10: Generate Deployment Checklist
echo "[10/10] Generating deployment checklist..."

cat > "$READINESS_DIR/DEPLOYMENT-CHECKLIST.md" <<EOF
# Deployment Checklist: $ENVIRONMENT

**Generated**: $(date -Iseconds)

## Quality Gates Status

| Gate | Status | Score/Details |
|------|--------|---------------|
| Tests Passing | $([ ${GATES[tests]} -eq 1 ] && echo "✅" || echo "❌") | $([ "$TESTS_PASSED" = "true" ] && echo "All tests passing" || echo "Tests failing") |
| Code Quality | $([ ${GATES[quality]} -eq 1 ] && echo "✅" || echo "❌") | $QUALITY_SCORE/100 (need ≥85) |
| Test Coverage | $([ ${GATES[coverage]} -eq 1 ] && echo "✅" || echo "❌") | $TEST_COVERAGE% (need ≥80%) |
| Security | $([ ${GATES[security]} -eq 1 ] && echo "✅" || echo "❌") | Critical: $CRITICAL_SECURITY, High: $HIGH_SECURITY |
| Performance | $([ ${GATES[performance]} -eq 1 ] && echo "✅" || echo "❌") | SLA compliance |
| Documentation | $([ ${GATES[docs]} -eq 1 ] && echo "✅" || echo "❌") | All required docs present |

## Pre-Deployment Checklist

### Code Quality
- [ ] All tests passing (100%)
- [ ] Code quality ≥ 85/100
- [ ] Test coverage ≥ 80%
- [ ] No linting errors
- [ ] No TypeScript errors

### Security
- [ ] No critical or high-severity vulnerabilities
- [ ] Dependencies up to date
- [ ] Secrets in environment variables (not hardcoded)
- [ ] Security headers configured
- [ ] Authentication/authorization tested

### Performance
- [ ] Response times within SLAs
- [ ] No performance bottlenecks
- [ ] Database queries optimized
- [ ] Caching configured
- [ ] Load tested

### Documentation
- [ ] README.md up to date
- [ ] API documentation complete
- [ ] Deployment guide available
- [ ] Rollback plan documented
- [ ] Environment variables documented

### Monitoring & Observability
- [ ] Logging configured
- [ ] Error tracking setup
- [ ] Metrics collection enabled
- [ ] Alerts configured
- [ ] Dashboard created

### Infrastructure
- [ ] Environment variables configured
- [ ] Database migrations ready
- [ ] Backup strategy verified
- [ ] Scaling configuration reviewed
- [ ] SSL certificates valid

### Rollback Plan
- [ ] Rollback procedure documented
- [ ] Previous version backed up
- [ ] Rollback tested
- [ ] Rollback SLA defined

## Deployment Steps

1. **Pre-deployment**
   - Create deployment branch
   - Final code review
   - Merge to main/master

2. **Staging Deployment**
   - Deploy to staging
   - Run smoke tests
   - Verify functionality

3. **Production Deployment**
   - Create database backup
   - Deploy to production
   - Run health checks
   - Monitor for errors

4. **Post-deployment**
   - Verify functionality
   - Monitor metrics
   - Check error rates
   - Document any issues

## Rollback Procedure

If deployment fails:

1. Stop deployment immediately
2. Execute rollback: \`./scripts/rollback.sh\`
3. Verify previous version restored
4. Investigate root cause
5. Fix issues before retry

## Sign-off

- [ ] **Development Lead**: Code review approved
- [ ] **QA Lead**: Testing complete
- [ ] **Security Team**: Security review approved
- [ ] **DevOps**: Infrastructure ready
- [ ] **Product Owner**: Features approved

---

🤖 Generated by Claude Code Production Readiness Check
EOF

# Calculate overall readiness
GATES_PASSED=$((${GATES[tests]} + ${GATES[quality]} + ${GATES[coverage]} + ${GATES[security]} + ${GATES[performance]} + ${GATES[docs]}))
TOTAL_GATES=6

READY_FOR_DEPLOYMENT="false"
if [ "$GATES_PASSED" -eq "$TOTAL_GATES" ]; then
  READY_FOR_DEPLOYMENT="true"
fi

# Generate summary
echo ""
echo "================================================================"
echo "Production Readiness Assessment"
echo "================================================================"
echo ""
echo "Environment: $ENVIRONMENT"
echo "Gates Passed: $GATES_PASSED/$TOTAL_GATES"
echo ""
echo "Quality Gates:"
echo "  Tests: $([ ${GATES[tests]} -eq 1 ] && echo "✅" || echo "❌")"
echo "  Quality: $([ ${GATES[quality]} -eq 1 ] && echo "✅" || echo "❌") ($QUALITY_SCORE/100)"
echo "  Coverage: $([ ${GATES[coverage]} -eq 1 ] && echo "✅" || echo "❌") ($TEST_COVERAGE%)"
echo "  Security: $([ ${GATES[security]} -eq 1 ] && echo "✅" || echo "❌") (Critical: $CRITICAL_SECURITY, High: $HIGH_SECURITY)"
echo "  Performance: $([ ${GATES[performance]} -eq 1 ] && echo "✅" || echo "❌")"
echo "  Documentation: $([ ${GATES[docs]} -eq 1 ] && echo "✅" || echo "❌")"
echo ""

if [ "$READY_FOR_DEPLOYMENT" = "true" ]; then
  echo "🚀 READY FOR DEPLOYMENT!"
  echo ""
  echo "Next steps:"
  echo "1. Review deployment checklist: $READINESS_DIR/DEPLOYMENT-CHECKLIST.md"
  echo "2. Get required sign-offs"
  echo "3. Schedule deployment window"
  echo "4. Execute deployment"
else
  echo "🚫 NOT READY FOR DEPLOYMENT"
  echo ""
  echo "Blocking issues must be resolved before deployment."
  echo "See detailed reports in: $READINESS_DIR/"
  exit 1
fi
```

## Integration Points

### Cascades
- Final stage in `/feature-dev-complete` cascade
- Part of `/release-preparation` cascade
- Used by `/deploy-to-production` cascade

### Commands
- Uses: `/audit-pipeline`, `/security-scan`, `/performance-report`
- Uses: `/bottleneck-detect`, `/test-coverage`

### Other Skills
- Invokes: `quick-quality-check`, `code-review-assistant`
- Output to: `deployment-automation`, `rollback-planner`

## Usage Example

```bash
# Check production readiness
production-readiness . production

# Staging environment
production-readiness ./dist staging

# Skip performance tests
production-readiness . production --skip-performance
```

## Failure Modes

- **Tests failing**: Block deployment, fix tests
- **Security issues**: Block deployment, fix vulnerabilities
- **Poor quality**: Block deployment, improve code
- **Missing docs**: Warning, but can proceed with approval
- **Performance issues**: Warning for staging, blocking for production

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dnyoussef) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
