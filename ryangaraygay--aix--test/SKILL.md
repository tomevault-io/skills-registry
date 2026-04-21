---
name: test
description: Run test suite for a project. Detects test framework and runs appropriate commands. Use when this capability is needed.
metadata:
  author: ryangaraygay
---

# Skill: test

Run the project's test suite.

## Usage

```
/test              # Run all tests
/test path/to/file # Run specific test file
/test --watch      # Run in watch mode (if supported)
```

## Detection

The skill auto-detects the test framework:

| Indicator | Framework | Command |
|-----------|-----------|---------|
| `vitest` in package.json | Vitest | `npx vitest run` |
| `jest` in package.json | Jest | `npx jest` |
| `mocha` in package.json | Mocha | `npx mocha` |
| `pytest.ini` or `pyproject.toml` | Pytest | `pytest` |
| `go.mod` | Go | `go test ./...` |
| `Cargo.toml` | Rust | `cargo test` |
| `*.test.sh` files | Shell | Run each test file |

## Execution

### 1. Detect Framework

```bash
# Check package.json for JS projects
if [ -f "package.json" ]; then
    if grep -q "vitest" package.json; then
        TEST_CMD="npx vitest run"
    elif grep -q "jest" package.json; then
        TEST_CMD="npx jest"
    fi
fi

# Check for Python
if [ -f "pytest.ini" ] || [ -f "pyproject.toml" ]; then
    TEST_CMD="pytest"
fi

# Check for Go
if [ -f "go.mod" ]; then
    TEST_CMD="go test ./..."
fi
```

### 2. Run Tests

```bash
$TEST_CMD $ARGS
```

### 3. Report Results

```markdown
## Test Results

**Framework**: [detected framework]
**Command**: [command run]

### Summary
- Total: X tests
- Passed: X
- Failed: X
- Skipped: X

### Failed Tests
[List any failures with file:line]

### Coverage (if available)
- Statements: X%
- Branches: X%
- Functions: X%
- Lines: X%
```

## Options

| Option | Description |
|--------|-------------|
| `--watch` | Run in watch mode (re-run on file changes) |
| `--coverage` | Generate coverage report |
| `--verbose` | Show detailed output |
| `--filter <pattern>` | Run only matching tests |

## Error Handling

### No Test Framework Found

```
No test framework detected.

Supported frameworks:
- JavaScript: vitest, jest, mocha (add to package.json)
- Python: pytest (add pytest.ini or pyproject.toml)
- Go: built-in (go.mod detected)
- Rust: built-in (Cargo.toml detected)

To run tests manually: [suggest command]
```

### Tests Fail

```
X test(s) failed.

[Show first 3 failures with context]

Run with --verbose for full output.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryangaraygay) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
