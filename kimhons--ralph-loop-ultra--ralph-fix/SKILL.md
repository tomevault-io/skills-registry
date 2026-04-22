---
name: ralph-fix
description: Quick-fix mode for existing projects. Auto-detects project type, runs diagnostics, and creates a targeted fix plan. Use when you need to fix bugs or issues in an existing codebase without full PRD setup. Use when this capability is needed.
metadata:
  author: kimhons
---

## Ralph Ultra Quick Fix

Rapidly diagnose and fix issues in an existing project.

### What this does

1. **Auto-detects** project type and framework
2. **Runs diagnostics** — environment-doctor, security-auditor, flaky-test-detector
3. **Creates minimal** `.ralph-ultra/` setup if not present
4. **Generates fix PRD** — Targeted stories for identified issues
5. **Optionally runs** the fix loop immediately

### Usage

```
/ralph-ultra:ralph-fix [--dir path] [--analyze-only] [--auto-run]
```

### Options

| Option | Default | Description |
|--------|---------|-------------|
| `--dir` | `.` | Project directory to fix |
| `--analyze-only` | false | Only analyze, don't create fix plan |
| `--auto-run` | false | Immediately run fix loop after analysis |

### Diagnostics Run

1. **Environment Doctor** — Runtime versions, missing deps, stale caches
2. **Security Auditor** — Vulnerabilities, hardcoded secrets, insecure patterns
3. **Flaky Test Detector** — Intermittent test failures
4. **Dependency Updater** — Outdated packages, known CVEs
5. **Log Analyzer** — Recent error patterns

### Example

Fix a Node.js project with security issues:
```
/ralph-ultra:ralph-fix --dir ./my-project
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimhons) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
