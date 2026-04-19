---
name: test-structure
description: Structures and names tests following the Arrange-Act-Assert (AAA) pattern with clear naming conventions. Use when writing tests, reviewing test quality, naming tests, refactoring test suites or fixing issues like 'multiple actions', 'unclear phases', 'generic test names' or 'missing blank lines'. Ensures consistent test structure across your test suite. Use when this capability is needed.
metadata:
  author: l1oret
---

# Test Structure

Pattern for naming and structuring tests into four distinct phases: naming (clear intent), setup (Arrange), execution (Act), and verification (Assert). Creates readable, maintainable tests that clearly communicate intent.

## When to use this skill

- Writing new tests.
- Reviewing test quality or structure.
- Naming or renaming tests.
- Refactoring test suites.
- Code review of test files.
- Identifying unclear or poorly named tests.

## The four phases

Test structure divides each test into four sequential phases. This structure makes tests self-documenting and easier to understand.

1. [Naming - Intent phase](#naming---intent-phase).
2. [Arrange - Setup phase](#arrange---setup-phase).
3. [Act - Execution phase](#act---execution-phase).
4. [Assert - Verification phase](#assert---verification-phase).

### Naming - Intent phase

Define what the test verifies and under what conditions.

```python
def test_enrolls_student():
```

**Pattern:**

```
test_<expected_behavior>
```

_Only if disambiguation needed:_

```
test_<expected_behavior>_when_<state_under_test>
```

#### Guidelines

- Focus on ONE intent per test.
- Start with expected behavior as a verb in third person that reflects the layer being tested.
- For application layer (use cases or application services) name the test after the action being executed using business verbs like `creates`, `generates` or `finishes`.
- For infrastructure layer (API endpoints, CLI commands or message consumers) use technical verbs like `returns`, `runs` or `consumes`.
- Avoid `and` in name - indicates testing multiple things.

**When to use `when`:**

- The `when` clause disambiguates between different scenarios that produce the same outcome.
- The happy path is the default case and does not need a `when` clause.
- Add `when` clause **ONLY** if needed to distinguish from other tests with same behavior.

**Naming exceptions:**

- Always start with `raises_` followed by the specific exception name (e.g. `raises_name_of_the_exception` for `NameOfTheException`).
- Use the specific exception name as the behavior, not generic `raises_error`.
- Omit `when` if the exception name already describes the condition (e.g. `UserNotFound` already implies the user doesn't exist).

**Important:**

- **ALWAYS** use snake_case for the entire test name.

### Arrange - Setup phase

Prepare everything needed for the test: objects, data, preconditions.

```python
maths = SubjectMother.maths()
fake_subject_repository = FakeSubjectRepository()
fake_subject_repository.create(maths)
alice = StudentMother.alice()
fake_student_repository = FakeStudentRepository()
fake_student_repository.create(alice)
```

#### Guidelines

- Create only what this specific test needs.
- Always use builders or factories if they exist for the objects you need.
- Keep setup focused and minimal.

### Act - Execution phase

Execute the single behavior being tested. One action only.

```python
EnrollStudent(fake_subject_repository, fake_student_repository).run('alice', 'maths')
```

#### Guidelines

- Only ONE action per test.
- Multiple actions indicate testing too much or unclear test intent.

### Assert - Verification phase

Verify the expected outcome. Assert behavior, not implementation.

```python
assert maths in fake_student_repository.get('alice').subjects()
```

#### Guidelines

- Assert the behavior, not implementation details.
- Verify state changes, return values or side effects.
- Multiple asserts are acceptable if verifying the same logical concept.
- Avoid logic in assertions.

### Complete example - All four phases

```python
def test_enrolls_student():
    maths = SubjectMother.maths()
    fake_subject_repository = FakeSubjectRepository()
    fake_subject_repository.create(maths)
    alice = StudentMother.alice()
    fake_student_repository = FakeStudentRepository()
    fake_student_repository.create(alice)

    EnrollStudent(fake_subject_repository, fake_student_repository).run('alice', 'maths')

    assert maths in fake_student_repository.get('alice').subjects()
```

## Common anti-patterns

### Naming issues

- **Generic names**: `test_success`, `test_error`. Use descriptive behavior names.
- **Missing behavior**: `test_enrollment`. Include expected outcome (creates, raises, returns).
- **Unnecessary "when"**: Single test with `when` clause. Remove if no disambiguation needed.
- **`and` in name**: `test_creates_and_sends_email`. Split into two tests.
- **`should` prefix**: `test_should_creates_user`. Remove "should", it's redundant.
- **`when` on happy path**: The default case needs no condition.
- **Generic exception**: `test_raises_error_when_x`. Use the specific exception name as behavior.

### Structure issues

- **Missing blank lines**: Phases not visually separated. Add one blank line between each phase.
- **Multiple Acts**: Testing too much in one test. Split into separate tests.
- **Mixed phases**: Asserts before Act or setup mixed with execution.
- **Overly complex Arrange**: Setup too complicated. Use builders, mothers or factories.
- **Excessive setup**: Too many objects in Arrange. Likely testing too much, split into separate tests.
- **Duplicate scenario tests**: Multiple tests for the same input/condition verifying different side effects. Combine into one test with multiple asserts.

## Special cases

### Tests with only three phases

Some tests may lack one phase:

- **No Arrange**: No setup needed. Only Naming, Act and Assert.
- **No Assert**: Testing exceptions where the exception itself is the verification.

### Multiple asserts

Multiple asserts are acceptable when verifying a single logical concept:

- **Good**: Multiple asserts verifying the same logical concept or operation outcome.
- **Bad**: Verifying multiple unrelated behaviors in one test.

**Guideline:** Ask yourself: "If this test fails, is there ONE clear reason?" If yes, multiple asserts are fine.

## Application Strategy

### For new tests

Structure your test with four distinct phases.

1. **Naming**: Define the intent with clear expected behavior.
2. **Arrange**: Setup needed objects and data.
3. **Act**: Execute the single behavior being tested.
4. **Assert**: Verify the expected outcome.

_Arrange, Act and Assert phases must be separated by blank lines._

### For existing tests

1. **Review the name**: Does it follow conventions? Rename if needed.
2. **Identify the action** being tested (the Act phase).
3. **Group code**: Setup before Act, verifications after Act.
4. **Separate AAA phases** with blank lines.
5. **If multiple Acts exist**: Split into separate tests.

## Interaction with TDD

Test structure and TDD are complementary:

- **TDD**: Tells you _when_ to write tests (before production code).
- **Test structure**: Tells you _how_ to name and structure each test.

When practicing TDD, apply naming conventions and AAA structure from the start.

## Expected outcomes

Applying test structure consistently produces tests that are:

- **Clearly named**: Intent obvious from the test name alone.
- **Self-documenting**: Structure reveals what's being tested.
- **Focused**: One behavior per test.
- **Maintainable**: Easy to modify when requirements change.
- **Debuggable**: Failure point is obvious.
- **Consistent**: Same structure across entire test suite.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/l1oret) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
