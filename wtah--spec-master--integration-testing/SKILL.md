---
name: integration-testing
description: Guidelines for testing container integrations: creating isolated environments, running builds and local tests, verifying runtime behavior. Excludes tests requiring external services/APIs. Works after integration-developer completes. Use when this capability is needed.
metadata:
  author: wtah
---

# Integration Testing Skill

This skill provides guidance for **testing and validating** container integrations. The integration-tester verifies that builds succeed, local tests pass, and containers run correctly, using isolated environments for reproducible results.

**IMPORTANT**: Only test what can run locally. Tests requiring external services, APIs, databases, or network dependencies are EXCLUDED.

---

## Core Principle

**You validate local functionality; you don't test external integrations.**

```
Integration Developer creates integration
              ↓
     [Integration Tester]
              ↓
    Environment → Build → Local Tests → Startup Verify
              ↓
         Pass OR Fail with detailed report
```

---

## Technology Agnostic

This skill is **technology-agnostic**. All implementation details depend on:

```
.constraints/TECHNOLOGY.md
```

**Always read this file first** to understand:
- Programming language(s)
- Build tools and commands
- Test framework and commands
- Package manager and dependencies
- Runtime requirements

**Also read** the container's README to understand its specific directory structure and commands.

---

## Testing Scope: Local Only

### INCLUDE in Testing

| Category | Examples |
|----------|----------|
| **Unit tests** | Pure logic, calculations, transformations |
| **Component tests** | Internal behavior with mocks |
| **Build verification** | Compilation, type checking, linting |
| **Import verification** | All modules resolve correctly |
| **Startup verification** | Entrypoint runs without immediate crash |
| **Mocked tests** | Tests using mocked external dependencies |

### EXCLUDE from Testing

| Category | Examples | Why Excluded |
|----------|----------|--------------|
| **External API calls** | REST calls to third-party services | APIs not available |
| **Database connections** | PostgreSQL, MongoDB, Redis | Database not running |
| **Message queues** | RabbitMQ, Kafka, SQS | Queue not available |
| **Cloud services** | S3, Azure Blob, GCS | Credentials unavailable |
| **Network-dependent** | Tests requiring internet | Network may be restricted |

### Test Filtering Commands

```bash
# Python - exclude external markers
pytest -m "not external and not requires_db and not e2e"

# Node.js - ignore external directories
npm test -- --testPathIgnorePatterns="e2e|external"

# Go - short mode skips long/external tests
go test -short ./...
```

---

## Success Factors

### 1. Environment Isolation

| Criterion | Measure |
|-----------|---------|
| **Container-Local** | Env created inside container folder |
| **Not Global** | No system/global package pollution |
| **Gitignored** | Env directory in .gitignore |
| **Reproducible** | Can be recreated from scratch |

### 2. Build Success

| Criterion | Measure |
|-----------|---------|
| **No Compilation Errors** | All source files compile |
| **Type Safety** | Type checks pass (if applicable) |
| **Dependencies Resolved** | All imports/requires work |

### 3. Local Test Success

| Criterion | Measure |
|-----------|---------|
| **Local Tests Run** | All locally-runnable tests executed |
| **Local Tests Pass** | 100% pass rate for local tests |
| **External Skipped** | External tests properly excluded |

### 4. Startup Success

| Criterion | Measure |
|-----------|---------|
| **No Immediate Errors** | Entrypoint runs without crash |
| **Config Loads** | Configuration parsing works |
| **Connection Errors OK** | External service errors expected |

---

## Input: What You Receive

### From Integration Developer

```
{container}/
├── [entrypoint files]          # Structure varies by language
├── [scripts/]                  # Launch scripts (if created)
├── [test directories]          # Structure varies by language
├── .env.example                # Environment template
└── README.md                   # Documentation with commands
```

**Note**: Directory structure varies. Always read README/specs first.

### From Container Specs

```
{container}/.specs/integration.md     # Expected behavior
{container}/.specs/technology.md      # Tech stack
```

### From Constraints

```
.constraints/TECHNOLOGY.md            # Language, tools, commands
```

---

## Output: What You Create

### 1. Isolated Environment

```
{container}/.venv/              # Python
{container}/node_modules/       # Node.js (default)
{container}/.goenv/             # Go (if needed)
{container}/target/             # Rust
```

### 2. Updated .gitignore

```
{container}/.gitignore          # With env directory entry
```

### 3. Test Results Report

Structured report with all validation results.

---

## Workflow

### Step 1: Read Constraints, Specs, and README

```
Read (in order):
├── .constraints/TECHNOLOGY.md         → Language, tools
├── {container}/.specs/integration.md  → Expected behavior
├── {container}/.specs/technology.md   → Container tech stack
└── {container}/README.md              → Build/test commands
```

### Step 2: Create Isolated Environment

Create environment INSIDE the container folder:

#### Python
```bash
cd {container}
python -m venv .venv
source .venv/bin/activate  # or .venv\Scripts\activate
pip install -r requirements.txt
```

#### Node.js
```bash
cd {container}
npm install
```

#### Go
```bash
cd {container}
go mod download
```

### Step 3: Update .gitignore

Ensure `{container}/.gitignore` includes:

```gitignore
# Virtual environment
.venv/
node_modules/
.goenv/
target/

# Environment
.env
```

### Step 4: Run Build Checks

| Language | Command |
|----------|---------|
| Python | `python -m py_compile` / `mypy` |
| TypeScript | `npm run build` / `tsc` |
| Go | `go build ./...` |
| Rust | `cargo build` |

### Step 5: Run Local Tests Only

Execute ONLY locally-runnable tests:

| Framework | Command (Local Only) |
|-----------|---------------------|
| pytest | `pytest -m "not external and not e2e" -v` |
| jest | `npm test -- --testPathIgnorePatterns="e2e\|external"` |
| go test | `go test -short ./...` |

**Identifying external tests**:
- Look for markers: `@pytest.mark.external`, `@pytest.mark.requires_db`
- Check directories: `tests/e2e/`, `tests/external/`
- Scan for imports of HTTP clients, database drivers, SDK clients

### Step 6: Verify Startup

1. Set up minimal environment (dummy values for external services)
2. Run entrypoint with short timeout
3. Check for immediate errors (syntax, import, config)
4. **Ignore** connection errors to external services (expected)

### Step 7: Generate Report

```markdown
## Integration Test Report: {container}

### Environment Setup
- Virtual environment: ✓/✗
- .gitignore updated: ✓/✗
- Dependencies: ✓/✗

### Build Verification
- Status: ✓ PASS / ✗ FAIL
- Errors: {list}

### Local Tests (External Excluded)
- Total local: {n}
- Passed: {n}
- Failed: {n}
- Skipped (external): {n}
- Details: {per test}

### Startup Verification
- Entrypoint runs: ✓/✗
- Startup errors: {list - excluding connection errors}
- Connection errors (expected): {list - informational}

### Overall: ✓ PASS / ✗ FAIL

### Issues (if any)
1. {detailed issue}

### Notes
- External tests skipped: {list}
- These will be tested when services are available
```

---

## Environment Isolation Patterns

### Why Container-Local Environments

| Benefit | Explanation |
|---------|-------------|
| **No Conflicts** | Each container has isolated deps |
| **Parallel Testing** | Test multiple containers simultaneously |
| **Clean State** | Fresh environment each time |
| **Production-Like** | Mirrors containerized deployment |

### Directory Structure

```
{container}/
├── .venv/              # Isolated environment (gitignored)
├── .gitignore          # Includes .venv/
├── [source files]      # Structure varies
├── [test files]        # Structure varies
└── ...
```

### .gitignore Template

```gitignore
# Environments
.venv/
.goenv/
node_modules/
target/

# Config
.env
.env.local

# Cache
.pytest_cache/
__pycache__/
*.pyc
.coverage
```

---

## Error Reporting Patterns

### Build Error

```markdown
### Error: Build Failure

**Type**: Build Error
**Location**: {container}/module.py:42
**Message**:
```
SyntaxError: unexpected indent
```

**Context**:
```python
def process():
    result = compute()
     return result  # <- extra indent
```

**Possible Cause**: Extra space before return statement
```

### Test Failure

```markdown
### Error: Test Failure

**Type**: Test Failure
**Test**: test_validation
**Location**: {container}/tests/test_core.py:15
**Message**:
```
AssertionError: Expected True, got False
```

**Possible Cause**: Validation logic not handling edge case
```

### What NOT to Report as Errors

```markdown
# These are EXPECTED and should NOT fail the test:

ConnectionRefusedError: [Errno 111] Connection refused (database)
TimeoutError: Connection to api.example.com timed out
redis.exceptions.ConnectionError: Error connecting to Redis
```

---

## Quality Checklists

### Before Reporting

#### Environment
- [ ] Created inside container folder
- [ ] Not in global/system location
- [ ] .gitignore includes env directory
- [ ] Dependencies installed successfully

#### Build
- [ ] All source files processed
- [ ] No compilation errors
- [ ] Imports resolve correctly

#### Local Tests
- [ ] Identified local vs external tests
- [ ] External tests excluded
- [ ] All local tests executed
- [ ] Results captured with details

#### Startup
- [ ] Entrypoint attempted
- [ ] Immediate errors captured
- [ ] Connection errors ignored (expected)

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **Global Environment** | Pollutes system, conflicts | Use container-local env |
| **Missing .gitignore** | Env committed to repo | Always update .gitignore |
| **Running External Tests** | Fail due to missing services | Filter to local only |
| **Failing on Connection Errors** | Expected when services unavailable | Ignore them |
| **Vague Error Reports** | Hard to debug | Include file:line, context |
| **Fixing Issues** | Not your role | Report and hand back |
| **Assuming Structure** | Breaks on non-standard layouts | Read README first |

---

## Coordination

### Report Back Contains

1. **Environment status** - Created? Gitignored?
2. **Build results** - Pass/fail with errors
3. **Local test results** - Count, pass/fail, external skipped
4. **Startup results** - Runs? Immediate errors?
5. **Detailed errors** - For integration-developer to fix

### Handoff to Integration Developer

Your error report enables fixes by providing:
- Exact location (file:line)
- Error message
- Context (code snippet)
- Possible cause

### Do NOT

- Fix issues yourself
- Modify any code
- Skip local validation steps
- Report connection errors as failures
- Assume specific directory structures

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
