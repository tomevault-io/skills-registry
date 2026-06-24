---
name: app-common-workflow-generator
description: Use when generating or extending the workflow engine in app-common: step handlers, retry policies, processors, compensation flows, and unit tests.
metadata:
  author: ryan-alexander-zhang
---

# App Common Workflow Generator

## Overview
Generates and extends the linear workflow engine (step handlers, retry policies, processors) with idempotent steps, guarded state transitions, and explicit compensation start conditions.
**REQUIRED:** Follow `GENERATOR_SKILL_STRUCTURE.md`. Variables in `VARIABLES.md`.

Templates: See `references/templates.md`.

## When to Use
- `{{basePackage}}.app.common.workflow.*`

### Don't use when
- You need the infra-level workflow store implementation — use `infra-store-implementation-generator`.
- You need an event handler (outbox/inbox) — use `app-common-event-generator`.
- You need a standard use-case handler (Command/Query) — use `app-usecase-generator`.
- You need scheduler job wiring — use `adapter-scheduler-job-generator`.

## Inputs Required
- `workflowType` (stable string)
- Step list (linear): ordered `stepSeq` + `stepType`
- Retry policy requirements (max attempts, backoff)
- WAITING semantics (deadline + wake-up event type) if used

## Outputs
- App/common workflow:
  - step handler(s) implementing `WorkflowStepHandler`
  - optional retry policy implementation + unit tests
  - services that start/tick/signal workflows

## Naming & Packaging
- Handler names: `<WorkflowType><StepType>Handler` or `<StepType>WorkflowStepHandler`
- Keep engine types in `app/common/workflow/**`; business-specific in `app/{{bcName}}/**` if needed.

## Rules
- Linear execution (responsibility chain) unless explicitly requested otherwise.
- Steps must be idempotent (replay safe).
- Retry policy must be configurable and unit-tested.
- State transitions must be guarded (status predicates at store boundary).
- Compensation workflows must not be auto-started by default; start conditions must be explicit and
  observable from upstream command/event contracts.
- If adding/using compensation start APIs:
  - normal `start(...)` path must reject compensation workflow types
  - compensation must use dedicated start API with explicit trigger metadata
    (`triggerSource`, `triggerReason`, `triggerReferenceId`) persisted for observability.
- Keep app-common focused on workflow policy and orchestration; lease/persistence mechanics belong to infra.

## Reference Implementations
- `{{appModuleDir}}/src/main/java/{{basePackagePath}}/app/common/workflow/service/WorkflowRunner.java`
- `{{appModuleDir}}/src/main/java/{{basePackagePath}}/app/common/workflow/service/WorkflowTaskProcessorImpl.java`
- `{{infraModuleDir}}/src/main/java/{{basePackagePath}}/infra/repository/workflow/store/MybatisWorkflowStore.java`
- `{{domainModuleDir}}/src/main/java/{{basePackagePath}}/domain/common/workflow/WorkflowStepStatus.java`

## Tests
- Unit tests for definition/registry/step results/policies.
- Integration tests for store lease + state transitions.
- For each new step handler, add unit tests for at least:
  - success/completed branch
  - retry/dead branch (when externally callable)

## Common Mistakes
| Mistake | Why It Happens | Fix |
|---------|---------------|-----|
| Forgetting to insert steps up front | Assuming steps are created on-the-fly during execution | Insert all steps at workflow creation; recovery depends on pre-existing step rows |
| Updating step status without lock owner predicates | Skipping optimistic concurrency checks | Always include lock owner / lease predicates in store update conditions |
| Auto-starting compensation workflows | Missing explicit trigger condition | Use dedicated compensation start API with `triggerSource`/`triggerReason`/`triggerReferenceId` |
| Non-idempotent step handlers | Not considering replay scenarios | Design every step to be replay-safe; use claim-then-execute pattern |

## Integration
- **Called by:** `scaffold-router`, `dev-workflow-ddd-implementation-workflow`
- **Pairs with:** `app-port-generator` (workflow store port), `infra-store-implementation-generator` (workflow store impl), `adapter-scheduler-job-generator` (periodic workflow tick), `app-common-event-generator` (event-driven workflow wake-up)

## Commit Gate

- pass required tests (`mvn -q clean test` minimum, `mvn -q clean verify` if DB behavior changed)
- run `requesting-code-review`
- resolve Critical/Important findings
- keep the commit focused

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-alexander-zhang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
