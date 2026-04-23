---
name: e2e-item-testing
description: Guidelines for testing a single requirement (FR-xxx), AI capability (AI-xxx), or user journey (UJ-xxx) against a running application. Used by e2e-item-tester agent spawned from /fix-e2e command. Use when this capability is needed.
metadata:
  author: wtah
---

# E2E Item Testing Skill

This skill provides guidance for **testing a single item** (requirement, AI capability, or user journey) against a running application. The e2e-item-tester agent executes one item at a time, validates acceptance criteria, captures errors, and persists passing tests.

**CRITICAL**: This agent tests the ACTUAL running application, not static code analysis. The solution MUST be running via `./scripts/local-start-all.sh` before testing.

---

## Core Concept

```
/fix-e2e command
       │
       │  For EACH item (sequentially):
       ▼
┌─────────────────────────────────────────┐
│         e2e-item-tester agent           │
│                                         │
│  1. Verify solution is running          │
│  2. Execute tests for the item          │
│  3. Capture errors if any               │
│  4. If PASS: persist test file          │
│  5. Report PASS/FAIL with details       │
└─────────────────────────────────────────┘
       │
       ▼
  Main logic analyzes result
       │
       ├── PASS → move to next item
       └── FAIL → spawn fix agents → re-test
```

---

## Agent Input

The e2e-item-tester agent receives a prompt containing:

| Section | Content |
|---------|---------|
| **ITEM DETAILS** | ID, Type, Title, Priority, Test Approach |
| **SPECIFICATION** | Relevant section from .product/ files |
| **ACCEPTANCE CRITERIA** | Specific criteria to test |
| **APPLICATION INFO** | Frontend URL, API URL, Log Directory |

---

## Testing Approaches

The agent selects the appropriate testing approach based on item type:

### 1. Playwright (UI Testing)

Use for:
- User journeys (UJ-xxx) with UI interactions
- Requirements with UI components (forms, views)

Tools available:
- `mcp__playwright__browser_navigate` - Navigate to URLs
- `mcp__playwright__browser_click` - Click elements
- `mcp__playwright__browser_type` - Type text
- `mcp__playwright__browser_snapshot` - Capture accessibility tree
- `mcp__playwright__browser_console_messages` - Get console errors
- `mcp__playwright__browser_network_requests` - Get network failures

### 2. API (Backend Testing)

Use for:
- Backend-only requirements
- AI capabilities with API endpoints
- Data operations

Commands (use actual URLs from `.arch-registry/README.md`):
```bash
# GET request
curl -s http://localhost:{port}/api/v1/endpoint

# POST with JSON
curl -s -X POST http://localhost:{port}/api/v1/endpoint \
  -H "Content-Type: application/json" \
  -d '{"key": "value"}'

# POST with file
curl -s -X POST http://localhost:{port}/api/v1/import \
  -F "file=@test-data.csv"
```

### 3. Logs (Processing Verification)

Use for:
- AI processing verification
- Batch operations
- Backend operations not visible via API response

Commands:
```bash
# Check recent logs
tail -n 50 .local-logs/{container}/combined.log

# Search for patterns
grep "completed\|success\|error" .local-logs/{container}/combined.log
```

### 4. Combined Approaches

| Combined Approach | When to Use |
|-------------------|-------------|
| Playwright + Logs | UI triggers backend processing |
| API + Logs | API call triggers async processing |
| Playwright + API | UI displays data from API |

---

## Test Execution Workflow

### Step 1: Verify Solution Running

```bash
./scripts/local-status.sh
```

Expected exit code: `0` (running), `1` (not running)

If not running, report immediately:
```
RESULT: FAIL

Error: Solution is not running.
Start with: ./scripts/local-start-all.sh
```

### Step 2: Execute Tests Based on Approach

#### For Playwright Tests

1. Navigate to starting URL
2. Execute each step/criterion:
   - Perform action (click, type, navigate)
   - Verify expected outcome
   - Capture console/network errors
3. Take snapshot on failure for debugging

#### For API Tests

1. Make HTTP request(s)
2. Verify response status code
3. Verify response body content
4. Check for expected data

#### For Log Verification

1. Trigger the operation (via UI or API)
2. Wait for processing (appropriate timeout)
3. Search logs for expected entries
4. Verify processing completed successfully

### Step 3: Capture Errors

For each failure, capture:

| Field | Description |
|-------|-------------|
| Source | Browser console / Network / API response / Container logs |
| Error | Exact error message |
| Container | Affected container name |
| File | Filename from stack trace (if known) |
| Log context | Relevant log entries |

### Step 4: Report Results

#### On PASS

```
RESULT: PASS

All acceptance criteria verified successfully.

Tested steps:
- [✓] {criterion 1}: {how verified}
- [✓] {criterion 2}: {how verified}
- [✓] {criterion 3}: {how verified}

Test file created: tests/{category}/{id}-{slug}.{test|spec}.{ext}
```

#### On FAIL

```
RESULT: FAIL

Failed criteria:
- [✗] {criterion}: {error details}

Error details:
- Source: {source}
- Error: {error message}
- Container: {container name}
- File (if known): {filename:line}
- Log context:
  ```
  {relevant log entries}
  ```

Passed criteria:
- [✓] {criterion 1}
- [✓] {criterion 2}
```

---

## Test Persistence (On PASS Only)

When all criteria pass, the agent persists the test for CI/CD:

### Step 1: Check for Existing Test

```bash
ls tests/requirements/{id}-*.test.* 2>/dev/null
ls tests/ai-capabilities/{id}-*.test.* 2>/dev/null
ls tests/user-journeys/{id}-*.spec.* 2>/dev/null
```

### Step 2: Create/Update Test File

If test doesn't exist or needs update, create based on item type:

#### For API Tests (Shell Script)

```bash
#!/bin/bash
# Test: {ID} - {Title}
# Source: .product/{REQUIREMENTS|AI_CAPABILITIES|USER_JOURNEYS}.md
# Approach: {API | API + Logs}
# Prerequisites: Solution running (./scripts/local-start-all.sh)

set -e

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
PROJECT_ROOT="$(dirname "$(dirname "$SCRIPT_DIR")")"

# Colors
RED='\033[0;31m'
GREEN='\033[0;32m'
NC='\033[0m'

echo "=========================================="
echo "Test: {ID} - {Title}"
echo "=========================================="

# Verify solution is running
if ! "$PROJECT_ROOT/scripts/local-status.sh" > /dev/null 2>&1; then
    echo -e "${RED}ERROR: Solution not running${NC}"
    echo "Start with: ./scripts/local-start-all.sh"
    exit 1
fi

# Test 1: {Criterion 1}
echo -n "Testing {criterion 1}... "
RESPONSE=$(curl -s {request})
if echo "$RESPONSE" | grep -q "{expected}"; then
    echo -e "${GREEN}PASS${NC}"
else
    echo -e "${RED}FAIL${NC}"
    echo "Response: $RESPONSE"
    exit 1
fi

# Additional tests...

echo ""
echo -e "${GREEN}All tests passed!${NC}"
exit 0
```

#### For UI Tests (Playwright TypeScript)

```typescript
/**
 * Test: {ID} - {Title}
 * Source: .product/USER_JOURNEYS.md
 * Approach: Playwright
 * Prerequisites: Solution running (./scripts/local-start-all.sh)
 */

import { test, expect } from '@playwright/test';
import { execSync } from 'child_process';
import * as path from 'path';

const PROJECT_ROOT = path.resolve(__dirname, '../..');

test.beforeAll(async () => {
  try {
    execSync(`${PROJECT_ROOT}/scripts/local-status.sh`, { stdio: 'ignore' });
  } catch {
    throw new Error('Solution not running. Start with: ./scripts/local-start-all.sh');
  }
});

test.describe('{ID}: {Title}', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('{frontend-url}');
  });

  test('should {criterion 1}', async ({ page }) => {
    // Test implementation
  });

  test('should {criterion 2}', async ({ page }) => {
    // Test implementation
  });
});
```

### Step 3: Update Test Index

Update `tests/README.md` with new test entry:

```markdown
| {ID} | {Title} | {Type} | {category}/{id}-{slug}.{ext} | ✓ |
```

### Step 4: Verify Test Runs

```bash
# For shell scripts
chmod +x tests/{category}/{id}-{slug}.test.sh
./tests/{category}/{id}-{slug}.test.sh

# For Playwright
cd tests && npx playwright test {category}/{id}-{slug}.spec.ts
```

---

## Directory Structure Reference

```
tests/
├── README.md                           # Test suite index
├── run-all.sh                          # Run all tests
├── run-all.ps1                         # Windows version
├── requirements/                       # FR-xxx tests
│   └── fr-{n}-{slug}.test.sh
├── ai-capabilities/                    # AI-xxx tests
│   └── ai-{n}-{slug}.test.sh
├── user-journeys/                      # UJ-xxx tests
│   └── uj-{n}-{slug}.spec.ts
├── fixtures/                           # Shared test data
│   └── test-*.{json|csv}
└── playwright.config.ts                # Playwright config (if UI tests)
```

---

## Environment Context

### LOCAL_RUN Mode

The solution runs with `LOCAL_RUN=true`:
- Authentication is bypassed
- Test user is pre-authenticated
- No login steps required in tests

### Test User

| Field | Value |
|-------|-------|
| ID | test-user-local-dev |
| Email | localdev@test.local |
| Name | Local Developer |
| Role | admin |

### Log Directory

```
.local-logs/
├── .running                    # Lock file (solution is running)
├── pids.txt                    # Process IDs
├── {container}/
│   ├── stdout.log              # Standard output
│   ├── stderr.log              # Error output
│   └── combined.log            # Timestamped combined output
└── startup-summary.log
```

---

## Reading Required Files

Before testing, read these files in order:

1. **Architecture Registry**: `.arch-registry/README.md`
   - Container URLs and ports
   - Container responsibilities

2. **Product Specification** (based on item type):
   - `.product/REQUIREMENTS.md` for FR-xxx
   - `.product/AI_CAPABILITIES.md` for AI-xxx
   - `.product/USER_JOURNEYS.md` for UJ-xxx

3. **Technology Constraints**: `.constraints/TECHNOLOGY.md`
   - Tech stack for test file generation

4. **Container Specs** (for affected containers):
   - `{container}/.specs/technology.md` - Framework info
   - `{container}/.specs/interfaces/` - API contracts

---

## Error Mapping Heuristics

Use `.arch-registry/README.md` to identify container names for error mapping.

### Frontend Errors → frontend container

| Error Pattern | Source | Action |
|---------------|--------|--------|
| `TypeError` in `.tsx/.jsx/.vue` | Browser console | Check component code |
| `ReferenceError` | Browser console | Check variable definitions |
| Element not found | Playwright | Check selector, verify render |

### Backend Errors → api/backend container

| Error Pattern | Source | Action |
|---------------|--------|--------|
| `404 Not Found` | Network/API | Check endpoint exists |
| `500 Internal Server Error` | Network/API | Check container logs |
| Connection refused | Network | Check container is running |

### Service/Processing Errors → service containers

| Error Pattern | Source | Action |
|---------------|--------|--------|
| Processing timeout | Logs | Check service logs |
| Service unavailable | API response | Check container is running |
| Async operation failed | Logs | Check processing logs |

---

## Scope Boundaries

### MUST DO
- Test acceptance criteria exactly as specified
- Capture all errors with full context
- Persist passing tests immediately
- Report clear PASS/FAIL with details

### MUST NOT DO
- Modify application code (leave to fix agents)
- Skip criteria without reporting
- Create tests for failed items
- Run tests in parallel (sequential only)

---

## Common Patterns

### Waiting for Async Operations

```bash
# After triggering operation, wait and check logs
sleep 5
grep "processing.*completed" .local-logs/{container}/combined.log
```

### Verifying UI State After API Call

```typescript
// Trigger action
await page.click('[data-testid="submit"]');

// Wait for response indicator
await expect(page.locator('[data-testid="success"]')).toBeVisible({ timeout: 10000 });
```

### Testing Numeric Response Values

```bash
# Example: Testing a score/confidence value from an API
RESPONSE=$(curl -s -X POST http://localhost:{port}/api/v1/process \
  -H "Content-Type: application/json" \
  -d '{"input": "test data"}')

SCORE=$(echo "$RESPONSE" | jq -r '.score')
if (( $(echo "$SCORE > 0.7" | bc -l) )); then
    echo "PASS: Score $SCORE > 0.7"
else
    echo "FAIL: Score $SCORE <= 0.7"
fi
```

---

## Quick Reference

### Playwright MCP Tools

| Tool | Purpose |
|------|---------|
| `browser_navigate` | Go to URL |
| `browser_click` | Click element |
| `browser_type` | Type text |
| `browser_snapshot` | Get accessibility tree |
| `browser_console_messages` | Get console logs |
| `browser_network_requests` | Get network activity |
| `browser_wait_for` | Wait for text/time |
| `browser_take_screenshot` | Capture screenshot |

### Result Format

```
RESULT: PASS | FAIL

{Details as specified above}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
