---
name: vcp-test-plan
description: > Use when this capability is needed.
metadata:
  author: z-m-huang
---

# VCP Test Plan

Generate a comprehensive test plan for a specific file or module.

## Step 1: Resolve Config

1. Read `.vcp/config.json` from the project root. Extract the `pluginRoot` field.
2. **If `.vcp/config.json` does not exist or `pluginRoot` is missing:** Stop and tell the user: "No VCP configuration found. Run `/vcp-init` to configure VCP for this project."
3. **Validate `pluginRoot`:** The path must be absolute, contain `/.claude/` (or `\.claude\` on Windows) as a path segment, and contain only safe path characters (letters, digits, `/`, `\`, `-`, `_`, `.`, `:`, and spaces). Reject any path with shell metacharacters (`;`, `&`, `|`, `$`, `` ` ``, `(`, `)`, `{`, `}`, `<`, `>`, `!`, `~`, `#`, `*`, `?`, `[`, `]`, `'`, `"`). If validation fails, stop and tell the user: "Invalid pluginRoot — must be within ~/.claude/ and contain no shell metacharacters. Run `/vcp-init` to fix." Also verify the file `<pluginRoot>/lib/vcp-context-core.ts` exists using Glob. If it does not exist, stop and tell the user: "pluginRoot points to an invalid VCP installation. Run `/vcp-init` to fix."
4. Run the config resolution script via Bash:
   ```bash
   bun "<pluginRoot>/lib/resolve-config.ts" "<project-root>"
   ```
5. Parse the JSON output. It contains: `applicableStandards`, `ignoredRules`, `severity`, `exclude`.

## Step 2: Fetch Applicable Standards

From the `applicableStandards` array in the resolved config, keep only entries where:
- `id` is `core-testing`, OR
- `id` is `core-error-handling`

For each selected standard, use WebFetch to fetch its content from:
```
{entry.url}
```

Extract the **Rules** section and the **Patterns** section from each fetched standard.

## Step 4: Analyze Target Code

**Target path:** `$ARGUMENTS`. If not provided, ask the user which file or module to generate a test plan for.

1. **Read the target code.** Read the file(s) at the specified path.

2. **Identify test-relevant elements:**
   - **Entry points** — Exported functions, public methods, API endpoints, route handlers, CLI commands
   - **External dependencies** — HTTP clients, database connections, file system access, message queues, third-party SDKs, system clock
   - **Validation rules** — Input validation, type checks, authorization checks, business rule enforcement
   - **Error conditions** — Operations that can fail (network, parsing, I/O), explicit error throws, try/catch blocks
   - **State transitions** — Functions that change state (database writes, cache updates, session changes, queue operations)

3. **Identify the testing framework** in use by checking:
   - `package.json` for `jest`, `vitest`, `mocha`, `ava`, etc.
   - `pyproject.toml`/`setup.cfg` for `pytest`, `unittest`
   - `go.mod` for `testing` package usage
   - Existing test files for import patterns

## Step 5: Generate Test Plan

Output a structured test plan following VCP testing standards.

```
### VCP Test Plan — `[file path]`

**Standards:** core-testing, core-error-handling
**Testing framework:** [detected framework]

#### Summary

- **Entry points:** N functions/methods
- **External dependencies:** [list]
- **Estimated tests:** N unit + M integration + P edge cases

---

#### Mock Guidance

**Mock these** (external boundaries):
- `PaymentGateway.charge()` — External payment API
- `db.query()` — Database connection
- `fetch()` / HTTP client — External API calls

**Do NOT mock these** (internal logic — test through them):
- `PriceCalculator.calculate()` — Internal business logic
- `OrderValidator.validate()` — Internal validation
- `formatCurrency()` — Internal utility

---

#### Unit Tests

##### `createOrder(items, userId)` — line 25

| # | Test Case | Input | Expected Output |
|---|-----------|-------|-----------------|
| 1 | Creates order with valid items | `[{id: 1, qty: 2}], "user-1"` | Order object with correct total |
| 2 | Rejects empty items array | `[], "user-1"` | Throws `ValidationError("items required")` |
| 3 | Rejects negative quantity | `[{id: 1, qty: -1}]` | Throws `ValidationError("quantity must be positive")` |
| 4 | Handles single item | `[{id: 1, qty: 1}]` | Order with total = item price |
| 5 | Handles maximum quantity | `[{id: 1, qty: 999999}]` | Order or appropriate limit error |

##### `processPayment(orderId)` — line 58

...

---

#### Integration Tests

| # | Test Case | Components | What It Verifies |
|---|-----------|------------|------------------|
| 1 | Full order flow | createOrder → processPayment → sendConfirmation | End-to-end order creation with mocked payment gateway |
| 2 | Payment failure rollback | createOrder → processPayment (fails) | Order status reverted, no charge persisted |

---

#### Edge Cases Checklist

- [ ] Null/undefined inputs for each function parameter
- [ ] Empty strings and empty arrays
- [ ] Zero and negative numeric values
- [ ] Boundary values (max int, max string length)
- [ ] Unicode and special characters in string inputs
- [ ] Concurrent access (if applicable)
- [ ] Network timeout on external calls
- [ ] Malformed response from external APIs
- [ ] Database connection failure during transaction

---

#### Error Path Tests

| # | Function | Error Condition | Expected Behavior |
|---|----------|-----------------|-------------------|
| 1 | `processPayment` | Payment gateway returns 500 | Throws `PaymentError`, order unchanged |
| 2 | `processPayment` | Payment gateway timeout | Throws `TimeoutError` after configured limit |
| 3 | `createOrder` | Database write fails | Transaction rolled back, throws `DatabaseError` |
```

If the target file has no testable functions (e.g., pure configuration, type definitions): **"No testable functions found in [path]. This file contains [types/config/constants] and does not require a dedicated test plan."**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z-m-huang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
