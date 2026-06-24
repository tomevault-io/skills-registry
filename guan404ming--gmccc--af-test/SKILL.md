---
name: af-test
description: Write tests for Airflow backend API endpoints following project conventions. Use when this capability is needed.
metadata:
  author: guan404ming
---

# Airflow Test Writer

Write tests for Airflow backend API endpoints.

## Usage
```
/af-test <endpoint file path or description>
```

## Test Location

Tests mirror the source tree:
- `airflow-core/src/airflow/api_fastapi/core_api/routes/ui/foo.py`
- `airflow-core/tests/unit/api_fastapi/core_api/routes/ui/test_foo.py`

## Conventions

### Structure
- One test class per endpoint, named `Test<EndpointFunction>` (e.g. `TestGetPartitionedDagRuns`)
- All test methods inside the class, with descriptive names
- Auth tests in every class: `test_should_response_401`, `test_should_response_403`
- Use `pytestmark = pytest.mark.db_test` at module level
- Use `@pytest.fixture(autouse=True)` for cleanup

### Parametrize over separate methods
- Prefer one parametrized test over multiple methods with similar setup
- Use `@pytest.mark.parametrize` with `ids` for all variants
- Vary inputs (counts, booleans, enums) and derive expected values in the test body
- Keep assertions generic enough to work across all parameter combinations

Example: instead of `test_pending`, `test_fulfilled`, `test_partial`, write one `test_should_response_200` parametrized on `(num_assets, received_count, fulfilled)`.

### Fixtures and helpers
- Use `dag_maker` fixture to create DAGs, call `dag_maker.sync_dagbag_to_db()` after
- Use `session` fixture for direct DB operations (add, flush, commit)
- Use `test_client` for authenticated requests, `unauthenticated_test_client` / `unauthorized_test_client` for auth tests
- Use `assert_queries_count` to verify query efficiency where existing tests do
- Cleanup helpers from `tests_common.test_utils.db`: `clear_db_dags`, `clear_db_serialized_dags`, `clear_db_apdr`, `clear_db_pakl`, etc.

## Instructions

1. **Read the endpoint code** to understand the route, parameters, response model, and dependencies.

2. **Read 1-2 existing test files** in the same directory to match the local style. Use the closest neighbor (e.g. `test_assets.py` for asset-related endpoints).

3. **Write the test file** with:
   - Auth tests (401, 403) for each endpoint class
   - 404 for missing resources
   - One parametrized 200 test covering: happy path, edge cases (empty/partial data), and relevant filter/flag combinations
   - Separate parametrized tests only when the setup or assertions differ significantly

4. **Run ruff:** `prek .:ruff` to fix lint issues.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guan404ming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
