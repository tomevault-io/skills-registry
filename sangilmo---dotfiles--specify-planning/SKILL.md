---
name: specify-planning
description: Specification-driven development toolkit integrating GitHub's Spec-Kit. Use when starting new projects, planning features, establishing development standards, or creating technical roadmaps. Trigger phrases include "create a spec", "plan this feature", "write a constitution", "specify requirements", "break down into tasks", "validate specification", or "start spec-driven development". Use when this capability is needed.
metadata:
  author: sangilmo
---

# Specify-Planning Skill

## Introduction

Specification-driven development (spec-driven development) is a methodology that prioritizes clear, detailed specifications before writing code. Unlike traditional development approaches where requirements are often implicit or discovered during implementation, spec-driven development makes requirements explicit, measurable, and verifiable upfront.

This approach transforms how we work with AI coding assistants. Instead of iterative "try and see" conversations, we establish a shared understanding through specifications. This creates a single source of truth that guides implementation, ensures consistency, and enables better collaboration between humans and AI.

The traditional development flow often looks like: idea → code → debug → refactor → maybe document. Spec-driven development inverts this: constitution → specification → plan → tasks → implement. Each stage builds on the previous one, creating progressive refinement from high-level principles to executable tasks.

Benefits of spec-driven development with AI:
- **Reduced ambiguity**: Explicit requirements prevent misunderstandings and rework
- **Better AI outputs**: Clear specifications lead to more accurate implementations
- **Easier validation**: Testable acceptance criteria make verification straightforward
- **Faster iteration**: Changes to specs are cheaper than changes to code
- **Living documentation**: Specifications remain current as the single source of truth
- **Team alignment**: All stakeholders work from the same understanding

## Core Workflow

The specify-planning skill implements a five-stage progressive refinement process:

### 1. Constitution → Establish governance
Define project-wide principles, standards, and constraints. The constitution sets architectural patterns, testing requirements, code quality standards, and technology choices. It answers: "What are the non-negotiable rules for this project?"

### 2. Specify → Document requirements
Capture what needs to be built and why, without prescribing how. Focus on user journeys, acceptance criteria, and success metrics. This stage answers: "What problem are we solving and how will we know we succeeded?"

### 3. Plan → Design technical solution
Translate requirements into technical decisions. Choose architectures, data models, API contracts, and implementation strategies while adhering to constitutional principles. This answers: "How will we technically implement these requirements?"

### 4. Tasks → Break down into chunks
Decompose the plan into reviewable, implementable units of work. Identify dependencies and parallelization opportunities. This answers: "What specific work needs to be done and in what order?"

### 5. Implement → Execute with validation
Build according to the plan, validate against specifications, and commit progress. Each step is guided by the previous stages' decisions.

### Progressive Refinement
Each stage adds specificity without changing prior decisions. If you discover that requirements are impossible, you revise the specification—not silently adjust during implementation. This maintains specification integrity and creates an audit trail.

### Integration with Claude Code
This workflow integrates with Claude Code's orchestration modes:
- **Sequential mode**: Single specification or plan creation
- **Internal Parallel mode**: Research for planning (technology evaluation, pattern analysis)
- **Internal Swarms mode**: Multi-perspective validation (security + performance + UX reviews)

The skill also integrates with auto-commit (specifications trigger git commits after validation) and git-push-protection (feature branch workflows).

## Subcommand Reference

### `/specify-init` - Initialize specification environment

**Purpose**: Set up the `.spec/` directory structure and configuration for specification-driven development in your project.

**Usage**:
```
/specify-init [feature-name]
```

**What it does**:
1. Creates `.spec/` directory with subdirectories: `features/`, `templates/`, `validation/`
2. Detects current git branch or prompts for feature name
3. Generates initial `.specrc` configuration file
4. Creates tracking files for specification status
5. Optionally prompts to create project constitution

**Example**:
```
User: "/specify-init user-authentication"

Claude:
  - Creates .spec/ structure
  - Detects branch: feature/001-user-auth
  - Creates .specrc with defaults
  - Prompts: "Create project constitution now? (recommended for new projects)"
```

**Configuration**: The `.specrc` file controls auto-commit, validation levels, git integration, and task generation preferences.

---

### `/specify-constitution` - Define project governance

**Purpose**: Create or update the project constitution that establishes coding standards, architectural principles, testing requirements, and technology constraints.

**Usage**:
```
/specify-constitution [--update]
```

**What it does**:
1. Checks if `.spec/constitution.md` exists
2. If new: Uses template from `templates/constitution.template.md`
3. If update: Loads existing and prompts for changes
4. Guides through defining:
   - Code quality standards (testing, reviews, documentation)
   - Architecture principles (patterns, separation of concerns)
   - Security and compliance requirements
   - Tech stack constraints (approved/prohibited technologies)
   - Quality gates (pre-commit, pre-PR, pre-release checklists)
5. Validates completeness
6. Saves to `.spec/constitution.md`
7. Triggers auto-commit workflow

**Example**:
```
User: "/specify-constitution"

Claude:
  "Creating project constitution. Let's define your project's core principles.

  1. Testing Requirements:
     - Minimum test coverage? (suggest: 80%)
     - Testing approach? (suggest: TDD for critical paths)

  2. Architecture Principles:
     - Design patterns? (suggest: repository pattern, dependency injection)
     - Performance targets? (suggest: API response < 200ms p95)

  [... continues with all sections ...]

  Constitution complete. Validate and commit?"
```

**Note**: The constitution is referenced by all subsequent specifications to ensure consistency.

---

### `/specify-requirements` - Document requirements and user stories

**Purpose**: Create a feature specification that captures what needs to be built and why, without prescribing implementation details.

**Usage**:
```
/specify-requirements <feature-name>
```

**What it does**:
1. Creates feature directory: `.spec/features/{number}-{name}/`
2. Creates git branch if configured: `feature/{number}-{name}`
3. Uses template from `templates/specification.template.md`
4. Guides through documenting:
   - Overview: what, why, success criteria
   - User stories with acceptance criteria
   - Functional requirements (prioritized: P0/P1/P2)
   - Non-functional requirements (performance, security, scalability)
   - Dependencies and constraints
   - Explicit out-of-scope items
   - Open questions flagged with `[NEEDS CLARIFICATION]`
5. Validates against constitution
6. Saves to `.spec/features/{number}-{name}/specification.md`
7. Triggers auto-commit workflow

**Example**:
```
User: "/specify-requirements payment-integration"

Claude:
  "Creating specification for payment-integration.
  Feature number: 002

  Let's define what we're building:

  Q: What payment methods should be supported?
  User: Credit cards and PayPal

  Q: What's the primary user goal?
  User: Complete purchase securely with saved payment info

  [... continues gathering requirements ...]

  Specification draft ready. Review:

  ## Overview
  **What**: Payment processing integration with Stripe and PayPal
  **Why**: Enable customers to complete purchases securely
  **Success Criteria**:
  - 95% of payments complete within 3 seconds
  - Zero payment data stored locally
  - Support for saved payment methods

  [... full specification ...]

  Validate and commit?"
```

**Validation**: Checks for missing sections, implementation details (which shouldn't be in specs), and constitutional compliance.

---

### `/specify-plan` - Generate technical implementation plan

**Purpose**: Transform requirements into a technical implementation plan with architecture decisions, technology choices, and testing strategies.

**Usage**:
```
/specify-plan <feature-name>
```

**What it does**:
1. Reads `.spec/features/{number}-{name}/specification.md`
2. Reads `.spec/constitution.md` for constraints
3. Uses Internal Parallel mode to research technology options if needed
4. Uses template from `templates/plan.template.md`
5. Develops:
   - Constitutional compliance checklist
   - Architecture overview with diagrams
   - Technology choices with rationales
   - Data model and schema design
   - API contracts (request/response schemas)
   - Security design (authentication, authorization flows)
   - Testing strategy (unit, integration, e2e)
   - Monitoring and observability approach
   - Risk assessment and mitigation
6. Validates technical feasibility
7. Saves to `.spec/features/{number}-{name}/plan.md`
8. Triggers auto-commit workflow

**Example**:
```
User: "/specify-plan payment-integration"

Claude:
  "Creating technical plan for payment-integration.
  Reading specification... ✓
  Reading constitution... ✓

  [Internal Parallel mode: researching payment SDKs]

  Plan draft ready:

  ## Constitutional Compliance
  ✓ Using approved pattern: repository + service layer
  ✓ Testing: 80% coverage required, TDD approach
  ✓ Security: PCI DSS Level 1 compliant (no card data storage)

  ## Technology Choices
  | Component | Technology | Rationale |
  |-----------|------------|-----------|
  | Payment SDK | Stripe SDK v14 | Best TypeScript support, webhook reliability |
  | State Management | Redux Toolkit | Approved by constitution, handles async well |

  ## API Contracts
  POST /api/v1/payments/intent
  Request: { amount: number, currency: string, customerId: string }
  Response: { clientSecret: string, intentId: string }

  [... full plan ...]

  Validate and commit?"
```

**Integration**: Can trigger Internal Swarms mode for multi-perspective validation (security + performance + architecture reviews).

---

### `/specify-tasks` - Break down into executable tasks

**Purpose**: Decompose the technical plan into reviewable, implementable chunks with clear definitions of done.

**Usage**:
```
/specify-tasks <feature-name> [--auto-create]
```

**What it does**:
1. Reads `.spec/features/{number}-{name}/plan.md`
2. Uses template from `templates/tasks.template.md`
3. Generates:
   - Phased task breakdown (foundation, implementation, integration, testing)
   - Task metadata: type, estimate, dependencies, definition of done
   - Dependency graph visualization
   - Parallelizable task identification
4. Validates task completeness
5. Saves to `.spec/features/{number}-{name}/tasks.md`
6. If `--auto-create` flag: Creates Claude Code Tasks using TaskCreate tool
7. Triggers auto-commit workflow

**Example**:
```
User: "/specify-tasks payment-integration --auto-create"

Claude:
  "Creating task breakdown for payment-integration.
  Reading plan... ✓

  Task breakdown generated:

  ## Summary
  - Total Tasks: 12
  - Estimated Duration: 2-3 weeks
  - Parallelizable Tasks: 5

  ## Phase 1: Foundation (3 tasks)

  ### Task 1.1: Set up Stripe SDK integration
  - Type: Setup
  - Estimate: 4 hours
  - Dependencies: None
  - Definition of Done:
    ✓ Stripe SDK installed and configured
    ✓ API keys stored in secure vault
    ✓ Connection test passing

  ### Task 1.2: Create payment repository layer
  - Type: Implementation
  - Estimate: 6 hours
  - Dependencies: Task 1.1
  - Definition of Done:
    ✓ Repository interface defined
    ✓ Stripe adapter implemented
    ✓ Unit tests passing (>80% coverage)

  [... continues with all tasks ...]

  ## Dependency Graph
  1.1 → 1.2 → 2.1 → 2.3
         ↓     ↓     ↓
        1.3 → 2.2 → 3.1

  Parallelizable: [1.1, 1.3], [2.1, 2.2], [3.2, 3.3]

  Creating Claude Code Tasks... ✓
  [TaskCreate: "Set up Stripe SDK integration"]
  [TaskCreate: "Create payment repository layer"]
  [... 10 more tasks ...]

  Tasks ready. View with /tasks or start implementation?"
```

**Integration with Claude Code Tasks**: Each task becomes a Claude Code task with proper dependencies, enabling orchestrated parallel execution.

---

### `/specify-validate` - Validate specification quality

**Purpose**: Run comprehensive validation checks on specifications, plans, or tasks to ensure completeness and quality.

**Usage**:
```
/specify-validate <feature-name> [--swarm]
```

**What it does**:
1. Detects which specification artifacts exist for the feature
2. Runs appropriate validation scripts:
   - `scripts/validate-constitution.sh` - checks constitutional completeness
   - `scripts/validate-spec.sh` - validates specification sections and format
   - `scripts/validate-plan.sh` - checks technical plan completeness
3. If `--swarm` flag: Launches Internal Swarms mode for multi-perspective expert review
4. Reports:
   - Missing required sections
   - Implementation details in specifications (anti-pattern)
   - Unresolved `[NEEDS CLARIFICATION]` items
   - Constitutional compliance issues
   - Consistency across artifacts
5. Provides actionable recommendations

**Example**:
```
User: "/specify-validate payment-integration --swarm"

Claude:
  "Validating payment-integration specification.

  Running validation scripts...
  ✓ Constitution check passed
  ✓ Specification structure valid
  ⚠ Plan has 2 minor issues

  Launching swarm validation (security, performance, UX)...
  [Internal Swarms mode: 3 expert agents]

  📋 Validation Report

  🟢 Specification (95/100)
  ✓ All required sections present
  ✓ No implementation details leaked
  ⚠ 1 unresolved question: "[NEEDS CLARIFICATION] Payment retry logic?"

  🟢 Technical Plan (92/100)
  ✓ Constitutional compliance verified
  ✓ Technology choices justified
  ⚠ Missing: database index strategy for payment queries

  🔴 Security Review (3 findings)
  - [Important] No rate limiting specified for payment endpoints
  - [Minor] PCI DSS attestation process not documented

  🟡 Performance Review (2 findings)
  - [Important] Missing caching strategy for payment methods

  🟢 UX Review (no issues)

  Recommended actions:
  1. Clarify payment retry logic in specification
  2. Add rate limiting to technical plan
  3. Document index strategy for payment queries

  Address these issues before implementation?"
```

**Swarm Mode**: Leverages Internal Swarms to get expert perspectives on security, performance, UX, and architecture simultaneously.

---

## Integration Points

### Git and GitHub Integration

**Branch-based specifications**: Each feature specification can automatically create a corresponding git branch following the pattern `feature/{number}-{name}`. This keeps specification changes isolated and reviewable.

**Conventional commits**: All specification commits follow Conventional Commits format:
- `spec: add [feature] requirements specification`
- `plan: add technical implementation plan for [feature]`
- `tasks: break down [feature] into executable tasks`

**Pull request workflow**: When specifications are ready for review, they can be committed to feature branches and PR'd for team approval before implementation begins.

### Claude Code Task Tool Integration

**Automatic task creation**: The `/specify-tasks` command can automatically create Claude Code tasks using the TaskCreate tool, enabling:
- Task orchestration with proper dependencies
- Parallel execution of independent tasks
- Progress tracking with TaskList and TaskUpdate
- Seamless transition from specification to implementation

**Task metadata**: Each generated task includes:
- Subject: Brief imperative description
- Description: Full implementation details from the task breakdown
- ActiveForm: Present continuous form for progress display
- Dependencies: Set using TaskUpdate after creation

### Auto-Commit Integration

**Validation-triggered commits**: After validation succeeds, the auto-commit workflow triggers:
1. Specification validated successfully
2. AskUserQuestion: "Specification complete and validated. Commit to git?"
3. If approved: Generate commit message following Conventional Commits
4. Execute commit with Co-Authored-By Claude

**Commit message format**:
```
spec: add payment integration requirements

- Define user journeys for payment flow
- Specify P0/P1/P2 requirements
- Document success criteria

Tests: Validation checklist passed

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>
```

### Swarm Coordination Integration

**Multi-perspective validation**: Use Internal Swarms mode for comprehensive specification review:
1. User requests: `/specify-validate payment-integration --swarm`
2. Leader creates validation tasks: security, performance, UX, architecture
3. Specialized agents review in parallel
4. Results aggregated into unified report
5. Prioritized findings presented to user

**Validation agents**:
- **security-sentinel**: Scans for security gaps, compliance issues, threat model completeness
- **performance-oracle**: Reviews scalability concerns, performance targets, optimization opportunities
- **ux-guardian**: Validates user journeys, accessibility, error handling
- **architecture-strategist**: Checks design patterns, constitutional compliance, maintainability

## File Organization

### Project-level structure

When using specify-planning in a project:

```
project-root/
├── .spec/
│   ├── constitution.md              # Project governance
│   ├── features/
│   │   ├── 001-feature-name/
│   │   │   ├── specification.md
│   │   │   ├── plan.md
│   │   │   ├── tasks.md
│   │   │   └── validation/
│   │   │       └── validation-report.md
│   │   └── README.md                # Feature index
│   ├── templates/                   # Project-customized templates
│   └── .specrc                      # Configuration
└── [rest of project files]
```

### Skill bundle structure

The specify-planning skill includes:

```
~/.claude/skills/specify-planning/
├── SKILL.md                         # This file
├── LICENSE.txt                      # MIT license
├── README.md                        # Quick start guide
├── references/                      # Detailed methodology docs
├── templates/                       # Default templates
├── examples/                        # Complete examples
└── scripts/                         # Validation scripts
```

### Configuration file (.specrc)

Located at `.spec/.specrc` in your project:

```json
{
  "version": "1.0",
  "autoCommit": true,
  "autoValidate": true,
  "featurePrefix": "features/",
  "numberingScheme": "sequential",
  "constitutionPath": ".spec/constitution.md",
  "defaultTemplates": "~/.claude/skills/specify-planning/templates",
  "validationLevel": "strict",
  "gitIntegration": {
    "branchPattern": "feature/{number}-{name}",
    "commitConvention": "conventional",
    "autoCreateBranch": true
  },
  "taskIntegration": {
    "autoCreateTasks": true,
    "taskPrefix": "SPEC"
  }
}
```

## Quality Gates and Validation

Each stage has validation checkpoints to ensure quality:

### Constitution validation
- ✓ All 6 core principle sections present (code quality, architecture, security, tech stack, quality gates, team practices)
- ✓ Each principle has measurable criteria
- ✓ Quality gates have explicit checklists
- ✓ No ambiguous terms (e.g., "good", "fast", "secure" without quantification)

### Specification validation
- ✓ Overview with what/why/success-criteria present
- ✓ At least one user story with acceptance criteria
- ✓ Requirements prioritized (P0/P1/P2)
- ✓ Non-functional requirements specified
- ✓ Out-of-scope explicitly stated
- ✗ No implementation details (e.g., class names, API endpoints)
- ✓ Open questions flagged with `[NEEDS CLARIFICATION]`

### Plan validation
- ✓ Constitutional compliance checklist completed
- ✓ Technology choices include rationales
- ✓ API contracts defined with schemas
- ✓ Testing strategy specifies coverage targets
- ✓ Risks identified with mitigation strategies
- ✓ All specification requirements addressed

### Task validation
- ✓ Each task has definition of done
- ✓ Dependencies explicitly stated
- ✓ Estimates provided (time or complexity)
- ✓ Parallelizable tasks identified
- ✓ All plan components covered by tasks
- ✓ Tasks are reviewable units (not too large)

## Bundled Resources

This skill includes comprehensive resources beyond this core file:

**Methodology reference** (`references/methodology.md`): Deep dive into spec-driven development philosophy, comparison with traditional approaches, and AI-specific considerations.

**Writing guides** (`references/*-guide.md`): Step-by-step guides for writing effective constitutions, specifications, plans, and task breakdowns.

**Validation checklists** (`references/validation-checklists.md`): Complete checklists for each validation stage, including quality criteria and common pitfalls.

**Templates** (`templates/*.template.md`): Production-ready templates for constitution, specification, plan, and tasks with inline guidance and examples.

**Examples** (`examples/`): Real-world examples including:
- Complete SaaS platform constitution
- User authentication feature (full spec → plan → tasks)
- Payment integration feature (full spec → plan → tasks)
- API service constitution

**Validation scripts** (`scripts/*.sh`): Automated validation scripts that check structural completeness, detect anti-patterns, and generate validation reports.

To explore these resources, use the Read tool:
```
Read: ~/.claude/skills/specify-planning/references/methodology.md
Read: ~/.claude/skills/specify-planning/templates/specification.template.md
Read: ~/.claude/skills/specify-planning/examples/specifications/user-auth-spec.md
```

---

## Getting Started

### For new projects

1. **Initialize**: `/specify-init my-project`
2. **Create constitution**: `/specify-constitution`
3. **First feature**: `/specify-requirements my-feature`
4. **Plan it**: `/specify-plan my-feature`
5. **Break down**: `/specify-tasks my-feature --auto-create`
6. **Implement**: Start with first task from /tasks

### For existing projects

1. **Initialize in existing repo**: `/specify-init`
2. **Create constitution** (captures current standards): `/specify-constitution`
3. **Add feature specs** for planned work: `/specify-requirements new-feature`
4. **Continue normal workflow**: Plan → Tasks → Implement

### Best practices

- **Start with constitution** even for small projects—it clarifies standards
- **Keep specifications implementation-agnostic**—no class names or database tables
- **Validate early and often**—use `/specify-validate` after each stage
- **Use swarm validation** for critical features—catch issues before implementation
- **Commit after each stage**—maintain audit trail of decisions
- **Review specifications like code**—use PRs for team alignment

---

*For detailed methodology, consult `references/methodology.md`. For templates and examples, see respective directories in this skill bundle.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sangilmo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
