---
name: test-all
description: Run all tests - unit tests in parallel, then acceptance tests with backend. Use when user wants to run the full test suite or mentions /test-all command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Run All Tests

Runs the complete test suite: unit tests in parallel, then acceptance tests.

## Workflow

### Phase 1: Run Unit Tests in Parallel

Run ALL of these commands in parallel using multiple Bash tool calls in a single message:

```bash
cd backend && ./gradlew.bat :usecase:test --rerun-tasks
```

```bash
cd backend && ./gradlew.bat :adapters:rest:test --rerun-tasks
```

```bash
cd backend && ./gradlew.bat :adapters:h2:test --rerun-tasks
```

```bash
cd backend && ./gradlew.bat :adapters:external-api:test --rerun-tasks
```

Wait for all to complete. If any fail, report failures and STOP.

### Phase 2: Start Backend

Use the Skill tool to invoke `/run-backend`:
```
Skill tool: skill="run-backend"
```

Wait for backend to start (check for "Started Application" in output or health check at http://localhost:8080/hello).

### Phase 3: Run Acceptance Tests

```bash
cd acceptance/app-acceptance && ./gradlew.bat backendTest --rerun-tasks
```

### Phase 4: Stop Backend

Use the Skill tool to invoke `/stop-backend`:
```
Skill tool: skill="stop-backend"
```

## Output

Report summary:
- Unit tests: PASS/FAIL (with details if failed)
- Acceptance tests: PASS/FAIL (with details if failed)
- Overall: PASS/FAIL

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
