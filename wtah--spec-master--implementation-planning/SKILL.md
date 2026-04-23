---
name: implementation-planning
description: Guidelines for analyzing specifications against implementation state and generating structured action item plans. Used by planner-agent to create .implementation-plan.md files for components, containers, and infrastructure. Use when this capability is needed.
metadata:
  author: wtah
---

# Implementation Planning Skill

This skill defines how to analyze specifications against current implementation and generate structured, trackable implementation plans at three levels: component, container, and infrastructure.

---

## Core Principle

**You analyze and plan; you don't implement.**

```
Specifications (.specs/)
        ↓
Implementation (src/, tests/)
        ↓
   [Gap Analysis]
        ↓
.implementation-plan.md
        ↓
   Coding Agent
```

---

## Success Factors

A good implementation plan meets these criteria:

### 1. Completeness

| Criterion | Measure |
|-----------|---------|
| **All specs covered** | Every class, method, test has an action item |
| **All gaps identified** | Nothing missing between spec and implementation |
| **All proposed changes** | Every proposed change is captured |

### 2. Actionability

| Criterion | Measure |
|-----------|---------|
| **Clear items** | Each item describes exactly what to do |
| **Linked to specs** | Every item references its specification |
| **Prioritized** | Every item has P0-P3 priority |
| **Ordered** | Implementation sequence is clear |

### 3. Trackability

| Criterion | Measure |
|-----------|---------|
| **Checkboxes** | Every item has `[ ]` or `[x]` status |
| **Unique IDs** | Every item has identifier for reference |
| **Progress visible** | Summary shows completion percentage |

---

## Input: What You Analyze

### Component Specifications

```
{container}/{component}/.specs/
├── design.md           # Overall design, patterns, implementation order
├── classes/
│   └── {class}.md      # Class specifications with methods
├── tests/
│   ├── unit-tests.md   # Unit test specifications
│   └── integration-tests.md
└── decisions/
    └── ADR-{n}.md      # Decisions affecting implementation
```

### Component Current Implementation

```
{container}/{component}/
├── src/
│   ├── __init__.{ext}
│   ├── types.{ext}
│   └── {class}.{ext}      # Existing implementations
└── tests/
    ├── conftest.{ext}
    ├── test_{class}.{ext}
    └── integration/
```

### Component Registry Context

```
.arch-registry/components/{container}/{component}.md
```

---

### Container Specifications

```
{container}/.specs/
├── components.md             # C4 diagram + component inventory
├── technology.md             # Technology stack for this container
├── integration.md            # Integration requirements (entrypoints, scripts, tests)
├── interfaces/
│   └── {name}.md             # Container Internal API contracts
├── data-models/
│   └── {entity}.md           # Data schemas
└── decisions/
    └── ADR-{n}.md            # Significant decisions
```

### Container Current Implementation

```
{container}/
├── src/                      # Integration code (entrypoints, wiring)
│   ├── main.{ext}            # Main entrypoint
│   ├── config.{ext}          # Configuration loader
│   └── container.{ext}       # DI container/wiring
├── scripts/                  # Launch and setup scripts
│   ├── start.{sh/ps1}
│   ├── dev.{sh/ps1}
│   └── build.{sh/ps1}
├── tests/                    # Integration tests
│   └── integration/
└── .env.example              # Environment template
```

### Container Registry Context

```
.arch-registry/containers/{container}.md
```

---

## Output: Implementation Plan Structure

### File Locations

| Level | Location |
|-------|----------|
| Component | `{container}/{component}/.implementation-plan.md` |
| Container | `{container}/.implementation-plan.md` |
| Infrastructure | `.specs/deployment/.implementation-plan.md` |

### Component Document Structure

```markdown
# Implementation Plan - {Component Name}

**Generated**: {YYYY-MM-DD HH:MM}
**Component**: {container}/{component}
**Status**: {Not Started | In Progress | Complete}

## Summary
{Metrics table}

## Priority Legend
{P0-P3 definitions}

## 1. Types & Interfaces
{Type and interface action items}

## 2. Classes
{Per-class action items with properties, methods, errors}

## 3. Tests
{Unit and integration test action items}

## 4. Proposed Changes
{Action items from ## Proposed Changes sections}

## 5. Infrastructure
{Setup and dependency action items}

## Implementation Order
{Phased implementation sequence}

## Notes
{Blockers, clarifications, technical debt}
```

### Container Document Structure

```markdown
# Implementation Plan - {Container Name} Integration

**Generated**: {YYYY-MM-DD HH:MM}
**Container**: {container}
**Status**: {Not Started | In Progress | Complete}

## Summary
{Metrics table}

## Priority Legend
{P0-P3 definitions}

## 1. Entrypoints
{Main application entry, API/web entry action items}

## 2. Component Wiring
{DI setup, component registration, initialization action items}

## 3. Scripts
{Launch scripts, environment setup action items}

## 4. Configuration
{Config schema, loader, external services action items}

## 5. Integration Tests
{Container-level and external integration test action items}

## 6. Documentation
{README, setup docs action items}

## Implementation Order
{Phased implementation sequence}

## Notes
{Blockers, clarifications, technical debt}
```

---

## Analysis Process

### Component Analysis

#### Step 1: Extract Component Specifications

Read all specification files and extract:

#### From `design.md`:
- Implementation order
- Design patterns used
- Dependencies between classes
- Error handling strategy

#### From `classes/{class}.md`:
- Class name and purpose
- Properties (name, type, access)
- Methods (name, parameters, return type)
- Error cases to handle
- Test cases for this class

#### From `tests/unit-tests.md`:
- Test case ID
- Test description
- Input/Expected output
- Priority

#### From `tests/integration-tests.md`:
- Scenario name
- Setup requirements
- Verification steps

#### From `## Proposed Changes`:
- Feature ID
- New/modified classes
- New/modified methods
- New test requirements

### Step 2: Scan Implementation

Check what currently exists:

#### File Existence:
```python
# Check if files exist
src/__init__.py
src/types.py
src/{class}.py
tests/conftest.py
tests/test_{class}.py
tests/integration/
```

#### Class Analysis:
For each expected class:
- Does the file exist?
- Does the class exist in the file?
- Which methods are implemented?
- Which properties are defined?

#### Method Analysis:
For each expected method:
- Does it exist?
- Does signature match spec?
- Are error cases handled?

#### Test Analysis:
For each expected test:
- Does test file exist?
- Does test function exist?
- Does it test the right scenario?

### Step 3: Generate Gap Analysis

For each specification item, determine status:

| Status | Condition |
|--------|-----------|
| `[x]` Complete | Implementation matches spec |
| `[ ]` Not Started | No implementation exists |
| `[~]` Partial | Implementation exists but incomplete |

### Step 4: Create Action Items

#### Action Item Format:

```markdown
| ID | Item | Spec Reference | Status | Priority |
|----|------|----------------|--------|----------|
| {ID} | {Description} | [{file}]({path}) | [ ] | P{n} |
```

#### ID Conventions:

**Component Level:**

| Prefix | Category | Example |
|--------|----------|---------|
| T-xxx | Types | T-001 |
| I-xxx | Interfaces | I-001 |
| C{n}-P-xxx | Class property | C1-P-001 |
| C{n}-M-xxx | Class method | C1-M-001 |
| C{n}-E-xxx | Class error handling | C1-E-001 |
| UT-xxx | Unit test | UT-001 |
| IT-xxx | Integration test | IT-001 |
| PC-xxx | Proposed change | PC-001 |
| INF-xxx | Infrastructure | INF-001 |
| DEP-xxx | Dependency | DEP-001 |

**Container Level:**

| Prefix | Category | Example |
|--------|----------|---------|
| ENT-xxx | Entrypoint | ENT-001 |
| WIRE-xxx | Component wiring | WIRE-001 |
| INIT-xxx | Initialization | INIT-001 |
| SCR-xxx | Script | SCR-001 |
| ENV-xxx | Environment setup | ENV-001 |
| CFG-xxx | Configuration | CFG-001 |
| CIT-xxx | Container integration test | CIT-001 |
| EIT-xxx | External integration test | EIT-001 |
| DOC-xxx | Documentation | DOC-001 |

#### Priority Assignment:

| Priority | Criteria |
|----------|----------|
| **P0** | Blocking others, must be first |
| **P1** | Core functionality, high value |
| **P2** | Standard features, normal priority |
| **P3** | Nice to have, can defer |

### Step 5: Determine Implementation Order

Based on dependencies, create phases:

```markdown
## Implementation Order

1. **Phase 1: Foundation** (P0)
   - Types and interfaces (no dependencies)
   - Project structure

2. **Phase 2: Core Classes** (P0-P1)
   - Classes with no internal dependencies
   - Constructor and core methods

3. **Phase 3: Dependent Classes** (P1)
   - Classes that depend on Phase 2
   - Integration between classes

4. **Phase 4: Error Handling** (P1)
   - All error cases
   - Validation logic

5. **Phase 5: Tests** (P1-P2)
   - Unit tests per class
   - Integration tests

6. **Phase 6: Proposed Changes** (varies)
   - New features
   - Modifications
```

---

## Templates

### Summary Table

```markdown
## Summary

| Metric | Count |
|--------|-------|
| Total Action Items | {n} |
| Completed | {n} |
| Remaining | {n} |
| Progress | {%} |

### By Category

| Category | Total | Done | Remaining |
|----------|-------|------|-----------|
| Types | {n} | {n} | {n} |
| Interfaces | {n} | {n} | {n} |
| Classes | {n} | {n} | {n} |
| Tests | {n} | {n} | {n} |
| Proposed Changes | {n} | {n} | {n} |
```

### Class Section

```markdown
### 2.1 {ClassName}

**Spec**: [.specs/classes/{class}.md](.specs/classes/{class}.md)
**Implementation**: [src/{class}.py](src/{class}.py)
**Status**: {Not Started | Partial | Complete}

#### Properties

| ID | Property | Type | Status | Notes |
|----|----------|------|--------|-------|
| C1-P-001 | `_dependency` | `IDependency` | [ ] | Private |

#### Methods

| ID | Method | Status | Test Status | Notes |
|----|--------|--------|-------------|-------|
| C1-M-001 | `__init__(dep)` | [ ] | N/A | Constructor |
| C1-M-002 | `process(input)` | [ ] | [ ] | Core logic |

#### Error Handling

| ID | Error Case | Status | Test Status |
|----|------------|--------|-------------|
| C1-E-001 | ValidationError | [ ] | [ ] |
```

### Test Section

```markdown
## 3. Tests

### 3.1 Unit Tests

**Spec**: [.specs/tests/unit-tests.md](.specs/tests/unit-tests.md)

| ID | Test Case | Class | Status | Priority |
|----|-----------|-------|--------|----------|
| UT-001 | Valid input returns result | Service | [ ] | P0 |
| UT-002 | Invalid input raises error | Service | [ ] | P0 |
```

### Proposed Changes Section

```markdown
## 4. Proposed Changes

### 4.1 {Feature Name} (FR-xxx)

**Source**: [.specs/design.md#proposed-changes](.specs/design.md#proposed-changes)
**Status**: {Not Started | In Progress | Complete}

#### Summary
{Brief description of the feature}

#### Action Items

| ID | Change Item | Status | Priority |
|----|-------------|--------|----------|
| PC-001 | Add `newMethod()` to Service | [ ] | P1 |
| PC-002 | Update `process()` signature | [ ] | P1 |
| PC-003 | Add tests for new method | [ ] | P1 |

#### Dependencies
- Requires: {other items that must be done first}
- Blocks: {items that depend on this}
```

---

### Container Analysis

#### Step 1: Extract Container Specifications

Read container specification files and extract:

**From `integration.md`:**
- Required entrypoints (main, API, CLI)
- Launch scripts needed
- Environment variables required
- Integration test scenarios

**From `components.md`:**
- Components to wire together
- Component dependencies and order
- Interfaces between components

**From `technology.md`:**
- Framework/runtime configuration
- External service connections
- Build and deployment requirements

#### Step 2: Scan Container Implementation

Check what currently exists at container level:

**Entrypoints:**
```
src/main.{ext}      # Main application entry
src/api.{ext}       # API server (if applicable)
src/config.{ext}    # Configuration loader
src/container.{ext} # DI/wiring setup
```

**Scripts:**
```
scripts/start.{sh/ps1}
scripts/dev.{sh/ps1}
scripts/build.{sh/ps1}
```

**Environment:**
```
.env.example
```

**Tests:**
```
tests/integration/
```

#### Step 3: Generate Container Gap Analysis

| Integration Item | Expected | Actual | Gap |
|------------------|----------|--------|-----|
| Main entrypoint | Exists with DI | Missing | Full implementation |
| Config loader | Load from env | Partial | Missing validation |
| Launch script | start.sh | Missing | Create |
| Integration tests | 5 scenarios | 2 tests | 3 missing |

#### Step 4: Create Container Action Items

Use container-specific ID prefixes (ENT, WIRE, INIT, SCR, ENV, CFG, CIT, EIT, DOC).

#### Step 5: Container Implementation Order

```markdown
## Implementation Order

1. **Phase 1: Foundation** (P0)
   - Configuration schema and loader
   - Environment setup
   - DI container setup

2. **Phase 2: Component Wiring** (P0)
   - Register all components
   - Initialize components

3. **Phase 3: Entrypoints** (P0-P1)
   - Main entrypoint with DI
   - API setup (if applicable)
   - Launch scripts

4. **Phase 4: Integration Tests** (P1)
   - Container starts tests
   - End-to-end flow tests

5. **Phase 5: Polish** (P1-P2)
   - Remaining scripts
   - Documentation
   - Health checks
```

---

### Container Templates

#### Container Summary Table

```markdown
## Summary

| Metric | Count |
|--------|-------|
| Total Action Items | {n} |
| Completed | {n} |
| Remaining | {n} |
| Progress | {%} |

### By Category

| Category | Total | Done | Remaining |
|----------|-------|------|-----------|
| Entrypoints | {n} | {n} | {n} |
| Component Wiring | {n} | {n} | {n} |
| Scripts | {n} | {n} | {n} |
| Configuration | {n} | {n} | {n} |
| Integration Tests | {n} | {n} | {n} |
| Documentation | {n} | {n} | {n} |
```

#### Entrypoint Section

```markdown
## 1. Entrypoints

### 1.1 Main Application Entry

**Spec**: [.specs/integration.md](.specs/integration.md)
**Implementation**: [src/main.{ext}](src/main.{ext})

| ID | Item | Status | Priority | Notes |
|----|------|--------|----------|-------|
| ENT-001 | Create main entrypoint | [ ] | P0 | Bootstrap |
| ENT-002 | Initialize DI | [ ] | P0 | Wire components |
```

#### Component Wiring Section

```markdown
## 2. Component Wiring

### 2.1 Dependency Injection Setup

**Spec**: [.specs/components.md](.specs/components.md)

| ID | Item | Status | Priority | Notes |
|----|------|--------|----------|-------|
| WIRE-001 | Create DI container | [ ] | P0 | Central wiring |
| WIRE-002 | Register {Component} | [ ] | P0 | Interface binding |
```

#### Scripts Section

```markdown
## 3. Scripts

### 3.1 Launch Scripts

**Spec**: [.specs/integration.md#scripts](.specs/integration.md#scripts)

| ID | Item | Status | Priority | Notes |
|----|------|--------|----------|-------|
| SCR-001 | Create start script | [ ] | P0 | Main launch |
| ENV-001 | Create .env.example | [ ] | P0 | Environment template |
```

#### Container Integration Tests Section

```markdown
## 5. Integration Tests

### 5.1 Container-Level Tests

**Spec**: [.specs/integration.md#testing](.specs/integration.md#testing)

| ID | Test Scenario | Status | Priority |
|----|---------------|--------|----------|
| CIT-001 | Container starts successfully | [ ] | P0 |
| CIT-002 | Components wire correctly | [ ] | P0 |
| CIT-003 | End-to-end flow | [ ] | P1 |
```

---

## Greenfield vs Brownfield

### Greenfield (No Implementation)

When `src/` doesn't exist:

1. All items are "Not Started" `[ ]`
2. Include all infrastructure setup items
3. Full implementation plan from specs
4. Clear phased approach

### Brownfield (Existing Implementation)

When `src/` has code:

1. Scan existing files for implemented items
2. Mark completed items `[x]`
3. Focus remaining items on gaps
4. Include refactoring items if needed

### With Proposed Changes

When specs have `## Proposed Changes`:

1. Create dedicated section per feature
2. Track separately from base implementation
3. Include all new/modified items
4. Note dependencies on existing items

---

## Workflow: Generating a Plan

### 1. Read All Specifications

```
Read:
├── {component}/.specs/design.md
├── {component}/.specs/classes/*.md
├── {component}/.specs/tests/*.md
├── .arch-registry/components/{container}/{component}.md
└── .constraints/TECHNOLOGY.md
```

### 2. Scan Current Implementation

```
Scan:
├── {component}/src/
│   ├── List all .py files
│   ├── For each file: extract classes, methods
│   └── Check completeness against specs
└── {component}/tests/
    ├── List all test files
    └── For each file: extract test functions
```

### 3. Build Gap Matrix

| Spec Item | Expected | Found | Gap |
|-----------|----------|-------|-----|
| Class X | 5 methods | 3 methods | 2 missing |
| Test Y | 10 cases | 6 cases | 4 missing |

### 4. Generate Action Items

For each gap:
- Create action item with ID
- Add spec reference
- Set status checkbox
- Assign priority

### 5. Organize by Category

Group items:
1. Types & Interfaces
2. Classes (with subsections)
3. Tests
4. Proposed Changes
5. Infrastructure

### 6. Determine Order

Based on dependencies:
- What has no dependencies → Phase 1
- What depends on Phase 1 → Phase 2
- Continue until all items ordered

### 7. Write Plan File

Output to `.implementation-plan.md`

---

## Quality Checklists

### Before Analysis

- [ ] All spec files identified
- [ ] All src files scanned
- [ ] All test files scanned
- [ ] Proposed changes identified
- [ ] Registry entry read

### Plan Completeness

- [ ] Every class from specs has section
- [ ] Every method has action item
- [ ] Every property has action item
- [ ] Every error case has action item
- [ ] Every test case has action item
- [ ] All proposed changes captured

### Plan Quality

- [ ] All items have unique IDs
- [ ] All items have spec references
- [ ] All items have correct status
- [ ] All items have priority
- [ ] Implementation order is logical
- [ ] Summary metrics are accurate

### Plan Usability

- [ ] Links are valid
- [ ] Descriptions are clear
- [ ] Phases are actionable
- [ ] Progress is trackable

---

## Anti-Patterns

| Anti-Pattern | Problem | Solution |
|--------------|---------|----------|
| **Missing specs** | Not all specs analyzed | Systematically read all files |
| **Wrong status** | Incorrect `[x]` / `[ ]` | Actually verify implementation |
| **Vague items** | "Implement class" | Break into methods, properties |
| **No priority** | Everything equal | Use P0-P3 based on dependencies |
| **No order** | Random sequence | Respect dependency graph |
| **Stale plan** | Outdated after changes | Re-run after implementation |
| **Missing links** | No spec references | Always link to source |

---

## Example: Complete Analysis

### Input: Specifications

**design.md**:
- 3 classes: Parser, Repository, Service
- Order: Types → Parser → Repository → Service

**classes/parser.md**:
- 4 methods: `__init__`, `parse`, `validate`, `_extract`
- 1 property: `_config`
- 2 errors: ValidationError, ParseError

**tests/unit-tests.md**:
- 8 test cases for Parser
- 7 test cases for Repository

### Input: Current Implementation

**src/parser.py**:
- Class exists
- 2 of 4 methods implemented
- Property exists

**src/repository.py**:
- File exists but empty

**tests/test_parser.py**:
- 3 of 8 tests implemented

### Output: Gap Analysis

| Category | Total | Done | Gap |
|----------|-------|------|-----|
| Parser Methods | 4 | 2 | 2 |
| Parser Errors | 2 | 0 | 2 |
| Repository | 3 | 0 | 3 |
| Service | 5 | 0 | 5 |
| Unit Tests | 15 | 3 | 12 |

### Output: Action Items (excerpt)

```markdown
### 2.1 Parser

**Status**: Partial (50%)

#### Methods

| ID | Method | Status | Priority |
|----|--------|--------|----------|
| C1-M-001 | `__init__(config)` | [x] | P0 |
| C1-M-002 | `parse(input)` | [x] | P0 |
| C1-M-003 | `validate(data)` | [ ] | P1 |
| C1-M-004 | `_extract(raw)` | [ ] | P1 |

#### Error Handling

| ID | Error Case | Status | Priority |
|----|------------|--------|----------|
| C1-E-001 | ValidationError | [ ] | P1 |
| C1-E-002 | ParseError | [ ] | P1 |
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
