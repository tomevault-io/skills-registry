---
name: e2e
description: Playwright E2E testing patterns. Use when working on files in tests/e2e/. Use when this capability is needed.
metadata:
  author: bentefay
---

# E2E Test Guidelines

## Philosophy

- Journey-style tests covering critical flows end-to-end
- **Strongly prefer adding `test.step()` to existing tests** over creating new tests - keeps suite
  fast and avoids duplicating slow setup flows
- Fix flaky tests immediately, regardless of when introduced

## Commands

```bash
pnpm playwright test --reporter=line --max-failures=1 2>&1
pnpm playwright test --workers=4 --repeat-each=5 --reporter=line 2>&1  # flaky detection
```

**NEVER use `--debug` or `--ui`** - opens GUI, blocks forever. This is a common mistake.

## Selectors (priority order)

1. `getByRole()`
2. `getByTestId()`
3. `getByLabel()`
4. `getByText(/regex/i)`

## Critical Rules

- **Assert behaviour, not text** - text changes with copy edits/i18n
- **Independent tests** - use `beforeEach`, don't depend on prior test state
- **No arbitrary waits** - use `toBeEnabled()`, not `waitForTimeout()`

## Helpers

Import from `tests/e2e/helpers/`:

- `createNewIdentity(page)` - full new user flow
- `goToTransactions(page)`, `goToTags(page)`, etc.

Create helpers for multi-step reused flows. Don't wrap single Playwright calls.

## Pattern

```typescript
test.describe("Feature", () => {
    test.beforeEach(async ({ page }) => {
        await createNewIdentity(page);
        await goToFeature(page);
    });

    test("should do thing", async ({ page }) => {
        // Arrange → Act → Assert
    });
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bentefay) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
