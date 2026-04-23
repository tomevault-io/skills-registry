---
name: e2e-test
description: End-to-end testing workflow covering complete user journeys from start to finish Use when this capability is needed.
metadata:
  author: terrazul-ai
---

# End-to-End Testing Skill

Tests complete user journeys through the application, validating that all components (frontend, backend, integrations) work together correctly.

## When to Use

- Testing complete user flows
- Validating feature integration
- Pre-release verification
- User journey validation
- Cross-component testing
- Acceptance testing

## Capabilities

1. **Journey mapping** - Define complete user paths from start to goal
2. **Multi-step execution** - Execute complex flows sequentially
3. **State validation** - Verify data across frontend and backend
4. **Error flow testing** - Test failure modes and recovery
5. **Performance tracking** - Measure journey timing

## E2E vs Other Testing

| Type | Scope | When |
|------|-------|------|
| Unit | Single function | During development |
| Integration | Component interactions | After integration |
| **E2E** | **Complete journeys** | **Before release** |
| Regression | Existing functionality | After changes |

## Workflow

### Phase 1: Journey Mapping

Define the user journey:
- **User goal** - What the user wants to accomplish
- **Starting point** - Where the journey begins
- **Steps** - Actions to reach the goal
- **Success criteria** - How to know it worked
- **Failure modes** - What could go wrong

### Phase 2: Test Preparation

Set up for execution:
- Create test accounts if needed
- Prepare test data
- Clear previous state
- Configure environment
- Document prerequisites

### Phase 3: Step-by-Step Execution

For each step in the journey:
1. Screenshot before action
2. Perform the action (click, type, navigate)
3. Wait for response
4. Verify expected result
5. Screenshot after action
6. Log any issues

### Phase 4: State Validation

Verify data consistency:
- Frontend shows correct data
- API responses match UI
- Database reflects changes
- No orphaned data

### Phase 5: Reporting

Document journey results:
- Steps completed vs failed
- Issues found with severity
- Performance metrics
- Evidence (screenshots, logs)
- Overall pass/fail

## E2E Test Structure

```markdown
## E2E Test: [Journey Name]

**Goal**: [What user accomplishes]
**User**: [Persona/role]
**Duration**: [Expected time]

### Prerequisites
- [ ] Test account ready
- [ ] Test data available
- [ ] Environment configured

### Journey Steps

| Step | Action | Expected Result | Status |
|------|--------|-----------------|--------|
| 1 | Navigate to homepage | Page loads | |
| 2 | Click sign up | Form appears | |
| 3 | Fill form and submit | Success message | |
| 4 | Check email | Verification link | |
| 5 | Click verification | Account active | |

### Success Criteria
- [ ] User can complete full journey
- [ ] Data persisted correctly
- [ ] No errors in console
- [ ] Performance acceptable
```

## Common E2E Journeys

### New User Onboarding
```
Use the e2e-test skill to test the complete new user signup journey from homepage to first dashboard view
```

Steps:
1. Visit homepage
2. Click sign up CTA
3. Fill registration form
4. Submit registration
5. Verify email (if required)
6. Complete profile setup
7. View dashboard
8. Verify account in database

### Purchase Flow (E-commerce)
```
Use the e2e-test skill to test the complete purchase flow from product browsing to order confirmation
```

Steps:
1. Browse product catalog
2. Search for product
3. View product details
4. Add to cart
5. View cart
6. Proceed to checkout
7. Enter shipping info
8. Enter payment info
9. Review and confirm
10. Verify order confirmation
11. Check order in database

### User Profile Management
```
Use the e2e-test skill to test updating user profile from login to confirmation
```

Steps:
1. Log in to account
2. Navigate to settings
3. Update profile fields
4. Save changes
5. Verify success message
6. Refresh page
7. Verify changes persisted
8. Check database updated

### Data Management CRUD
```
Use the e2e-test skill to test creating, reading, updating, and deleting a resource
```

Steps:
1. Log in
2. Navigate to resource list
3. Create new resource
4. Verify in list
5. View resource details
6. Edit resource
7. Verify changes saved
8. Delete resource
9. Verify removed from list
10. Verify database state

## Error Flow Testing

Test how the system handles failures:

### Network Errors
- What happens when API fails?
- Are error messages helpful?
- Can user recover?

### Validation Errors
- Are errors shown clearly?
- Can user correct and retry?
- Is form state preserved?

### Session Expiry
- What happens when session times out?
- Is user redirected appropriately?
- Is data preserved?

## Usage Examples

### Full Journey Test
```
Use the e2e-test skill to test the complete signup-to-first-purchase journey
```

### Critical Path Validation
```
Use the e2e-test skill to validate the login to dashboard to logout flow
```

### Error Handling Test
```
Use the e2e-test skill to test payment failure handling in the checkout flow
```

### Multi-User Flow
```
Use the e2e-test skill to test the admin approval workflow for new user registration
```

## Best Practices

1. **Isolate tests** - Each E2E test should be independent
2. **Use realistic data** - Test with production-like data
3. **Test failures** - Include error scenarios
4. **Measure timing** - Track journey duration
5. **Clean up** - Remove test data after execution
6. **Document dependencies** - Note what must work for journey to succeed
7. **Screenshot everything** - Visual evidence for each step
8. **Test on realistic browsers** - Match production environment

## E2E Report Format

```markdown
# E2E Test Report: [Journey Name]

**Date**: [Date]
**Duration**: [Time]
**Status**: PASS / FAIL

## Journey: [Description]

### Steps Executed

| Step | Action | Result | Time | Evidence |
|------|--------|--------|------|----------|
| 1 | [Action] | PASS/FAIL | Xs | screenshot-1.png |
| 2 | [Action] | PASS/FAIL | Xs | screenshot-2.png |

### Issues Found
[List any issues with severity]

### Performance
- Total journey time: Xs
- Slowest step: [Step] (Xs)
- API calls: X

### Recommendation
[Pass / Fail - with reasoning]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
