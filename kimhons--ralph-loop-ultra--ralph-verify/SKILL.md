---
name: ralph-verify
description: Run the full 5-gate verification pipeline on the current project. Gates: Lint, Types, Tests, Security, Browser. Use before committing or after completing a story to ensure quality. Use when this capability is needed.
metadata:
  author: kimhons
---

## Ralph Ultra Verification Pipeline

Run comprehensive 5-gate verification.

### Gates

| Gate | What | Blocking |
|------|------|----------|
| **Gate 1: Lint** | ESLint, Pylint, rustfmt, gofmt | Yes |
| **Gate 2: Types** | TypeScript tsc, mypy, cargo check | Yes |
| **Gate 3: Tests** | Jest, pytest, cargo test, go test | Yes |
| **Gate 4: Security** | npm audit, pip-audit, semgrep, trivy | Critical/High block |
| **Gate 5: Browser** | Playwright/Puppeteer MCP audits + vision | Accessibility blocks, visual advises |

### Usage

```
/ralph-ultra:ralph-verify [--gate N] [--skip-browser]
```

### Options

| Option | Description |
|--------|-------------|
| `--gate N` | Run only gate N (1-5) |
| `--skip-browser` | Skip Gate 5 browser verification |

### Gate Results

Each gate outputs:
- **PASS** — All checks passed
- **FAIL** — Blocking issues found (must fix before proceeding)
- **WARN** — Non-blocking issues (should fix but won't block)

### Self-Correction Protocol

On failure:
1. Capture full error output
2. Analyze root cause
3. Inject error context into fix prompt
4. Retry (max 3 attempts per gate)
5. Circuit breaker: 3 failures = escalate to human

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimhons) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
