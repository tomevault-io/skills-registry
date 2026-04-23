---
name: agent-coordination
description: Concise overview of agent types, their responsibilities, and delegation flow. Reference for understanding the architecture pipeline. Use when this capability is needed.
metadata:
  author: wtah
---

# Agent Coordination

## Agent Types

| Agent | Scope | Owns | Delegates To |
|-------|-------|------|--------------|
| **high-level-architect** | System Context + Container boundaries | `.arch-registry/`, `.specs/context.md`, `.specs/containers.md` | container-architect, deployment-architect, dynamic-flow-architect |
| **container-architect** | Components within a container | `.arch-registry/components/{container}/`, `{container}/.specs/` | component-architect, integration-developer |
| **component-architect** | Classes/modules within a component | `{container}/{component}/.specs/` | coding-agent |
| **deployment-architect** | Infrastructure + CI/CD specs | `.specs/deployment/` | — (specs only, no coding delegation) |
| **dynamic-flow-architect** | Cross-container runtime flows | `.specs/flows/` | — |
| **planner-agent** | Gap analysis (specs vs code) | `.implementation-plan.md` | coding-agent, integration-developer |
| **coding-agent** | Code implementation (leaf) | `{component}/` (structure per tech stack) | — |
| **integration-developer** | Container integration (leaf) | `{container}/` (entrypoints, scripts, tests) | — |
| **integration-tester** | Local validation (leaf) | `{container}/.venv/`, `{container}/.gitignore` | — |
| **devops-coding-agent** | Container CI/CD scripts (leaf) | `{container}/.ci/`, `{container}/Dockerfile` | — |
| **devops-integration-agent** | Monorepo CI/CD pipelines (leaf) | `.github/workflows/`, `infrastructure/` | — |
| **e2e-test-planner** | E2E test case planning | `.e2e-tests/test-plan.md` | e2e-test-developer |
| **e2e-test-developer** | Individual E2E test implementation (leaf) | `e2e-tests/tests/` | — |
| **e2e-item-tester** | Single item E2E validation (leaf) | `tests/{category}/` | — |
| **fix-agent** | Syntax-level bug fixing (leaf) | Fixes in `{container}/{component}/` | — |
| **test-planner-agent** | Test planning (container-bounded) | `{container}/{component}/.test-plan.md`, `{container}/.test-plan.md` | test-implementer-agent |
| **test-implementer-agent** | Test implementation (leaf) | `{container}/{component}/tests/`, `{container}/tests/` | — |
| **test-fixer-agent** | Test fixing (leaf) | Fixes in test files only | — |
| **interface-evaluator-agent** | Interface compliance evaluation | `{target}/.INTERFACE-ALIGNMENT.md` | interface-alignment-agent |
| **interface-alignment-agent** | Interface deviation fixing (leaf) | Fixes in code or specs | — |

## Hierarchy

```
high-level-architect
    ├── container-architect
    │       ├── component-architect
    │       │       └── coding-agent
    │       └── integration-developer (after coding-agents complete)
    │               └── integration-tester (validates, reports back)
    ├── deployment-architect (specs only)
    └── dynamic-flow-architect

planner-agent → coding-agent / integration-developer

test-planner-agent
    └── test-implementer-agent (max 4 parallel)
            └── test-fixer-agent (max 4 parallel, on failures)

interface-evaluator-agent (max 4 parallel)
    └── interface-alignment-agent (max 4 parallel, on deviations)

e2e-test-planner
    └── e2e-test-developer (max 4 parallel, has Playwright MCP)

/prepare-infrastructure command:
    devops-coding-agent (per container, parallel)
        └── devops-integration-agent (after all containers, single)
```

## Pipeline Flow

```
/architect  →  Creates all specifications
     ↓
/plan       →  Creates .implementation-plan.md files
     ↓
/develop    →  coding-agent (components) + integration-developer (containers)
     ↓
/check-interface-compliance →  interface-evaluator-agent + interface-alignment-agent
     ↓
/integrate  →  integration-tester with retry loop
     ↓
/plan-test-setup     →  test-planner-agent (creates .test-plan.md files)
     ↓
/implement-test-setup →  test-implementer-agent (implements tests)
     ↓
/fix-test-setup       →  test-fixer-agent (fixes failing tests, container-bounded)
     ↓
/prepare-infrastructure  →  devops-coding-agent (per container)
     ↓                      then devops-integration-agent (monorepo)
/e2e-test   →  e2e-test-planner (creates test plan)
     ↓          then e2e-test-developer (parallel, max 6)
/test-report →  Runs all tests, generates structured report
     ↓
/fix        →  fix-agent (parallel, max 6) for syntax-level issues
     ↓
/plan       →  Verify completion
```

## Integration Test-Fix Loop

```
integration-developer → integration-tester
         ↑                     │
         │    (if FAIL)        │
         └─────────────────────┘
         (max 5 iterations)
```

The integration-tester validates builds, local tests (excluding external services/APIs), and startup. On failure, it reports errors back to integration-developer for fixes. This loop repeats until PASS or max 5 iterations.

## Infrastructure Preparation Flow

```
/prepare-infrastructure:

Phase 1: Container CI/CD (ALL IN PARALLEL)
    ├── container-a → devops-coding-agent (creates .ci/, Dockerfile)
    ├── container-b → devops-coding-agent (creates .ci/, Dockerfile)
    └── container-c → devops-coding-agent (creates .ci/, Dockerfile)
    │   Each validates locally (NO deployment, NO cloud costs)
    ▼
Phase 2: Monorepo Integration (SINGLE AGENT)
    └── devops-integration-agent
        │   Reads all {container}/.ci/ directories
        │   Creates .github/workflows/ (or equivalent)
        │   Validates locally (NO deployment, NO cloud costs)
```

**Critical Rule**: Infrastructure agents validate ONLY. No deployments. No cloud costs.

## E2E Testing Flow

```
/e2e-test:

Phase 1: Discovery
    │   Load e2e-testing skill
    │   Read .product/USER_JOURNEYS.md
    │   Read .product/REQUIREMENTS.md
    │   Read .arch-registry/README.md
    ▼
Phase 2: Test Planning (SINGLE AGENT)
    └── e2e-test-planner
        │   Analyzes user journeys and requirements
        │   Creates .e2e-tests/test-plan.md
        │   Defines test cases (E2E-001, E2E-002, ...)
    ▼
Phase 3: User Review
    │   Display test plan summary
    │   Get confirmation to proceed
    ▼
Phase 4: Test Development (BATCHED, MAX 4 PARALLEL)
    │
    │   Batch 1:
    │   ├── e2e-test-developer (E2E-001) ─┐
    │   ├── e2e-test-developer (E2E-002) ─┤ All have Playwright MCP
    │   ├── e2e-test-developer (E2E-003) ─┤ access for browser automation
    │   └── e2e-test-developer (E2E-004) ─┘
    │   Wait for batch completion
    │
    │   Batch 2: (if more tests)
    │   ├── e2e-test-developer (E2E-005)
    │   └── ...
```

**Key Feature**: E2E test developers have access to the Playwright MCP server, allowing them to autonomously interact with the browser to explore the UI and implement tests.

## Fix-E2E Command Flow

```
/fix-e2e:

Phase 1: Discovery (Main Logic)
    │   Read .product/REQUIREMENTS.md → Extract FR-xxx items
    │   Read .product/AI_CAPABILITIES.md → Extract AI-xxx items
    │   Read .product/USER_JOURNEYS.md → Extract UJ-xxx items
    │   Create consolidated test list with priorities
    ▼
Phase 2: Application Startup (Main Logic)
    │   Run ./scripts/local-status.sh
    │   If not running: ./scripts/local-start-all.sh
    │   Verify all containers healthy
    ▼
Phase 3: Individual Item Testing (SEQUENTIAL, ONE AGENT PER ITEM)
    │
    │   For EACH item in test list (SEQUENTIALLY):
    │   ┌─────────────────────────────────────────────────────┐
    │   │  1. Spawn e2e-item-tester agent via Task tool       │
    │   │     - Wait for completion (no background)           │
    │   │     - Agent tests item, persists test if PASS       │
    │   │                                                     │
    │   │  2. Analyze agent output                            │
    │   │     - PASS → record success, move to next item      │
    │   │     - FAIL → extract errors, continue to step 3     │
    │   │                                                     │
    │   │  3. Map errors to containers (main logic)           │
    │   │                                                     │
    │   │  4. Spawn fix-agent(s) per container (parallel)     │
    │   │                                                     │
    │   │  5. Spawn NEW e2e-item-tester agent (re-test)       │
    │   │     - Max 10 iterations per item                    │
    │   │                                                     │
    │   │  6. Move to next item only after current completes  │
    │   └─────────────────────────────────────────────────────┘
    ▼
Phase 4: Final Report (Main Logic)
    │   Generate E2E-VALIDATION-REPORT.md
    │   Summary of all items tested
    │   Issues fixed and remaining
    │   Tests persisted for CI/CD
```

**Key Feature**: Each item is tested by a dedicated e2e-item-tester agent. The main logic NEVER performs testing directly.

## Fix Command Flow

```
/fix:

Phase 1: Test Discovery & Execution
    │   Run unit, integration, and E2E tests (like /test-report)
    │   Collect all failures with locations
    ▼
Phase 2: Error Analysis
    │   Group errors by container/component
    │   Classify: syntax errors (fixable) vs logic errors (escalate)
    ▼
Phase 3: Parallel Fix (BATCHED, MAX 6 PARALLEL)
    │
    │   Batch 1:
    │   ├── fix-agent ({container-a}/{component-1})
    │   ├── fix-agent ({container-a}/{component-2})
    │   ├── fix-agent ({container-b}/{component-1})
    │   └── ... (up to 6)
    │   Wait for batch completion
    │
    │   Batch 2: (if needed)
    │   └── ...
    ▼
Phase 4: Verification
    │   Re-run failed tests
    │   Check if fixes resolved issues
    ▼
Phase 5: Iterate (max 3 iterations)
    │   If failures remain, repeat Phase 3-4
    │   Stop if logic errors or persistent failures
    ▼
Phase 6: Summary Report
```

**Key Features**:
- fix-agent only makes SYNTAX-level changes (imports, dependencies, typos)
- fix-agent does NOT change business logic or deviate from specifications
- fix-agent reads `.arch-registry` and `.specs` to understand boundaries
- Logic errors are reported, not fixed (require coding-agent review)

## Interface Compliance Flow

```
/check-interface-compliance:

Phase 1: Discovery
    │   Find all components with src/ + .specs/
    │   Find all containers with src/ + .specs/interfaces/
    │   Build target list
    ▼
Phase 2: Interface Evaluation (BATCHED, MAX 4 PARALLEL)
    │   Component Batch:
    │   ├── {container-a}/{component-1} → interface-evaluator-agent
    │   ├── {container-a}/{component-2} → interface-evaluator-agent
    │   └── ... (up to 4 total)
    │   Wait for batch completion
    │   ▼
    │   Container Batch:
    │   ├── {container-a} → interface-evaluator-agent
    │   ├── {container-b} → interface-evaluator-agent
    │   └── ...
    │   Wait for batch completion
    │   ▼
    │   Each creates {target}/.INTERFACE-ALIGNMENT.md
    ▼
Phase 3: Collect Deviation Reports
    │   Read all .INTERFACE-ALIGNMENT.md files
    │   Filter to those with DEVIATIONS_FOUND
    │   Build alignment target list
    ▼
Phase 4: Interface Alignment (BATCHED, MAX 4 PARALLEL)
    │   For each target with deviations:
    │   └── interface-alignment-agent
    │       │   90% → Fix implementation to match spec
    │       │   10% → Update spec (with strong justification)
    │       │   Updates .INTERFACE-ALIGNMENT.md with resolution
    │   Wait for batch completion
    ▼
Phase 5: Generate Final Report
    │   Aggregate all alignment results
    │   Generate INTERFACE-REPORT.md
    │   Determine STATUS: [CRITICAL_ERROR | OPEN_ISSUES | PASS]
```

**Key Features**:
- Checks three interface levels: cross-container, container-internal, component
- interface-evaluator-agent only reports deviations, never fixes
- interface-alignment-agent prefers fixing implementation (90% of cases)
- Spec updates require strong business justification (10% of cases)
- Final report includes STATUS for pipeline continuation decision
- CRITICAL_ERROR → Cannot proceed to /integrate
- OPEN_ISSUES → Can proceed with warnings
- PASS → All interfaces aligned

**Interface Hierarchy**:
```
Cross-Container Interfaces (most critical)
└── .arch-registry/interfaces/{container}/*.md

Container-Internal Interfaces
└── {container}/.specs/interfaces/*.md

Component Interfaces
└── {container}/{component}/.specs/classes/*.md
```

## Test Setup Pipeline Flow (Container-Bounded)

```
/plan-test-setup:

Phase 1: Discovery
    │   Find test specifications (.specs/tests/)
    │   Find existing tests (tests/)
    │   Build target list
    ▼
Phase 2: Component Test Planning (BATCHED, MAX 4 PARALLEL)
    │   For each component:
    │   └── test-planner-agent
    │       │   Reads .specs/tests/unit-tests.md
    │       │   Reads .specs/tests/integration-tests.md
    │       │   Analyzes existing tests
    │       │   Creates {component}/.test-plan.md
    ▼
Phase 3: Container Test Planning (BATCHED, MAX 4 PARALLEL)
    │   For each container:
    │   └── test-planner-agent
    │       │   Reads .specs/integration.md
    │       │   Creates {container}/.test-plan.md
```

```
/implement-test-setup:

Phase 1: Discovery
    │   Find all .test-plan.md files
    │   Filter to those with unchecked items
    ▼
Phase 2: Component Test Implementation (BATCHED, MAX 4 PARALLEL)
    │   For each component with .test-plan.md:
    │   └── test-implementer-agent
    │       │   Creates fixtures and mocks
    │       │   Implements unit tests
    │       │   Implements integration tests
    │       │   Updates .test-plan.md
    ▼
Phase 3: Container Test Implementation (BATCHED, MAX 4 PARALLEL)
    │   For each container with .test-plan.md:
    │   └── test-implementer-agent
    │       │   Creates test infrastructure
    │       │   Implements container tests
    │       │   Updates .test-plan.md
    ▼
Phase 4: Verification
    │   Run all implemented tests
    │   Report pass/fail status
```

```
/fix-test-setup:

Phase 1: Run Tests
    │   Execute tests per container
    │   Collect all failures
    ▼
Phase 2: Analyze Failures
    │   Classify: syntax-level vs logic-level vs out-of-scope
    │   Filter to fixable (syntax-level only)
    ▼
Phase 3: Fix (BATCHED, MAX 4 PARALLEL)
    │   For each location with errors:
    │   └── test-fixer-agent
    │       │   Fixes import errors
    │       │   Fixes mock/fixture issues
    │       │   Fixes syntax errors
    │       │   Does NOT change expected values
    ▼
Phase 4: Verify
    │   Re-run previously failing tests
    ▼
Phase 5: Iterate (max 3 iterations)
    │   If fixable failures remain, repeat Phase 3-4
    ▼
Phase 6: Report
    │   Fixed issues
    │   Logic errors (need manual review)
    │   Out of scope (need /e2e-test)
```

**Key Features**:
- All tests are scoped to container boundaries
- External dependencies are mocked, never real
- test-fixer-agent only fixes syntax-level issues
- Logic errors are reported, not fixed
- Cross-container tests are out of scope (use /e2e-test)

## Core Rules

1. Each agent designs at their level and delegates details downstream
2. Never implement what belongs to a downstream agent
3. `/develop` handles code only (no infrastructure)
4. `/prepare-infrastructure` handles CI/CD only (no deployment)
5. Infrastructure agents validate locally, never deploy
6. E2E test developers work in parallel (max 4) with browser automation access
7. `/fix` handles syntax-level issues only (no logic changes)
8. Fix agents must verify changes against `.arch-registry` and `.specs`
9. `/plan-test-setup`, `/implement-test-setup`, `/fix-test-setup` operate within container boundaries only
10. Test agents mock all external dependencies (databases, APIs, other containers)
11. `/check-interface-compliance` runs after `/develop` and before `/integrate` to catch interface mismatches
12. interface-alignment-agent prefers fixing implementation (90%) over updating specs (10%)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wtah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
