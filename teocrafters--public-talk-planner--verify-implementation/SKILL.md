---
name: verify-implementation
description: | Use when this capability is needed.
metadata:
  author: teocrafters
---

# Verify Implementation Skill

## Purpose

USE this skill when:

- Implementation of a requirement is complete and needs verification
- You need to validate all aspects: code quality, i18n, API interfaces, and user flows
- Before marking a feature as "implementation complete"
- To ensure quality and completeness standards are met

DO NOT use this skill for:

- In-progress implementations (use during development for quick checks)
- Requirements that haven't been implemented yet
- Simple code reviews without requirements context

## Workflow Overview

This skill coordinates **5 verification phases** in sequence:

1. **Code Implementation Verification** - Check files, components, APIs against requirements
2. **i18n Keys Verification** - Validate all translation keys exist in both languages
3. **API Interface Verification** - Compare backend/frontend interface consistency
4. **Flow Testing with ChromeDevTools** - Test complete user workflows in browser
5. **Final Report Generation** - Comprehensive report with fix recommendations

Each phase builds on the previous, providing progressive validation depth.

---

## Phase 1: Code Implementation Verification

### Goal

Verify all planned code changes are correctly implemented according to specification and project
guidelines.

### Steps

#### 1.1 Load Requirements Context

```markdown
Action: Read active requirement information

- Check `requirements/.current-requirement` for active requirement ID
- Load latest specification from `requirements/[id]/` directory
- Look for `.latest-spec` file or scan for highest version number
- Parse specification metadata: name, version, date, scope
```

Display to user:

```
📋 Loading Requirement Context...
   ID: [requirement-id]
   Name: [requirement-name]
   Version: v[X]
   Date: [creation-date]
```

#### 1.2 Verify File Existence

```markdown
Action: Check all files mentioned in specification exist

- Components in `app/components/`
- Pages in `app/pages/`
- API endpoints in `server/api/`
- Middleware in `app/middleware/`
- Database schema updates in `server/database/schema.ts`
- Utilities in `app/utils/`, `server/utils/`, `shared/utils/`
```

Report:

```
📁 File Verification:
   ✅ Created: [list of new files]
   ✅ Modified: [list of changed files]
   ❌ Missing: [list of expected but not found files]
```

#### 1.3 Code Quality Checks

```markdown
Action: Run automated quality checks

1. Execute: `pnpm typecheck`
   - MUST pass with 0 errors
   - Report any TypeScript errors with file locations
2. Execute: `pnpm lint`
   - MUST pass with 0 errors
   - Report any ESLint errors with file locations
```

If errors found:

```
❌ Quality Check Failed

TypeScript Errors: [count]
[List errors with file:line]

ESLint Errors: [count]
[List errors with file:line]

⚠️ Fix these errors before continuing verification.
```

If no errors:

```
✅ Code Quality: All checks passed
   TypeScript: 0 errors
   ESLint: 0 errors
```

#### 1.4 Pattern Compliance Check

```markdown
Action: Verify code follows project patterns Reference files:

- @AGENTS.md - Core development guidelines
- @.agents/vue-conventions.md - Vue component patterns
- @.agents/date-time-patterns.md - Date/time handling
- @.agents/i18n-patterns.md - Translation patterns
- @.agents/nuxt-ui-4-integration.md - UI component usage
- @.agents/database-patterns.md - Database patterns

Check for:

- Auto-import usage (no imports from `shared/utils/` direct files)
- `#shared` alias for subdirectory imports
- TypeScript strict mode compliance
- Zod schema validation in API endpoints
- i18n key usage (no hardcoded Polish text)
- Date handling with dayjs utilities
- Component structure with `<script setup>`
```

Report issues:

```
⚠️ Pattern Compliance Issues:

[File:line] - [Issue description]
   Recommendation: [How to fix]

[File:line] - [Issue description]
   Recommendation: [How to fix]
```

#### 1.5 Output Phase 1 Results

```markdown
Summary: ✅ Code Implementation: [X/Y] files verified ✅ TypeScript: 0 errors ✅ ESLint: 0 errors ⚠️
Pattern Issues: [count] found
```

If critical issues (TypeScript/ESLint errors), STOP here and recommend fixes.

---

## Phase 2: i18n Keys Verification

### Goal

Ensure all translation keys used in frontend code exist in both Polish (primary) and English
(fallback) locale files.

### Steps

#### 2.1 Extract i18n Keys from Code

```markdown
Action: Search frontend files for translation key usage

- Search pattern: `t\(['"](.+?)['"]\)` in all .vue, .ts files in `app/`
- Search pattern: `\$t\(['"](.+?)['"]\)` in all .vue templates
- Collect all unique translation keys
- Remove duplicates
```

Example keys found:

```
Found [X] unique translation keys:
- publishers.title
- publishers.createButton
- weekendPlanner.coTalkTitle
- validation.phoneInvalid
...
```

#### 2.2 Load Translation Files

```markdown
Action: Read and parse locale files

- Read `i18n/locales/pl.json`
- Read `i18n/locales/en.json`
- Parse JSON structures
- Build key existence maps for both locales
```

#### 2.3 Validate Key Existence

```markdown
Action: Check each key in both locales For each key found in code:

1. Check if exists in pl.json (primary language)
2. Check if exists in en.json (fallback language)
3. Categorize: exists-both, missing-pl, missing-en, missing-both
```

#### 2.4 Cross-Reference Verification

```markdown
Action: Check for unsynchronized keys

- Find keys in pl.json but NOT in en.json
- Find keys in en.json but NOT in pl.json
- Report synchronization issues
```

#### 2.5 Generate Fix Recommendations

For each missing key, provide suggested translation:

````markdown
## Missing Key: "publishers.title"

**Severity:** Moderate **Location:** app/pages/publishers.vue:42 **Found in code:**
{{ $t('publishers.title') }}

### Recommended Fix:

Add to `i18n/locales/pl.json`:

```json
{
  "publishers": {
    "title": "Zwiastunowie"
  }
}
```
````

Add to `i18n/locales/en.json`:

```json
{
  "publishers": {
    "title": "Publishers"
  }
}
```

**Context Analysis:** Based on file name and usage, this appears to be a page title for publishers
management feature.

````

#### 2.6 Output Phase 2 Results

```markdown
✅ i18n Keys Verification Complete

Keys Verified: [X] total
✅ Exist in both locales: [count]
❌ Missing in pl.json: [count]
❌ Missing in en.json: [count]
⚠️ Unsynchronized: [count]

[Detailed list with fix recommendations follows in report]
````

---

## Phase 3: API Interface Verification

### Goal

Detect mismatches between backend API endpoint definitions and frontend API usage to prevent runtime
errors.

### Steps

#### 3.1 Identify Backend API Endpoints

```markdown
Action: Scan server/api/ directory For each endpoint file:

1. Extract HTTP method (from filename: .get.ts, .post.ts, .patch.ts, .delete.ts)
2. Extract endpoint path (from directory structure)
3. Find Zod schema validation (look for validateBody calls)
4. Extract schema fields and types
5. Identify response type (from return statements or JSDoc)
```

Example endpoint analysis:

```
Found Endpoint: POST /api/publishers
  Request Schema: createPublisherSchema
    - firstName: string (required)
    - lastName: string (required)
    - email: string (optional)
    - phone: string (optional)
    - isElder: boolean (required)
  Response Type: Publisher (inferred from return)
```

#### 3.2 Find Frontend API Usage

```markdown
Action: Search frontend for API calls

- Search for `$fetch(` calls in .vue, .ts files in `app/`
- Search for `useFetch(` calls in .vue, .ts files in `app/`
- Extract endpoint URLs
- Extract request body objects
- Extract expected response types (from type annotations or usage)
```

Example frontend usage:

```
Found API Call: POST /api/publishers
  File: app/components/PublisherModal.vue:87
  Request Body:
    - firstName: formData.firstName
    - lastName: formData.lastName
    - email: formData.email
    - phone: formData.phone
    - isElder: formData.isElder.toString() // ⚠️ Type conversion
  Expected Response: Publisher
```

#### 3.3 Compare Interfaces

```markdown
Action: Match frontend calls to backend endpoints For each frontend API call:

1. Find corresponding backend endpoint
2. Compare request body fields:
   - Missing required fields in frontend
   - Extra fields sent by frontend not accepted by backend
   - Type mismatches (string vs number vs boolean)
3. Compare response expectations:
   - Fields expected by frontend but not returned by backend
   - Type mismatches in response data
```

#### 3.4 Generate Fix Recommendations

For each mismatch:

````markdown
## API Interface Mismatch: POST /api/publishers

**Severity:** High **Backend:** server/api/publishers/index.post.ts **Frontend:**
app/components/PublisherModal.vue:87

### Issue: Type Mismatch

**Backend expects:** `isElder: boolean` **Frontend sends:** `formData.isElder.toString()` (string)

### Recommended Fix:

In `app/components/PublisherModal.vue`:

```typescript
// ❌ Wrong:
const body = {
  isElder: formData.isElder.toString(), // Converts to string
}

// ✅ Correct:
const body = {
  isElder: formData.isElder, // Keep as boolean
}
```
````

**Why this matters:** Backend validation will fail, causing 400 Bad Request errors.

````

#### 3.5 Output Phase 3 Results

```markdown
✅ API Interface Verification Complete

Endpoints Verified: [X]
✅ Matching Interfaces: [count]
❌ Request Mismatches: [count]
❌ Response Mismatches: [count]
⚠️ Type Inconsistencies: [count]

[Detailed list with fix recommendations follows in report]
````

---

## Phase 4: Flow Testing with ChromeDevTools MCP

### Goal

Test complete user workflows in running application using browser automation to verify everything
works end-to-end.

### Prerequisites Check

BEFORE starting flow testing, EXPLICITLY ask user:

```markdown
⚠️ MANUAL STEP REQUIRED: Development Server

Flow testing requires a running development server.

Please execute in a separate terminal:

pnpm dev

Wait for the server to start at: http://localhost:3000

📝 Ensure database is seeded with test data if needed.

Press [Enter] when server is ready to continue...
```

WAIT for user confirmation. DO NOT proceed until user confirms server is running.

### Test Account Selection

Based on requirement scope, automatically select appropriate test account from
`tests/fixtures/test-accounts.json`:

```markdown
Action: Determine required test roles

- Read `tests/fixtures/test-accounts.json`
- Analyze requirement specification for permission requirements
- Select appropriate account(s):
  - admin@test.local / TestAdmin123! (admin role)
  - publisher@test.local / TestPublisher123! (publisher role)
  - public_talk_coordinator@test.local / TestPublicTalkCoordinator123!
  - boe_coordinator@test.local / TestBOECoordinator123!
```

Display:

```
🔐 Test Account Selected: [role]
   Email: [email]
   Testing permissions: [list of expected capabilities]
```

### ChromeDevTools MCP Flow Testing

#### 4.1 Browser Setup

```markdown
Action: Initialize browser session

- Use `mcp__chrome-devtools__new_page` with URL: http://localhost:3000
- Use `mcp__chrome-devtools__take_snapshot` to verify page loaded
- Use `mcp__chrome-devtools__list_console_messages` to check for startup errors
```

#### 4.2 Authentication Flow

```markdown
Action: Log in with test account

1. Navigate to login page (if not already there)
2. Take snapshot to identify login form elements
3. Use `mcp__chrome-devtools__fill_form` to fill:
   - Email field
   - Password field
4. Use `mcp__chrome-devtools__click` on submit button
5. Wait for navigation/redirect
6. Take snapshot to confirm logged-in state
7. Verify user menu or profile displays correct user
```

Example:

```
🌐 Testing Authentication...
   ✅ Navigated to login page
   ✅ Filled credentials: publisher@test.local
   ✅ Submitted form
   ✅ Logged in successfully
   ✅ User menu displays: "Test Publisher"
```

#### 4.3 Feature Flow Testing

Based on requirement specification, test each defined user flow:

```markdown
Action: Execute feature workflows For each flow in specification:

1. Navigate to starting page
2. Take snapshot to verify UI elements present
3. Interact with elements using data-testid selectors:
   - Click buttons: `mcp__chrome-devtools__click`
   - Fill forms: `mcp__chrome-devtools__fill_form`
   - Select options: `mcp__chrome-devtools__click` on dropdowns
4. Verify success states after actions
5. Check for error messages or console errors
6. Take screenshots at key points
```

Example flow test:

```
📋 Testing Flow: Create New Publisher

Step 1: Navigate to publishers page
   ✅ URL: http://localhost:3000/publishers
   ✅ Page loaded successfully

Step 2: Open create modal
   ✅ Clicked: [publishers-create-button]
   ✅ Modal appeared: [publisher-modal]

Step 3: Fill publisher form
   ✅ Filled firstName: "Jan"
   ✅ Filled lastName: "Kowalski"
   ✅ Filled email: "jan.kowalski@example.com"
   ✅ Selected isElder: true

Step 4: Submit form
   ✅ Clicked: [publisher-submit-button]
   ✅ Success message appeared
   ✅ Publisher added to list

Console: 0 errors, 0 warnings
```

#### 4.4 Permission Testing

If requirement involves permissions:

```markdown
Action: Test role-based access

1. Test with appropriate role account (logged in)
2. Verify allowed actions work correctly
3. Log out
4. Test with insufficient permission account
5. Verify middleware redirects or UI elements hidden
6. Check for proper error handling
```

Example:

```
🔒 Testing Permissions: Publisher Management

With admin account:
   ✅ Can access /publishers
   ✅ Can create publishers
   ✅ Can edit publishers
   ✅ Can archive publishers

With publisher account:
   ❌ Redirected from /publishers (expected)
   ✅ Middleware protection working
   ✅ No UI elements visible for unauthorized actions
```

#### 4.5 Error Handling Testing

```markdown
Action: Test validation and error scenarios

1. Submit forms with empty required fields
2. Submit forms with invalid data
3. Verify error messages display correctly
4. Verify error messages use i18n keys (Polish)
5. Check console for unexpected errors
```

Example:

```
⚠️ Testing Error Handling: Empty Form Submission

Step 1: Open create modal
Step 2: Submit without filling fields
   ✅ Form validation triggered
   ✅ Error messages displayed in Polish
   ✅ Required field indicators shown
   ✅ Form not submitted
   ✅ No console errors

Console: 0 errors, 0 warnings
```

#### 4.6 Browser Cleanup

```markdown
Action: Clean up browser session

- Take final snapshot of application state
- Collect all console messages: `mcp__chrome-devtools__list_console_messages`
- Close browser: `mcp__chrome-devtools__close_page`
```

### Output Phase 4 Results

```markdown
✅ Flow Testing Complete

Authentication: ✅ Passed Feature Flows: [X/Y] passed Permissions: ✅ Verified Error Handling: ✅
Correct

❌ Issues Found: [List any failures with details]

⚠️ Console Messages: Errors: [count] Warnings: [count] [List significant console output]
```

---

## Phase 5: Final Report Generation

### Goal

Consolidate all verification results into comprehensive report with actionable fix recommendations.

### Steps

#### 5.1 Aggregate Results

```markdown
Action: Collect findings from all phases

- Phase 1 results: code quality, files, patterns
- Phase 2 results: i18n keys status
- Phase 3 results: API interface consistency
- Phase 4 results: flow testing outcomes
- Categorize by severity: critical, moderate, minor
- Calculate completion percentage
```

#### 5.2 Generate Verification Report

Create report file: `requirements/[requirement-id]/[XX]-verification-report.md`

Report structure:

````markdown
# Verification Report: [Requirement Name]

**Verification Date:** [ISO-8601 timestamp] **Specification Version:** v[X] **Implementation
Status:** [Complete / Issues Found / Failed] **Verified By:** verify-implementation skill

---

## Executive Summary

[2-3 sentence overview of verification results]

**Overall Completion:** [percentage]% **Critical Issues:** [count] **Total Issues:** [count]
**Recommendation:** [Ready for production / Fix issues and re-verify / Major rework needed]

---

## Verification Results by Phase

### Phase 1: Code Implementation ✅ / ⚠️ / ❌

**Status:** [Passed / Issues / Failed]

- Files Verified: [X/Y]
- Files Created: [list]
- Files Modified: [list]
- TypeScript Errors: [count]
- ESLint Errors: [count]
- Pattern Compliance Issues: [count]

**Details:** [Detailed findings]

---

### Phase 2: i18n Keys ✅ / ⚠️ / ❌

**Status:** [Passed / Issues / Failed]

- Keys Verified: [count]
- Keys Exist in Both: [count]
- Missing in pl.json: [count]
- Missing in en.json: [count]
- Unsynchronized: [count]

**Missing Keys:**

| Key   | Location    | Suggested pl.json | Suggested en.json |
| ----- | ----------- | ----------------- | ----------------- |
| [key] | [file:line] | [translation]     | [translation]     |

**Details:** [Detailed findings with fix recommendations]

---

### Phase 3: API Interfaces ✅ / ⚠️ / ❌

**Status:** [Passed / Issues / Failed]

- Endpoints Verified: [count]
- Matching Interfaces: [count]
- Request Mismatches: [count]
- Response Mismatches: [count]
- Type Inconsistencies: [count]

**Mismatches Found:**

| Endpoint   | Issue   | Backend    | Frontend | Severity |
| ---------- | ------- | ---------- | -------- | -------- |
| [endpoint] | [issue] | [expected] | [actual] | [level]  |

**Details:** [Detailed findings with fix recommendations]

---

### Phase 4: Flow Testing ✅ / ⚠️ / ❌

**Status:** [Passed / Issues / Failed]

- Flows Tested: [count]
- Flows Passed: [count]
- Roles Tested: [list]
- Issues Found: [count]
- Console Errors: [count]

**Test Results:**

| Flow        | Status  | Details       |
| ----------- | ------- | ------------- |
| [flow name] | ✅ / ❌ | [description] |

**Console Output:**

- Errors: [count] - [list significant errors]
- Warnings: [count] - [list significant warnings]

**Details:** [Detailed findings]

---

## Issues Summary

### Critical Issues (Blocking)

Issues that MUST be fixed before deployment:

1. **[Issue Title]**
   - **Severity:** Critical
   - **Location:** [file:line]
   - **Description:** [what's wrong]
   - **Impact:** [why it matters]
   - **Recommended Fix:**
     ```[language]
     [suggested code or steps]
     ```

---

### Moderate Issues

Issues that should be fixed but don't block deployment:

[Same structure as critical]

---

### Minor Issues

Suggestions for future improvement:

[Same structure as critical]

---

## Fix Recommendations

### Immediate Actions Required

Prioritized list of fixes needed before marking complete:

1. **[Action]**
   - File: [path]
   - Change: [what to do]
   - Command: [if applicable]

2. **[Action]** [...]

---

### Optional Improvements

Nice-to-have improvements for consideration:

[List of suggestions]

---

## Next Steps

### If All Phases Passed:

✅ Implementation is complete and verified ✅ Ready for production deployment ✅ Consider running
E2E test suite for additional coverage

### If Issues Found:

⚠️ Fix issues listed in "Immediate Actions Required" section ⚠️ Re-run verification after fixes:
invoke verify-implementation skill again ⚠️ Critical issues must be resolved before deployment

---

## Verification Metadata

**Skill Version:** verify-implementation v1.0 **Verification Duration:** [time taken] **Tools
Used:** ChromeDevTools MCP, TypeScript, ESLint, i18n parser **Test Account:** [email used]
**Browser:** Chrome DevTools Protocol

---

**Report Generated:** [ISO-8601 timestamp] **Report File:**
requirements/[id]/[XX]-verification-report.md
````

#### 5.3 Update Metadata

Update `requirements/[requirement-id]/metadata.json`:

```json
{
  "verificationHistory": [
    {
      "timestamp": "[ISO-8601]",
      "specVersion": "v[X]",
      "skillUsed": "verify-implementation",
      "phase1_codeImplementation": "passed|issues|failed",
      "phase2_i18nKeys": "passed|issues|failed",
      "phase3_apiInterfaces": "passed|issues|failed",
      "phase4_flowTesting": "passed|issues|failed",
      "overallStatus": "complete|issues|failed",
      "completionPercentage": [0-100],
      "criticalIssues": [count],
      "totalIssues": [count],
      "reportFile": "[XX]-verification-report.md"
    }
  ]
}
```

#### 5.4 Display Summary to User

Show concise summary:

```markdown
🎉 VERIFICATION COMPLETE

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ Overall Status: [Complete ✅ / Issues Found ⚠️ / Failed ❌]
Completion: [percentage]% Critical Issues: [count] Total Issues: [count]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📊 Phase Results: ✅ Code Implementation: [Passed / Issues / Failed] ✅ i18n Keys: [Passed / Issues
/ Failed] ✅ API Interfaces: [Passed / Issues / Failed] ✅ Flow Testing: [Passed / Issues / Failed]

📁 Full Report: requirements/[id]/[XX]-verification-report.md

[Next steps guidance based on results]
```

---

## Error Handling

### Critical Errors (Stop Verification)

If encountered during any phase:

- **TypeScript errors:** Cannot proceed, code won't compile
- **ESLint errors:** Code quality standards not met
- **Dev server not running:** Cannot test flows
- **Requirement file not found:** No context for verification

Action: STOP immediately, report issue, provide fix instructions, do not continue to next phase.

### Non-Critical Issues (Continue Verification)

Can proceed but must be documented:

- Missing i18n keys (can be added)
- API interface mismatches (can be fixed)
- Flow test failures (can be debugged)
- Pattern compliance issues (can be refactored)

Action: CONTINUE to next phase, document all issues, provide fix recommendations in final report.

### User Interruption

If user stops skill mid-execution:

- Save partial results to temporary file
- Log which phase was interrupted
- Provide command to resume from last phase
- Do not update metadata.json until complete

---

## Quality Standards

### Verification Passes When:

- ✅ TypeScript: 0 errors
- ✅ ESLint: 0 errors
- ✅ All i18n keys exist in both pl.json and en.json
- ✅ All API interfaces match between backend and frontend
- ✅ All feature flows complete successfully without errors
- ✅ No critical issues found
- ✅ Console shows 0 errors during flow testing

### Verification Has Issues When:

- ⚠️ Pattern compliance issues found (non-blocking)
- ⚠️ Some i18n keys missing (can be added)
- ⚠️ API interface mismatches found (can be fixed)
- ⚠️ Minor flow test issues (can be debugged)
- ⚠️ Console warnings present (should be reviewed)

### Verification Fails When:

- ❌ TypeScript compilation errors
- ❌ ESLint errors present
- ❌ Critical API interface mismatches causing runtime errors
- ❌ Feature flows completely broken
- ❌ Multiple critical issues found

---

## Integration with Project Guidelines

### Must Reference:

- **@AGENTS.md** - Core development rules
- **@.agents/vue-conventions.md** - Vue component patterns
- **@.agents/i18n-patterns.md** - Translation key validation
- **@.agents/database-patterns.md** - Database schema patterns
- **@.agents/date-time-patterns.md** - Date handling patterns
- **@.agents/nuxt-ui-4-integration.md** - UI component usage
- **@.agents/e2e-testing-patterns.md** - Testing standards

### ChromeDevTools MCP Best Practices:

- Use `data-testid` selectors primarily (most stable)
- Take snapshots before interactions for debugging
- Check console messages after each major action
- Handle dialogs and alerts appropriately
- Test responsive behavior when requirement specifies
- Capture screenshots on failures for documentation

### Test Account Guidelines:

- Always use accounts from `tests/fixtures/test-accounts.json`
- Test role-appropriate features with correct accounts
- Never hardcode credentials in skill or reports
- Log out between different role tests
- Clean up test data if created during testing

---

## Example Usage

```
User: "verify-implementation - check publishers management"

Skill:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 VERIFY IMPLEMENTATION SKILL
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📋 Loading Requirement Context...
   ID: 2025-11-15-1234-publishers-management
   Name: Publishers Management Feature
   Version: v2
   Date: 2025-11-15

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 1: CODE IMPLEMENTATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📁 File Verification:
   ✅ Created: 8 files
      - app/pages/publishers.vue
      - app/components/PublisherModal.vue
      - server/api/publishers/index.get.ts
      - server/api/publishers/index.post.ts
      - [...]
   ✅ Modified: 3 files
      - server/database/schema.ts
      - shared/utils/schemas/index.ts
      - [...]

✅ TypeScript: 0 errors
✅ ESLint: 0 errors
⚠️ Pattern Issues: 2 found
   [Details listed with recommendations]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 2: i18n KEYS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Keys Verified: 47 total
✅ Exist in both locales: 45
❌ Missing in en.json: 2
   - publishers.createButton
   - publishers.archiveConfirm

[Fix recommendations provided]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 3: API INTERFACES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Endpoints Verified: 4
✅ Matching Interfaces: 3
⚠️ Type Inconsistency: 1
   POST /api/publishers - isElder field type mismatch

[Fix recommendation provided]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
PHASE 4: FLOW TESTING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠️ MANUAL STEP: Please start dev server (pnpm dev)
Press [Enter] when ready...

[User starts server]

🔐 Test Account: admin@test.local
🌐 Testing authentication... ✅
📋 Testing flow: Create Publisher... ✅
📋 Testing flow: Edit Publisher... ✅
📋 Testing flow: Archive Publisher... ✅
🔒 Testing permissions... ✅

Console: 0 errors, 0 warnings

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎉 VERIFICATION COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Overall Status: Issues Found ⚠️
Completion: 95%
Critical Issues: 0
Total Issues: 3

📊 Phase Results:
  ⚠️ Code Implementation: Issues (2 pattern issues)
  ⚠️ i18n Keys: Issues (2 missing keys)
  ⚠️ API Interfaces: Issues (1 type mismatch)
  ✅ Flow Testing: Passed

📁 Full Report: requirements/.../12-verification-report.md

Next Steps:
⚠️ Fix 3 issues listed in report
⚠️ Re-run verification after fixes
✅ All flows working, issues are minor
```

---

## Best Practices

### DO:

- ✅ Run verification when implementation feels complete
- ✅ Fix critical issues immediately when found
- ✅ Use detailed fix recommendations from report
- ✅ Re-run verification after making fixes
- ✅ Review console messages carefully
- ✅ Test with appropriate role accounts
- ✅ Document any manual test steps performed

### DON'T:

- ❌ Skip phases to save time (progressive validation important)
- ❌ Ignore pattern compliance issues (technical debt)
- ❌ Manually edit verification reports (generated, should reflect reality)
- ❌ Run without dev server (flow testing will fail)
- ❌ Use production credentials (always use test accounts)
- ❌ Proceed with deployment if critical issues found

---

**END OF SKILL**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teocrafters) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
