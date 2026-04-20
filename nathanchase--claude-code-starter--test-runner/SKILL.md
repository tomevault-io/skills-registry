---
name: testingtest-runner
description: | Use when this capability is needed.
metadata:
  author: nathanchase
---

# Test Runner

Execute project test suites with proper configuration.

## Quick Commands

```bash
# Unit tests
<test-command>              # Run all
<test-coverage-command>     # With coverage
<test-watch-command>        # Watch mode

# E2E tests
<e2e-command>               # Run all
<e2e-smoke-command>         # Smoke tests only

# Specific tests
<test-runner> <path-to-test-file>
```

## Dev Server Management (for E2E)

```bash
# Start (background)
nohup <dev-command> > dev.log 2>&1 &

# Verify
curl -s http://localhost:<port> | head -1

# Run E2E
<e2e-command>

# CRITICAL: Cleanup
pkill -f "<dev-process-name>"
```

## Debugging

```bash
# Verbose output
<test-runner> --reporter=verbose <test>

# Playwright UI mode
<playwright-runner> <test> --ui

# Headed mode (see browser)
<playwright-runner> <test> --headed
```

## Common Issues

**Server won't start:**
```bash
lsof -i :<port>              # Check port
kill $(lsof -t -i:<port>)    # Free port
```

**Tests hanging:**
```bash
ps aux | grep <process>      # Check processes
pkill -f <process>           # Kill if needed
```

**Coverage not updating:**
```bash
rm -rf coverage/             # Clear cache
<test-coverage-command>      # Regenerate
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nathanchase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
