---
name: tdd
description: | Use when this capability is needed.
metadata:
  author: ando-ar
---

# TDD Skill - Test-Driven Development

Implement features using the Red-Green-Refactor cycle with Codex consultation.

## TDD Cycle

```
Red: Write failing test
  ↓
Green: Write minimal code to pass
  ↓
Refactor: Clean up (tests still pass)
  ↓
Repeat for next test case
```

## Workflow

### Phase 1: Test Design

1. **Confirm Requirements**
   - Input/output specifications
   - Edge cases and error conditions
   - Acceptance criteria

2. **List Test Cases**
   ```markdown
   ## Test Cases for {feature}

   ### Happy Path
   - [ ] test_basic_case: Basic functionality
   - [ ] test_typical_input: Common usage pattern

   ### Edge Cases
   - [ ] test_empty_input: Handle empty data
   - [ ] test_boundary_values: Min/max limits

   ### Error Cases
   - [ ] test_invalid_input: Reject bad data
   - [ ] test_missing_required: Handle missing fields
   ```

3. **Consult Codex for Test Strategy** (optional)
   ```
   Task(subagent_type="general-purpose", prompt="""
   Consult Codex for TDD test design:

   codex exec --model o3 --sandbox read-only --full-auto "
   Design test cases for: {feature}

   Requirements:
   {requirements}

   Suggest:
   1. Essential test cases (happy path)
   2. Edge cases to consider
   3. Error scenarios
   4. Recommended test order
   " 2>/dev/null

   Return concise test case list.
   """)
   ```

### Phase 2: Red-Green-Refactor

#### Step 1: Red - Write Failing Test

```python
# tests/test_{module}.py
import pytest
from src.{module} import {function}


def test_{function}_basic():
    """Test the most basic case."""
    result = {function}(input_data)
    assert result == expected_output
```

Run and confirm failure:
```bash
uv run pytest tests/test_{module}.py -v
```

#### Step 2: Green - Minimal Implementation

Write the **minimum code** to make the test pass:

```python
# src/{module}.py
def {function}(input_data):
    """Implement {feature}."""
    # Minimal implementation - can hardcode initially
    return expected_output
```

Run and confirm pass:
```bash
uv run pytest tests/test_{module}.py -v
```

#### Step 3: Refactor

Improve code quality while keeping tests green:

- Remove duplication
- Improve naming
- Extract functions if needed
- Add type hints

```bash
# Verify tests still pass after refactor
uv run pytest tests/test_{module}.py -v
```

#### Step 4: Next Test Case

Return to Step 1 with the next test case from your list.

### Phase 3: Completion Check

1. **Run Full Test Suite**
   ```bash
   uv run pytest tests/ -v
   ```

2. **Check Coverage**
   ```bash
   uv run pytest tests/ --cov=src --cov-report=term-missing
   ```

3. **Target**: 80%+ coverage

4. **Quality Checks**
   ```bash
   poe quality
   ```

## Report Format

After completing TDD cycle, document results:

```markdown
## TDD Report: {feature}

### Test Cases
- [x] test_basic_case - PASS
- [x] test_edge_case - PASS
- [x] test_error_handling - PASS

### Coverage
- Lines: 85%
- Branches: 78%

### Files Created/Modified
- `src/{module}.py` - Main implementation
- `tests/test_{module}.py` - Test suite

### Notes
- {Any important observations}
```

## Key Principles

1. **Write tests FIRST** - Not after implementation
2. **Keep cycles small** - One test at a time
3. **Refactor after green** - Never refactor red tests
4. **Minimal code** - Only what's needed to pass
5. **Hardcoding is OK** - Improve in refactor phase

## When to Use TDD

- New feature implementation
- Bug fixes (write failing test first)
- Refactoring (ensure tests exist first)
- API development

## Integration with Codex

For complex implementations, consult Codex during Green phase:

```
Task(subagent_type="general-purpose", prompt="""
Consult Codex for TDD implementation:

codex exec --model o3 --sandbox read-only --full-auto "
I have a failing test:

{test code}

Error: {error message}

What's the minimal implementation to make this pass?
" 2>/dev/null

Return implementation suggestion.
""")
```

## Example Session

User: "/tdd implement calculate_recommendation_score"

1. **Design**: List test cases for scoring function
2. **Red**: Write `test_score_basic_input`
3. **Green**: Implement minimal scoring logic
4. **Refactor**: Clean up, add types
5. **Repeat**: Next test case until complete
6. **Report**: Document coverage and results

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ando-ar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
