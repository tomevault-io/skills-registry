---
name: code-implementation
description: Guidelines and best practices for implementing code based on component specifications. Covers implementation standards, testing requirements, and code quality criteria. Technology-agnostic. Use when this capability is needed.
metadata:
  author: wtah
---

# Code Implementation Skill

This skill provides guidance for **implementing code** based on specifications created by component-architects. The coding-agent is the **leaf agent** in the architecture pipeline - you implement, not design.

---

## Core Principle

**You implement specifications; you don't design.**

```
Component Architect creates specifications
              ↓
     [Coding Agent / Developer]
              ↓
    Implementation (src/) + Tests (tests/)
              ↓
         Done - no further delegation
```

---

## Technology Agnostic

This skill is **technology-agnostic**. All implementation details depend on:

```
.constraints/TECHNOLOGY.md
```

**Always read this file first** to understand:
- Programming language(s)
- Frameworks and libraries
- File naming conventions
- Directory structure conventions
- Testing framework
- Linting and formatting tools
- Build tools

Adapt all patterns and examples to match the actual technology stack.

---

## Success Factors

A well-implemented component meets these criteria:

### 1. Specification Compliance

| Criterion | Measure |
|-----------|---------|
| **All Classes Implemented** | Every class in `.specs/classes/` has implementation |
| **All Methods Implemented** | Every method in specs is implemented |
| **Contracts Honored** | Interfaces match specs exactly |
| **Error Handling Complete** | All error cases from specs handled |

### 2. Test Coverage

| Criterion | Measure |
|-----------|---------|
| **All Test Cases** | Every test case from specs implemented |
| **Coverage Target** | Per `.constraints/TECHNOLOGY.md` (typically >= 80%) |
| **All Tests Pass** | No failing tests |
| **Integration Tests** | Key paths covered |

### 3. Code Quality

| Criterion | Measure |
|-----------|---------|
| **No Lint Errors** | Passes all linting rules per constraints |
| **Type Safety** | No untyped code (unless justified) |
| **No Hardcoding** | Uses constants/config |
| **Documentation** | Public APIs documented per language conventions |

### 4. Build Success

| Criterion | Measure |
|-----------|---------|
| **Builds Clean** | No errors or warnings |
| **Dependencies Resolved** | All imports work |
| **Package Valid** | Can be imported by other components |

---

## Input: What You Receive

### From Constraints (Read First)

```
.constraints/TECHNOLOGY.md    # Language, framework, coding standards
```

### From Planner Agent (Task List)

```
{container}/{component}/.implementation-plan.md
```

If exists, use as your primary checklist.

### From Component Architect

```
{container}/{component}/.specs/
├── README.md           # Component overview
├── design.md           # Architecture, patterns, implementation order
├── classes/
│   └── {class}.md      # Individual class specifications
├── tests/
│   ├── unit-tests.md   # Unit test specifications
│   └── integration-tests.md
└── decisions/
    └── ADR-{n}.md      # Decisions affecting implementation
```

### From Architecture Registry

```
.arch-registry/components/{container}/{component}.md
```

---

## Output: What You Create

### Implementation Structure

The exact structure depends on `.constraints/TECHNOLOGY.md`. Generic pattern:

```
{container}/{component}/
├── src/                     # Source code (language-specific naming)
│   ├── {exports}            # Public API exports
│   ├── {types}              # Type definitions
│   ├── {errors}             # Error definitions
│   ├── {class/module}       # Implementation files
│   └── {subdir}/            # Organized by concern
└── tests/                   # Tests (language-specific naming)
    ├── {fixtures}           # Test fixtures/helpers
    ├── {unit_tests}         # Unit tests
    └── integration/
        └── {integration_tests}
```

---

## Workflow: Greenfield (New Component)

### Step 1: Read Technology Constraints

```
Read FIRST:
└── .constraints/TECHNOLOGY.md    → Language, frameworks, conventions
```

### Step 2: Read Your Task List and Specifications

```
Read (in order):
├── {component}/.implementation-plan.md       → Task list (if exists)
├── {component}/.specs/design.md              → Overall design
├── {component}/.specs/classes/               → Class specifications
├── {component}/.specs/tests/                 → Test specifications
├── .arch-registry/components/{container}/{component}.md → Context
```

### Step 3: Set Up Component Structure

Create directory structure per technology constraints.

### Step 4: Implement in Order

Follow the **Implementation Order** from `design.md`:

1. **Types/Interfaces** - Define contracts first
2. **Value Objects/Entities** - Data structures with no dependencies
3. **Repositories/Clients** - Data access layer
4. **Services** - Business logic
5. **Controllers/Handlers** - Entry points

### Step 5: Write Tests Alongside

For each class:
1. Read test cases from `.specs/tests/unit-tests.md`
2. Create test file per naming conventions
3. Implement all specified test cases
4. Verify tests pass before moving on

### Step 6: Run Integration Tests

After unit tests:
1. Read scenarios from `.specs/tests/integration-tests.md`
2. Implement integration test scenarios

### Step 7: Verify Quality

Run quality checks per `.constraints/TECHNOLOGY.md`:
- Test suite with coverage
- Linter
- Type checker (if applicable)
- Build

### Step 8: Update Implementation Plan

If `.implementation-plan.md` exists:
1. Mark completed items: `[ ]` → `[x]`
2. Update Summary metrics
3. Add notes about blockers

---

## Workflow: Brownfield (Proposed Changes)

### Step 1: Read Proposed Changes

Check for `## Proposed Changes - {Feature Name}` in specs.

### Step 2: Identify Tasks

| Change Type | Action |
|-------------|--------|
| New class | Implement from new class spec |
| Modified class | Update existing implementation |
| New tests | Add new test cases |
| Bug fix | Fix and add regression test |

### Step 3: Implement Changes

Follow the same implementation order.

### Step 4: Mark Complete

Update `.implementation-plan.md` if exists.

---

## Class Implementation Guidelines

### Reading a Class Spec

`.specs/classes/{class}.md` contains:

```markdown
# Class: ClassName

## Purpose
{What this class does}

## Interface
{Public contract}

## Properties
{State it holds}

## Methods
{Operations - with pre/post conditions}

## Dependencies
{What to inject}

## Test Cases
{What to test}
```

### Implementation Approach

1. **Read the spec completely** before writing code
2. **Define interface/contract first** (if language supports it)
3. **Implement constructor** with dependency injection
4. **Implement methods** in order specified
5. **Add error handling** for all cases in spec
6. **Document** per language conventions

### Key Principles

- Match the spec exactly - don't add extra methods or properties
- Use dependency injection for all external dependencies
- Handle all error cases documented in the spec
- Write idiomatic code for the target language
- Follow naming conventions from `.constraints/TECHNOLOGY.md`

---

## Test Implementation Guidelines

### Reading Test Specs

`.specs/tests/unit-tests.md` contains:

```markdown
## ClassName Tests

| ID | Case | Input | Expected | Priority |
|----|------|-------|----------|----------|
| T1 | Happy path | valid input | success result | P0 |
| T2 | Invalid input | null/empty | ValidationError | P0 |
| T3 | Not found | unknown_id | NotFoundError | P0 |
```

### Implementation Approach

1. **Create test file** per naming conventions
2. **Set up fixtures/mocks** for dependencies
3. **Implement each test case** from spec table
4. **Use Arrange-Act-Assert** pattern
5. **Reference spec IDs** in test names or comments

### Test Naming

Use descriptive names indicating:
- What is being tested
- Under what conditions
- What is expected

```
// Pattern examples (adapt to language):
// test_returns_result_when_input_valid
// should_throw_error_when_input_null
// processDocument_withValidPdf_returnsText
```

---

## Error Handling

### Implement All Error Cases from Specs

Each class spec includes error handling:

```markdown
## Error Handling

| Error Type | Condition | Recovery |
|------------|-----------|----------|
| ValidationError | Invalid input | Return 400 |
| NotFoundError | Entity not found | Return 404 |
| ServiceError | External service fails | Retry, then fail |
```

### Implementation Pattern

```
// Pseudocode - adapt to your language

function methodName(param) {
    // 1. Validate input (guard clauses)
    if (!isValid(param)) {
        throw ValidationError("Invalid param")
    }

    // 2. Check existence
    entity = repository.find(param.id)
    if (!entity) {
        throw NotFoundError("Entity not found: " + param.id)
    }

    // 3. Wrap external calls
    try {
        result = externalService.call(entity)
    } catch (ExternalError e) {
        throw ServiceError("External service failed", cause: e)
    }

    return result
}
```

---

## Naming Conventions

### Directory Names: Use Underscores, Not Hyphens

**CRITICAL**: When creating component directories, ALWAYS use **underscores** (`_`), NOT hyphens (`-`).

| Correct | Incorrect |
|---------|-----------|
| `document_processor` | `document-processor` |
| `product_matching` | `product_matching` |
| `content_generation` | `content_generation` |

**Rationale**: Python (and many other languages) cannot import modules with hyphens. `import document-processor` is a syntax error (Python interprets the hyphen as minus).

This applies to:
- Component directory names
- Source file names (some languages)
- Any directory that will be imported as a module

---

## Code Standards

### General Principles

These apply regardless of language:

1. **Consistency** - Follow conventions from `.constraints/TECHNOLOGY.md`
2. **Readability** - Clear, self-documenting code
3. **Type Safety** - Use types wherever possible
4. **Error Handling** - Never swallow errors silently
5. **Documentation** - Document public APIs
6. **No Magic Values** - Use constants/config
7. **Dependency Injection** - Inject dependencies, don't hardcode

### File Organization

General pattern (adapt to language):

```
1. File/module header (docstring, copyright)
2. Imports/requires (external, then internal)
3. Constants
4. Type definitions (if inline)
5. Classes/Functions
6. Exports (if applicable)
```

### Naming Conventions

Follow `.constraints/TECHNOLOGY.md`, but general patterns:

| Type | Common Patterns |
|------|-----------------|
| Class/Type | PascalCase |
| Function/Method | camelCase or snake_case |
| Constant | UPPER_SNAKE_CASE |
| Private | _prefix or language-specific |
| File | language-specific conventions |

---

## Quality Checklists

### Before Marking Complete

#### Code Quality
- [ ] All classes from `.specs/classes/` implemented
- [ ] All methods from specs implemented
- [ ] All error handling from specs implemented
- [ ] Documentation on all public methods
- [ ] Type safety enforced
- [ ] No debug/print statements (use logging)
- [ ] No hardcoded values

#### Test Quality
- [ ] All test cases from `.specs/tests/` implemented
- [ ] Coverage meets target from constraints
- [ ] All tests passing
- [ ] Mocks properly isolated
- [ ] Integration tests for key paths

#### Build Quality
- [ ] No lint errors
- [ ] No type errors (if applicable)
- [ ] Build succeeds
- [ ] Package can be imported

### Implementation Order Checklist

- [ ] Read `.constraints/TECHNOLOGY.md` for tech stack
- [ ] Read design.md for implementation order
- [ ] Read all class specs before starting
- [ ] Implement types/interfaces first
- [ ] Implement entities (no deps)
- [ ] Implement repositories
- [ ] Implement services
- [ ] Implement handlers/controllers
- [ ] Write tests alongside each class

---

## Coordination with Component Architect

### When to Escalate

1. **Spec is unclear** - Need clarification
2. **Spec has conflict** - Two specs contradict
3. **Design issue found** - Implementation reveals problem
4. **New requirement discovered** - Not in specs

### How to Escalate

Add to implementation notes:

```markdown
## Implementation Notes

### Clarifications Needed

| Question | Spec Reference | Blocking? |
|----------|----------------|-----------|
| {question} | {file:line} | Yes/No |

### Issues Found

| Issue | Spec Reference | Impact |
|-------|----------------|--------|
| {issue} | {file:line} | {impact} |
```

### Do NOT:

- Add methods not in specs
- Change interface contracts
- Add features without spec updates
- Make design decisions

---

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **Implementing Without Specs** | Code won't match architecture | Read specs first |
| **Ignoring Tech Constraints** | Wrong language/framework | Read constraints first |
| **Adding Unspecified Features** | Scope creep | Request spec update |
| **Changing Design** | Breaks architecture | Escalate |
| **Skipping Tests** | No confidence | Test alongside |
| **Ignoring Type Safety** | Runtime errors | Use types |
| **Hardcoding** | Config issues | Use constants |
| **Silent Failures** | Hidden bugs | Handle all errors |
| **Not Following Order** | Dependency issues | Follow order |
| **Hardcoded Mock/Fake Data** | App displays fake data | Always use real API endpoints |

---

## CRITICAL: No Hardcoded Mock Data in Frontends

**NEVER hardcode mock data, fake data, or placeholder data in frontend components.**

### The Rule

Frontend components MUST:
1. **Always call real API endpoints** defined in the architecture
2. **Use actual API client functions** from the API layer
3. **Display loading states** while fetching data
4. **Handle errors gracefully** when API calls fail

Frontend components MUST NOT:
1. ❌ Return hardcoded arrays of fake objects
2. ❌ Use placeholder data "for now" or "until the API is ready"
3. ❌ Import mock data files for display purposes
4. ❌ Comment out API calls and use static data instead

### Why This Matters

Hardcoded mock data:
- **Masks integration issues** - Frontend appears to work but is not connected
- **Creates false confidence** - App looks functional but isn't
- **Gets forgotten** - Mock data ships to production
- **Wastes E2E testing** - Tests pass against fake data, real integration untested

### Correct Pattern

```typescript
// ❌ WRONG - Hardcoded mock data
function VideoList() {
  const videos = [
    { id: '1', title: 'Fake Video 1', status: 'ready' },
    { id: '2', title: 'Fake Video 2', status: 'processing' },
  ];
  return <List items={videos} />;
}

// ✅ CORRECT - Real API call
function VideoList() {
  const { data: videos, isLoading, error } = useVideos();

  if (isLoading) return <Skeleton />;
  if (error) return <ErrorState error={error} />;
  if (!videos?.length) return <EmptyState />;

  return <List items={videos} />;
}
```

### When Backend Isn't Ready

If the backend API is not yet implemented:
1. **Defer the frontend component** - Mark it as deferred in `.implementation-plan.md`
2. **Document the dependency** - "Deferred - requires {api-endpoint} from {container}"
3. **Do NOT create mock data** as a workaround

### Testing with Mock Data

Mock data is ONLY acceptable in:
- **Unit tests** - Mocking API responses for component testing
- **Storybook stories** - Isolated component development
- **Test fixtures** - Integration test setup

Mock data must NEVER appear in production source code (`src/`) outside of test files.

---

## Example: Implementing a Component

### Given:

```
component/.specs/
├── design.md           # Implementation order, patterns
├── classes/
│   ├── parser.md       # Parser spec
│   ├── repository.md   # Repository spec
│   └── service.md      # Service spec
└── tests/
    ├── unit-tests.md
    └── integration-tests.md
```

### Steps:

1. **Read `.constraints/TECHNOLOGY.md`** → Get language, framework
2. **Read `.implementation-plan.md`** → Get task list (if exists)
3. **Set up structure** → Per constraints
4. **Implement in order**:
   - Types/interfaces
   - Parser (no dependencies)
   - Repository (data access)
   - Service (depends on above)
5. **Write tests alongside** → Per test specs
6. **Verify quality** → Lint, type check, coverage, build
7. **Update plan** → Mark items complete

### Output (generic):

```
component/
├── .specs/              # Read-only
├── src/                 # Per tech stack
│   ├── {exports}
│   ├── {types}
│   ├── {errors}
│   ├── parser.{ext}
│   ├── repository.{ext}
│   └── service.{ext}
└── tests/               # Per tech stack
    ├── {fixtures}
    ├── {test_parser}
    ├── {test_repository}
    ├── {test_service}
    └── integration/
        └── {test_flow}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
