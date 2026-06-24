---
name: tdd-automation-flow
description: Optimizing the developer feedback loop with automated testing triggers and watchers. Use when this capability is needed.
metadata:
  author: jcorpac
---

# TDD Automation Flow

The faster the feedback loop, the more effective your TDD process becomes.

## Local Automation
Use file watchers to run your tests immediately upon saving a file.

### Python (pytest-watch)
```bash
ptw .
```

### JavaScript (Jest/Vitest)
```bash
npm test -- --watch
```

## Essential Flow
1. **Save file** -> **Tests run automatically**.
2. **If Fail**: Write code to pass.
3. **If Pass**: Refactor and repeat.

## CI/CD Integration
TDD doesn't stop at the local machine. Use Git hooks and CI pipelines to enforce the testing discipline.

- **Pre-commit hooks**: Run linting and fast unit tests before allowing a commit.
- **PR Gates**: Prevent merging code that doesn't have 100% test pass rate.
- **Coverage Reports**: Monitor test coverage as a metric for team health (not a target!).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
