---
name: test
description: Run Swift tests and report results. Use when this capability is needed.
metadata:
  author: ombori-hackathon
---

# /test - Run Swift Tests

Run tests for the macOS client.

## Usage

```
/test           # Run all tests
/test <name>    # Run tests matching name
```

## Workflow

### Run Tests

```bash
# All tests
swift test

# Specific test class or method
swift test --filter <TestName>

# Verbose output
swift test --verbose
```

### Interpret Results

- **All tests passed**: Report success
- **Tests failed**:
  1. Show which tests failed
  2. Show error messages
  3. Suggest fixes if obvious

### Common Issues

- **Build failed**: Run `swift build` first to see build errors
- **Test not found**: Check test file is in Tests/SubStackClientTests/
- **Async test timeout**: Increase timeout or check for deadlocks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ombori-hackathon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
