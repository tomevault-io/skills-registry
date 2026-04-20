---
name: qa
description: > Use when this capability is needed.
metadata:
  author: vledicfranco
---

# QA Health Check

Automated quality gate for Constellation Engine. Phases run sequentially — a failure in phase N skips all subsequent phases.

---

## Argument Levels

| Invocation | Phases | Approx Time |
|------------|--------|-------------|
| `/qa` or `/qa quick` | 1-5 (compile + quality) | ~1min |
| `/qa tests` | 1-6 (+ full test suite) | ~3min |
| `/qa coverage` | 1-7 (+ coverage thresholds) | ~6min |
| `/qa full` | 1-9 (+ extension + dashboard E2E) | ~10min |

Parse the argument from the skill invocation. Default to `quick` if no argument or unrecognized argument.

---

## Check Pipeline

Run each phase sequentially. On failure, skip remaining phases and jump to the summary.

**Windows constraint:** Only one sbt instance can run at a time — never run sbt commands in parallel.

### Phase 1 — Compile

```bash
sbt compile
```

Exit code 0 = pass. Any compilation error = fail and early exit.

### Phase 2 — Format Check

```bash
sbt scalafmtCheckAll
```

- Pass: exit code 0
- Fail: show fix command → `sbt scalafmtAll`

### Phase 3 — Lint Check

```bash
sbt -J-Xmx4g "scalafixAll --check"
```

Note: The `-J-Xmx4g` flag is required — scalafix compiles all test sources and OOMs with default heap.

- Pass: exit code 0
- Fail: show fix command → `sbt scalafixAll`

### Phase 4 — Docs Freshness

```bash
sbt "docGenerator/runMain io.constellation.docgen.FreshnessChecker"
```

- Pass: exit code 0
- Fail: show fix command → `sbt "docGenerator/runMain io.constellation.docgen.DocGenerator"`

### Phase 5 — Ethos Verification

```bash
sbt "docGenerator/runMain io.constellation.docgen.EthosVerifier"
```

- Pass: exit code 0
- Fail: report which invariant references are broken

### Phase 6 — Tests (level `tests` and above)

```bash
sbt -J-Xmx4g test
```

Note: The `-J-Xmx4g` flag is required — parallel test compilation OOMs with default heap.

- Pass: exit code 0
- Fail: report test failures from output
- Known flaky: `AdversarialFuzzingTest` deep-nesting tests may stack overflow during full parallel runs but pass individually. If only these 3 tests fail, re-run `sbt "langParser/test"` to confirm.

### Phase 7 — Coverage (level `coverage` and above)

```bash
sbt -J-Xmx4g coverage test coverageReport coverageAggregate
```

- Pass: exit code 0 and all modules meet their per-module thresholds
- Fail: report which modules are below threshold
- After running, disable coverage mode: `sbt coverageOff`
- Reference per-module thresholds in `COVERAGE.md` (bundled with this skill)

To check detailed per-module results, read the aggregate report:
```
target/scala-3.3.4/scoverage-report/scoverage.xml
```

### Phase 8 — VSCode Extension (level `full` only)

```bash
cd vscode-extension && npm ci && npm run compile
```

- Pass: exit code 0
- Fail: report compilation errors

### Phase 9 — Dashboard E2E (level `full` only)

Requires a running server. Start it, run smoke tests, then stop it.

```bash
# Start server in background
sbt "exampleApp/run" &

# Wait for server to be ready
# Poll GET http://localhost:8080/health until 200 (max 60s)

# Run smoke tests
cd dashboard-tests && npx playwright test --grep @smoke

# Stop server
```

- Pass: all smoke tests green
- Fail: report failed test names

---

## Output Format

After all phases complete (or on early exit), print a summary table:

```
## QA Health Check — <level>

| # | Phase | Status | Duration |
|---|-------|--------|----------|
| 1 | Compile | PASS | 28s |
| 2 | Format | PASS | 4s |
| 3 | Lint | PASS | 8s |
| 4 | Docs | PASS | 3s |
| 5 | Ethos | PASS | 4s |
| 6 | Tests | FAIL | 1m42s |
| 7 | Coverage | SKIP | - |

Result: FAIL (phase 6)
```

Rules:
- Use `PASS`, `FAIL`, or `SKIP` for status
- Skipped phases show `-` for duration
- Final line: `Result: PASS` (all green) or `Result: FAIL (phase N)` (first failure)
- On failure, include the fix suggestion immediately after the table
- Track wall-clock duration for each phase (start/end timestamps from command execution)

---

## Error Recovery Suggestions

| Phase | Fix Command |
|-------|-------------|
| Format | `sbt scalafmtAll` |
| Lint | `sbt scalafixAll` |
| Docs | `sbt "docGenerator/runMain io.constellation.docgen.DocGenerator"` |
| Tests | Re-run failing test: `sbt "module/testOnly *TestName"` |
| Coverage | See `COVERAGE.md` for per-module thresholds |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vledicfranco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
