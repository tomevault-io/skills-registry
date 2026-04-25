---
name: test-adapter
description: Run adapter module tests. First argument is adapter name (matches directory under backend/adapters/). Use when user wants to run adapter tests or mentions /test-adapter command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# /test-adapter - Run Adapter Tests

## Usage
```
/test-adapter rest
/test-adapter h2 LoginStorageTest
/test-adapter external-api
/test-adapter payment PaymentGatewayCreatePaymentTest
```

## Convention

Adapter name maps directly to gradle module: `:adapters:{adapter}:test`

## Action

Parse arguments: first word is adapter name, optional second word is test filter.

Without test filter:
```bash
cd backend && ./gradlew.bat :adapters:{adapter}:test --rerun-tasks
```

With test filter:
```bash
cd backend && ./gradlew.bat :adapters:{adapter}:test --tests "*{filter}*" --rerun-tasks
```

## Output

Report the test results from output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
