---
name: qa-verify
description: AI-powered QA verification using Playwright MCP for visual E2E testing. Use when (1) Verifying UI changes after code modifications, (2) Testing user workflows end-to-end, (3) Checking for visual regressions, (4) Validating form submissions and error states, (5) Exploratory testing of new features, (6) Pre-deployment verification, (7) Debugging UI issues with screenshots Use when this capability is needed.
metadata:
  author: oxmus
---

# QA Verification Skill

AI-powered quality assurance using Playwright MCP for browser automation. Tests the Oxmus platform as a real user would.

## Prerequisites

Playwright MCP must be enabled (already configured in `.claude/settings.local.json`).

Development server must be running:
- Admin Portal: http://localhost:5001
- Client Portal: http://localhost:5002
- Web (Public): http://localhost:5000

## Core Philosophy

1. **Trust nothing** - Validate all claimed functionality visually
2. **Test edge cases** - Empty inputs, long text, special characters, rapid actions
3. **UI-only interaction** - Access through browser like real users, not source code
4. **Screenshot documentation** - Capture visual evidence at each step
5. **Continue testing** - Document bugs but continue testing rather than halting

## Quick Start

```
/qa-verify "Test admin user creation: login, create user, verify in list"
```

## Workflows

### 1. Feature Verification

Verify a specific feature works correctly:

```
1. Navigate to the feature
2. Perform the happy path
3. Screenshot each major step
4. Test edge cases (empty, invalid, boundary values)
5. Verify database state via UI feedback
6. Report pass/fail with evidence
```

### 2. Regression Check

After code changes, verify nothing broke:

```
1. Identify affected areas from git diff
2. Navigate to each affected page
3. Verify rendering is correct
4. Test primary user flows
5. Screenshot any anomalies
6. Compare with expected behavior
```

### 3. Exploratory Testing

Free-form testing to discover issues:

```
1. Start from the main entry point
2. Click around like a curious user
3. Try unexpected inputs
4. Look for UX issues, not just bugs
5. Document all findings with screenshots
```

## Test Scenarios

### Authentication Flows

```markdown
## Admin Portal Login
- URL: http://localhost:5001
- Email field: input[name="identity[email]"]
- Password field: input[name="identity[password]"]
- Submit: button[type="submit"]
- Success: redirects to /dashboard
- Failure: shows error flash message
```

### User Management (Admin)

```markdown
## Create Admin User
- Navigate: /users/new_admin
- Fill form: first_name, last_name, email, password
- Submit and verify flash message
- Check user appears in /users list
```

### Organization Management

```markdown
## Create Organization
- Navigate: /organizations/new
- Fill: name (auto-generates slug), type
- Submit and verify creation
- Check tenant schema created
```

## Verification Checklist

For each test, verify:

- [ ] Page loads without JavaScript errors (check console)
- [ ] Form submits successfully (flash message appears)
- [ ] Data persists (visible in lists/tables)
- [ ] Authorization enforced (can't access unauthorized pages)
- [ ] Error states show appropriate messages
- [ ] Loading states appear for async operations

## Report Format

After testing, generate a report:

```markdown
# QA Verification Report

**Date**: [timestamp]
**Feature**: [what was tested]
**Server**: http://localhost:5001

## Results

### Test 1: [name]
**Status**: PASS / FAIL
**Steps**:
1. [action] - [result]
2. [action] - [result]

**Screenshot**: [filename or inline]

### Issues Found

1. **[severity]**: [description]
   - Steps to reproduce: ...
   - Screenshot: ...

## Summary

- Total tests: X
- Passed: X
- Failed: X
- Issues found: X
```

## Common Issues Checklist

When testing, watch for:

1. **LiveView mount errors** - Check browser console for "mount failed"
2. **Form validation** - Ensure errors display correctly
3. **Tenant isolation** - Can't access other org's data
4. **Authorization** - Correct redirects for unauthorized access
5. **Multi-org switching** - Context changes correctly

## Tips

- Use `browser_snapshot` for accessibility tree (better than screenshots for form testing)
- Use `browser_console_messages` to check for JavaScript errors
- Use `browser_take_screenshot` for visual evidence
- Test with slow typing using `slowly: true` for realistic behavior
- Wait for async operations with `browser_wait_for`

## References

- See `references/test-flows.md` for detailed test scenarios
- See `references/selectors.md` for common element selectors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxmus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
