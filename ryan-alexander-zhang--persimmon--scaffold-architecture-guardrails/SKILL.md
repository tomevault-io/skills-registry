---
name: scaffold-architecture-guardrails
description: Use when validating any scaffold change against layer rules, naming conventions, migration constraints, concurrency safety, or commit readiness. Cross-cutting checklist for all generator skills.
metadata:
  author: ryan-alexander-zhang
---

# Architecture Guardrails (Global)

## Overview

The single source of truth for architectural invariants across the Persimmon scaffold. Every generator skill must satisfy these guardrails before code is committed.

**Core principle:** Guardrails are non-negotiable. If a generated artifact violates any rule here, it must be fixed before commit — no exceptions, no "fix later" TODOs.

Templates: See `references/templates.md`.

## When to Use

- Final checklist before committing any scaffold-aligned change
- Validating global invariants that other skills must not contradict
- Reviewing generated code for layer, naming, or concurrency violations

**Don't use when:**
- Routing a task to the correct skill (use `scaffold-router` instead)
- Generating code artifacts (use the specific generator skill)

## Layers & dependencies
- `domain`: pure domain model. No frameworks. **No Lombok**. Minimal public API.
- `app`: orchestration/use-cases, ports, app-common components. May use `spring-tx` annotations (only).
- `infra`: implementations (DB/MyBatis/Kafka/clients). May depend on `app` (allowed in this scaffold).
- `adapter`: delivery mechanisms (web, scheduler, mq consumers). Calls app services/ports only.
- `start`: wiring/configuration, YAML keys, Bean configs, component scanning.

## Naming conventions
- Prefer explicit names:
  - `*Repository`: domain persistence ports (domain-facing, business semantics)
  - `*Store`: app-common persistence ports for technical tables (outbox/inbox/workflow)
  - `*Transport`: message transport/publisher implementations (Kafka, etc.)
  - `*Job`: scheduler entrypoint (adapter)
- Event identifiers:
  - `eventId`: unique per produced event (UUID/ULID).
  - `aggregateId`: domain aggregate identity; not necessarily unique per event.
  - `eventType`: stable string identifier; do not use Java class name as the contract.

## State machines
- Outbox: `READY → SENDING → SENT` and terminal `DEAD` (with attempts + next retry).
- Inbox: `PROCESSING → PROCESSED` and terminal `DEAD`, retry `FAILED` (reclaimable).
- Workflow: keep status transitions atomic and guarded (status predicates).

## Concurrency safety
- Any `markSent/markFailed/markDead` MUST include **lock owner + status/lease predicates** to prevent late updates overwriting newer state.
- Prefer **claim-then-work** flows via `tryStart/tryClaim` or conditional updates.
- If handler is missing, do not leave inbox rows stuck in `PROCESSING`; mark as `DEAD`.
- Terminal integration-event failures (`NO_HANDLER` / non-retryable) must have an explicit, pluggable compensation trigger path.

## Flyway migrations
- No foreign keys.
- Include required unique constraints and check constraints.
- Backfill safely when adding non-null columns.

## DB integration test bootstrap
- DB ITs must use Flyway migration path only:
  - `spring.flyway.enabled=true`
  - `spring.flyway.locations=classpath:db/migration`
  - `spring.flyway.baseline-on-migrate=true`
  - `spring.flyway.baseline-version=0`
  - `spring.sql.init.mode=never`
- Do not use `spring.sql.init.schema-locations` in DB ITs.

## Tests
- Unit tests: fast, no DB.
- Integration tests: DB involved; use `*IT` and Failsafe.

## Integration Test Data Isolation
- DB integration tests must not depend on a globally empty database.
- Each `*IT` must be resilient to DB reuse:
  - cleanup tables it touches in `@BeforeEach`/`@AfterEach`
  - use unique ids/keys for inserted rows
  - assert only on rows created by the test (avoid full-table counts)

## Work-Unit Commit Gate
- At each focused work-unit completion, do not commit until:
  - compile + unit tests pass (`mvn clean test` minimum)
  - `requesting-code-review` is invoked for the pending commit scope
  - Critical/Important review findings are resolved
- A work unit must have one audit anchor before commit:
  - new feature delivery -> a slice record
  - failure remediation / bugfix -> an incident record and, when needed, a follow-up slice record
  - guardrail / skill / process hardening -> a slice record or decision record
  - documentation-only process changes -> a slice record when the change affects workflow rules
- Requirement/scope/rule changes must also have one change request record before downstream docs or code are updated:
  - `docs/changes/CR-<nn>-<slug>.md`
- Prefer one focused commit per work unit to preserve traceability.

## Auditability (Stage Commits)
- Prefer **stage commits** at natural checkpoints to improve traceability:
  - Skill/system changes (skills/templates/scripts) -> one commit
  - Documentation-only changes -> one commit
  - Bugfix-only changes -> one commit
  - Code delivery should follow the work-unit commit gate
- Do not batch unrelated checkpoints into one commit.

## Auditability & Slice Control
- Implementation work must bind to a single `slice-id` before code changes begin.
- One slice must solve one focused problem only; do not mix unrelated fixes or refactors into the same slice.
- Skill-owned templates and validators must live under the owning skill directory (`<active-skill-root>/<skill>/references` or `scripts`), not under `docs/`.
- Slice records are the primary audit record:
  - `docs/implementation/slices/slice-<nn>-<slug>.md`
- Change requests are the primary audit record for approved business changes before implementation starts:
  - `docs/changes/CR-<nn>-<slug>.md`
- Commit messages must stay precise and concise; do not use commit messages as the full execution log.
- Verification evidence must be persisted in the slice before the slice is commit-ready.
- Review status must be persisted in the slice before the slice is commit-ready.
- Compilation, test, verification, or integration failures must create an incident record:
  - `docs/incidents/incident-<yyyymmdd>-<slug>.md`
- Repeated incidents or stable corrective rules must be promoted into a decision record or executable guardrail:
  - `docs/decisions/adr-<nn>-<slug>.md`
- Prefer automating stable rules in AGENTS, skills, tests, or static analysis instead of leaving them as narrative-only notes.
- Do not leave maintenance commits unanchored:
  - bugfix commits must point to an incident or a focused remediation slice
  - guardrail/skill/process commits must point to a slice or decision record
  - requirement-driven implementation commits must point to both a change request and a slice/incident/decision as appropriate

## Checklist
- [ ] Correct module + package (matches `package-info.java`)
- [ ] `{{bcName}}` is explicitly resolved; do not default to `biz`
- [ ] No Lombok in `domain`
- [ ] DB changes have Flyway migration
- [ ] Outbox/Inbox/Workflow updates guarded by predicates
- [ ] Unit tests for logic, IT for store/mappers when needed
- [ ] If this is a focused work unit: passed the required verification, ran `requesting-code-review`, then committed
- [ ] If this is implementation work: bound the task to a `slice-id`
- [ ] If this changes approved business intent/scope/rules: created or updated a `CR` record before downstream edits
- [ ] If failures occurred: incident records were written and linked
- [ ] If recurring rules were discovered: decision/guardrail follow-up was recorded
- [ ] The commit is anchored to a slice, incident, or decision record as appropriate

## Common Mistakes

| Mistake | Why It Happens | Fix |
|---------|---------------|-----|
| Lombok in domain module | Habit from other Spring projects | Domain module is framework-free; write constructors/getters manually |
| Foreign keys in Flyway migrations | Relational DB instincts | This scaffold forbids FKs; use application-level referential integrity |
| Outbox update without status predicate | Quick-fix mentality | Always include lock owner + status + lease predicates in claim/update |
| `@Service`/`@Component` in app layer | Spring convention bleeding | App layer allows only `spring-tx`; stereotypes belong in `start` wiring |
| Mixing unrelated fixes in one commit | Efficiency bias | One focused work unit per commit for traceability |
| Defaulting `bcName` to `biz` | Scaffold placeholder confusion | `biz` is a reference namespace; resolve BC name explicitly |
| Leaving inbox rows stuck in `PROCESSING` | Handler missing or crashing | Mark as `DEAD` with compensation trigger when handler not found |

## Red Flags — STOP and Verify

If you catch yourself thinking any of these, stop and re-check:
- "I'll add the Flyway migration later" → migration must accompany DB changes
- "This FK is just for data integrity" → no foreign keys, period
- "The lock predicate isn't needed for this simple case" → always include predicates
- "I'll batch these unrelated fixes for efficiency" → one commit per work unit
- "The `biz` package already exists, so I'll use it" → `biz` is a placeholder, not a target

## Integration

- **Called by:** every generator skill (as post-generation checklist)
- **Pairs with:** `scaffold-router` (pre-routing), `slice-audit-orchestrator` (audit anchoring)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-alexander-zhang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
