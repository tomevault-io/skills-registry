---
name: regression-test
description: Run regression testing suite to verify existing functionality still works after changes Use when this capability is needed.
metadata:
  author: terrazul-ai
---

# Regression Testing Skill

Validates that existing functionality continues to work correctly after code changes, deployments, or dependency updates.

## When to Use

- After code deployments
- After dependency updates
- Before releases
- After bug fixes
- After feature additions
- Periodic health checks

## Capabilities

1. **Baseline comparison** - Compare current behavior to known good state
2. **Critical path testing** - Focus on essential functionality
3. **Change impact analysis** - Identify areas affected by changes
4. **Automated execution** - Run systematic regression tests
5. **Diff reporting** - Highlight what changed from baseline

## Workflow

### Phase 1: Scope Definition

Determine regression scope based on changes:
- **Full** - Test all critical paths (major releases, risky changes)
- **Targeted** - Focus on areas affected by specific changes
- **Smoke** - Quick validation of core functionality (daily builds)

### Phase 2: Baseline Identification

Establish expected behavior:
- Review previous test results as baseline
- Document expected responses and states
- Identify key acceptance criteria
- Note known issues to exclude

### Phase 3: Test Suite Preparation

Organize tests by priority:

**P0 - Critical Path (Always Run)**
- Authentication flows
- Core feature functionality
- Data persistence
- Payment/checkout (if applicable)

**P1 - Important Features**
- Secondary features
- User management
- Settings and preferences

**P2 - Edge Cases**
- Error handling
- Boundary conditions
- Rare scenarios

### Phase 4: Test Execution

Execute tests systematically:
1. Run P0 tests first - stop if critical failures
2. Continue with P1 tests
3. Run P2 tests if time permits
4. Capture evidence for all failures
5. Note any new behaviors

### Phase 5: Comparison Analysis

Compare results to baseline:
- **New failures** - Previously passing, now failing
- **New passes** - Previously failing, now passing (verify fix)
- **Flaky tests** - Inconsistent results
- **Unchanged** - Same as baseline

### Phase 6: Reporting

Generate regression report:
- Tests executed vs baseline
- New issues found
- Fixed issues confirmed
- Overall regression status
- Recommendation (pass/fail)

## Test Categories

### Critical Path Tests
Essential functionality that must work:
- User authentication (login, logout, session)
- Core feature workflows
- Data creation and persistence
- Navigation and routing
- API integrations

### Previously Fixed Bugs
Verify fixes haven't regressed:
- Run tests for recently fixed issues
- Confirm fix still in place
- Flag any regressions immediately

### Integration Points
Test system interactions:
- API contract compliance
- Third-party integrations
- Database operations
- Cache behavior

### Performance Baselines
Check performance hasn't degraded:
- Page load times
- API response times
- Memory usage patterns

## Usage Examples

### Full Regression (Before Release)
```
Use the regression-test skill to run full regression suite before the v2.0 release
```

### Targeted Regression (After Change)
```
Use the regression-test skill to test user authentication after the password reset changes were deployed
```

### Smoke Test (Quick Validation)
```
Use the regression-test skill for a quick smoke test before the demo
```

### After Bug Fix
```
Use the regression-test skill to verify the login fix and ensure no regressions in authentication
```

## Regression Report Format

```markdown
# Regression Test Report

**Build/Version**: [Version]
**Date**: [Date]
**Scope**: Full / Targeted / Smoke
**Baseline**: [Previous test date/version]

## Summary

| Metric | Count |
|--------|-------|
| Tests Executed | X |
| Passed | X |
| Failed | X |
| New Failures | X |
| Fixed (Newly Passing) | X |

## Regression Status: PASS / FAIL

## New Failures (Regressions)
[List of tests that were passing but now fail]

## Fixed Issues (Newly Passing)
[List of tests that were failing but now pass]

## Unchanged Failures
[Known failures that remain - verify they're expected]

## Recommendation
[Ship / Block / Investigate]
```

## Best Practices

1. **Maintain baselines** - Keep expected results updated after intentional changes
2. **Prioritize tests** - Run critical paths first
3. **Automate execution** - Reduce manual effort
4. **Track trends** - Monitor regression frequency over time
5. **Document intentional changes** - So they're not flagged as regressions
6. **Run frequently** - Catch regressions early
7. **Focus on changes** - Prioritize areas affected by recent work

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
