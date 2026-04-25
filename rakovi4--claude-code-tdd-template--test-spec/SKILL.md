---
name: test-spec
description: Generate BDD test specifications for story in 6 categories (API, UI, Load, Infrastructure, Security, Integration). Use when user wants to create test cases or mentions /test-spec command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Generate Test Specifications

Generate BDD-style test specifications for a story in 6 categories. Each category produces two files: **main** (critical tests) and **extended** (nice-to-have tests).

## Usage
```
/test-spec "Story name"
/test-spec 5              # By MVP story number
/test-spec                # Interactive selection
```

## Workflow

### Phase 1: Context & Story Selection

Read before generating: `ProductSpecification/BriefProductDescription.txt`, `MvpStories.txt`, `ExpectedLoad.txt`, story folder (`stories/*/`): mockups, `*.md`, `endpoints.md`, `story-specifics.txt`.

Parse input: by name (`"Login/Logout"`), by number (`5`), or interactive (list and ask).

### Phase 2: Generate Test Files

Create files in `ProductSpecification/stories/NN-story-name/tests/`:

**Main files (critical ~27-34 total tests):**
- `01_API_Tests.md`, `02_UI_Tests.md`, `03_Load_Tests.md`
- `04_Infrastructure_Tests.md`, `05_Security_Tests.md`, `06_Integration_Tests.md`

**Extended files in `extended/` subfolder (nice-to-have edge cases):**
- `extended/01_API_Tests_Extended.md`
- `extended/02_UI_Tests_Extended.md`
- `extended/03_Load_Tests_Extended.md`
- `extended/04_Infrastructure_Tests_Extended.md`
- `extended/05_Security_Tests_Extended.md`
- `extended/06_Integration_Tests_Extended.md`

Add this header to extended files:
```markdown
> These are additional edge case tests. Implement after core tests pass.
```

### Critical Tests Per Category

#### 01_API_Tests.md (8-12 tests)

Tests are ordered for **sequential TDD implementation** — each section builds on the previous one. You can implement group N without needing group N+1.

Start every generated file with this header:
```markdown
> **Implementation Order**: Tests are numbered for sequential TDD implementation.
> Start with [story-specific progression summary].
```

**Ordering principles (apply in this priority):**
1. **Security guards first** — auth/CSRF checks. Just need middleware, no business logic.
2. **Read operations before writes** — GET before POST/PUT/DELETE. Less infrastructure needed.
3. **Validation before happy path** — within a write group, reject bad input first (needs only validation), then test success (needs full pipeline).
4. **Simple states before complex states** — default/initial state before edge cases (e.g., trial before grace period).
5. **Verification/confirmation flows last** — depend on prior operations succeeding (e.g., email verification after registration, webhook after checkout).

**Structure:** Use `## N. Section Title` for groups, `### N.M Scenario Title` for individual tests. Separate sections with `---`.

**Typical section progression** (adapt to story — skip irrelevant sections, add story-specific ones):
```
## 1. Security Foundation
## 2. Read Current State (GET)
## 3. Create/Submit (POST) — Validation
## 4. Create/Submit (POST) — Happy Path
## 5. Verification/Confirmation/Callback
## 6. Additional Operations (PUT/DELETE)
```

Reference: `ProductSpecification/stories/12-billing-and-subscription/tests/01_API_Tests.md`

#### 02_UI_Tests.md (5-8 tests)

Same TDD-sequential ordering philosophy. Start with what needs the least code, build up.

Start every generated file with the same `> **Implementation Order**` header.

**Ordering principles:**
1. **Page display first** — render, layout, fields visible. Just needs the component, no logic.
2. **Basic interaction before submission** — focus, toggles, input. Needs handlers, no API.
3. **Form submission with loading state** — submit button, spinner. First test triggering API.
4. **Client-side validation display** — inline errors on blur. Needs validation logic wired.
5. **Server response handling** — success/error messages. Needs API integration.
6. **Navigation and post-action flows** — redirects, links to related pages.

**Typical section progression:**
```
## 1. Page Display
## 2. User Interaction
## 3. Form Submission
## 4. Validation Feedback
## 5. Server Response Display
## 6. Navigation
```

#### 03_Load_Tests.md (2-3 tests)
1. Single request response time (<200ms)
2. Concurrent requests (50 users, <500ms)
3. Queue/batch processing performance

#### 04_Infrastructure_Tests.md (2-3 tests)
1. Database connection failure handling
2. Database recovery after failure
3. External service unavailable handling

#### 05_Security_Tests.md (6-8 tests)
1. SQL injection prevention
2. XSS prevention
3. CSRF protection
4. Password hashing verification
5. Rate limiting (brute force protection)
6. Mass assignment protection
7. Input length limits
8. HTTPS enforcement

#### 06_Integration_Tests.md (3-4 tests)
1. External API success flow
2. External API error handling
3. External API timeout handling
4. Token refresh flow

### BDD Format Rules

1. Use Gherkin syntax with domain-specific language (DSL)
2. No technical details in scenario steps
3. Fold repeating sequences into reusable statements
4. End each file with DSL Technical Reference table:

```markdown
## DSL Technical Reference

| DSL Statement | Technical Implementation |
|---------------|-------------------------|
| `an authenticated user` | Valid JWT in Authorization header |
| `the user submits registration` | POST /api/auth/register |
```

### Phase 3: Summary

Report: folder path, files created, test counts per file.

## Rules

- English, Gherkin in Markdown, DSL only (no technical details in steps)
- Main files: critical path (~27-34 total), Extended files: edge cases
- Reference ExpectedLoad.txt for load tests, OWASP Top 10 for security

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
