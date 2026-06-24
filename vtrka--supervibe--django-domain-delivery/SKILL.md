---
name: django-domain-delivery
description: Use WHEN implementing or reviewing Django and DRF service work that touches ORM/query boundaries, migrations, serializers, viewsets, transactions, async jobs, auth/permissions, observability, tests, deployment, or rollback. TO deliver stack-specific production changes with framework-native practices, verification evidence, and rollback discipline.
metadata:
  author: vTRKA
---

# Django Domain Delivery

## Overview

Django Domain Delivery keeps Django and DRF changes inside clear framework
boundaries: models/managers own persistence shape, services or forms own domain
rules, serializers own API contracts, viewsets own request orchestration, tasks
own async side effects, and migrations own reversible schema evolution. It is
for shipping one safe Django slice with tests, query discipline, auth checks,
observability, and rollback evidence.

## When to Use

Use for Django models, managers, querysets, ModelForms, CBVs, DRF serializers,
viewsets, permissions, migrations, transactions, Celery/async jobs, admin
behavior, API tests, deployment checks, or rollback planning.

## Expert Operating Standard

Follow `<resolved-supervibe-plugin-root>/docs/references/skill-expert-operating-standard.md`: read local source
first, preserve evidence, keep scope narrow, verify before completion claims,
and lower confidence when migrations, query counts, auth paths, or rollback are
not proven.

## Step 0 - Read source of truth

1. Read the user request, `AGENTS.md`, task graph item, and owned write set.
2. Inspect `manage.py`, settings, installed apps, app layout, migrations,
   serializers, viewsets, permissions, factories, and nearest pytest or Django
   test cases.
3. Search for existing transaction, manager, queryset, serializer, permission,
   Celery, logging, and deployment patterns before adding new ones.
4. Use CodeGraph for unfamiliar apps and CodeGraph or symbol search before
   changing model fields, public serializers, URLs, task signatures, or shared
   querysets.

## When not to use

- Do not use for non-Django Python work unless the change enters through a
  Django app, ORM model, DRF API, or Django-managed job.
- Do not choose app boundaries, Celery topology, API versioning policy, or auth
  architecture when a narrower implementation decision is enough; hand off to
  the relevant architect or specialist.
- Do not perform broad migrations or serializer rewrites without a scoped plan.

## Decision tree

```text
Schema changes? -> migration up/down, data compatibility, deploy order, rollback.
API payload changes? -> serializer contract, viewset action, permission, schema/test.
Business rule changes? -> form/service/model method, not hidden in view or serializer save.
Query traverses relations? -> select_related/prefetch_related/query count test.
Multiple writes must be atomic? -> transaction.atomic around one unit of work + rollback test.
Side effect can outlive request? -> Celery/task boundary, idempotency key, retry policy.
Auth-sensitive path? -> permission class, object permission, ownership test, audit log.
```

## Procedure

1. Define the slice: app, entry point, input, output, old behavior preserved,
   verification command, deploy concern, and rollback path.
2. Map boundaries. Keep request orchestration in views/viewsets, API shape in
   serializers, domain validation in forms/services/model methods, persistence
   in managers/querysets, and side effects in tasks or explicit service calls.
3. Shape ORM access. Use existing managers/querysets, add `select_related` or
   `prefetch_related` only for proven traversal, avoid wildcard prefetch, and
   add query-count or regression tests where the project supports them.
4. Design migrations. Add reversible schema migrations, separate data migrations
   from risky deploys when needed, keep nullable/backfill/constraint sequencing
   compatible with rolling deploys, and document rollback for irreversible data
   choices.
5. Apply transaction rules. Use `transaction.atomic()` around one business unit,
   avoid network calls inside transactions, and test rollback on validation,
   permission, and repository failure paths.
6. Implement DRF contracts deliberately. Serializer fields are explicit, writes
   use `validated_data`, viewsets choose the smallest action, permissions cover
   object ownership, and schema impacts are tested or documented.
7. Isolate async/job work. Task arguments are IDs or primitive snapshots, tasks
   are idempotent, retries are bounded, and request code records enqueue
   failure instead of silently losing work.
8. Add observability. Use existing logging/metrics/tracing for request IDs,
   model identifiers, permission denials, task retry/drop decisions, migration
   risk, and external side effects without logging secrets.
9. Write focused tests first when behavior changes. Cover success, validation
   failure, auth/permission denial, object-level access, transaction rollback,
   query budget, serializer contract, migration behavior, and async retry or
   idempotency when relevant.
10. Verify with the active scoped command, such as `pytest <app>/tests/...`,
   `python manage.py test <app>`, `python manage.py check`, migration up/down
   checks, or schema generation checks. If policy defers commands, record the
   exact final gate and non-test evidence.
11. Repair loop: on failure, localize to serializer/viewset/service/query/task
   boundary, fix the smallest cause, rerun the same command, and stop with a
   blocker if the repair requires app-boundary, auth, API-version, or deploy
   strategy approval.

1. Read the source artifact, owned file paths, graph/task scope, and current project convention; record the evidence path, command, receipt, or runtime state that proves the starting point.
2. If required source, owner, dependency, runtime boundary, or approval is missing, stop and return BLOCKED with the missing field, impacted artifact, and next action instead of guessing.
3. After edits or reviewer findings, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, artifact path, confidence, and remaining blocker before completion.

## Worked example

Add a DRF endpoint to approve an invoice:

1. Viewset action checks authentication and object permission, then calls
   `approve_invoice(user, invoice_id)`.
2. Service loads the invoice with `select_related("account")`, verifies account
   ownership, wraps status update and audit row in `transaction.atomic()`, and
   enqueues a task after commit.
3. Serializer exposes explicit response fields and never uses `fields = "__all__"`.
4. Tests cover approve success, wrong account 403, already-approved 409,
   transaction rollback on audit failure, query count, and task idempotency.

## Good and bad delivery paths

Good delivery path: deliver through DRF viewset action, explicit serializer,
permission class, service boundary, queryset/manager access,
transaction.atomic, post-commit task enqueue, and audit row. Runtime-specific
tests include DRF API auth and object-permission tests, serializer errors,
query-count assertions, transaction rollback on audit or enqueue failure,
Celery retry/idempotency, and migration up/down where schema changes. Rollback
removes or flags the route/action and uses compatible migration revert or
unused nullable fields until cleanup. Failure boundaries are wrong account,
already-approved state, serializer validation, query explosion, task enqueue
failure, migration incompatibility, and partial audit write.

Bad unsafe path: hide approval rules in serializer.save or the viewset, use
fields-all serializers, enqueue before commit, and verify only the happy API
path. That path has no runtime-specific tests for the changed stack surface,
no concrete rollback beyond hope or manual cleanup, and weak failure
boundaries for wrong account, already-approved state, serializer validation,
query explosion, task enqueue failure, migration incompatibility, and partial
audit write.

## Common rationalizations

- "The serializer can do the business rule" fails when the same rule is needed
  from admin, task, management command, or service code.
- "This query is small" fails when list endpoints or nested serializers can
  turn one fixture into an N+1 production incident.
- "A data migration is one-way anyway" fails unless rollback, backup, or
  release waiver is explicit.

## Red flags

- `fields = "__all__"` on externally consumed serializers.
- Viewset action mutates multiple models without `transaction.atomic()`.
- Permission tests cover authentication but not object ownership.
- Celery task accepts a full model dict or performs non-idempotent external
  calls without retry/drop policy.
- Migration changes a non-null field on a populated table without deploy order
  or backfill evidence.

## Checklist

- ORM/query boundary and query-budget risk are handled.
- Migrations are reversible or the irreversible decision is documented.
- Serializer/viewset/permission behavior is explicit and tested.
- Transactions wrap one business unit and exclude slow network calls.
- Async jobs are idempotent and observable.
- Deployment and rollback path are named.

## Failure modes

- Nested serializer creates an N+1 list endpoint.
- Permission is checked at list level but not object level.
- Data migration locks a hot table during deploy.
- Task retry duplicates side effects.
- Rollback reverts code but leaves incompatible schema or data.

## Output contract

- `status`: PASS, BLOCKED, PARTIAL, or DEFERRED.
- `slice`: app, view/form/task/model behavior changed.
- `boundaries`: model/manager/service/serializer/viewset/task files touched.
- `queryAndTransactionEvidence`: query plan, count, transaction, and rollback
  proof or gap.
- `authAndContractEvidence`: serializer fields, permissions, schema/API tests.
- `tests`: exact commands, case list, exit code, or final-gate deferral reason.
- `deploymentRollback`: migration order, feature flag, revert, backup, or
  waiver.
- `confidence`: score with any cap from missing migration/auth/query proof.
- `nextAction`: repair, handoff, A026 binding, or final gate.

## Guard rails

- Prefer Django and DRF conventions already present in the project.
- Do not hide business logic in signals unless the project already owns that
  pattern and tests the side effect.
- Do not log secrets, credentials, tokens, or full sensitive payloads.
- Do not claim production readiness without focused verification or a recorded
  final-gate deferral.

## Verification

- Run the scoped Django/pytest command required by the active graph and rerun it
  after repairs.
- For migrations, run or document migration up/down, deploy-order, and rollback
  evidence.
- For DRF endpoints, verify serializer contract, permission denial, object
  ownership, and schema generation when the project owns schema output.
- For ORM-sensitive changes, include query-count evidence or a stated reason it
  is unavailable.

- If any scoped check fails or new evidence appears, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, blockers, and final-gate deferrals before claiming completion.

## Supporting references

Use local support files through progressive disclosure; keep the main SKILL.md as the operating contract and load deeper resources only when the active task needs them:
- Read `references/practice-pack.md` when django-domain-delivery needs deeper practice guidance, source evidence anchors, risk checks, or a final checklist beyond the core procedure.
- Run `scripts/self-check.mjs --check --json` after editing this django-domain-delivery resource tree or before claiming deterministic support-file readiness; use `--help` for options and `--dry-run` for read-only preview semantics.
- Use `evals/regression.json` when calibrating django-domain-delivery trigger boundaries, happy-path/failure-path coverage, boundary rollback behavior, or resource-tree regressions.
- Open `examples/workflow.md` when a concrete django-domain-delivery workflow example is needed for sequencing, evidence selection, or anti-example comparison.
- Use `templates/output-contract.md` when emitting django-domain-delivery-report so status, evidence, confidence, blockers, risks, rollback, and nextAction stay consistent.

## Related

- `supervibe:source-driven-development`
- `supervibe:tdd`
- `supervibe:test-strategy`
- `supervibe:auth-flow-design`
- `supervibe:error-envelope-design`
- `supervibe:verification`

---
> Source: [vTRKA/supervibe](https://github.com/vTRKA/supervibe) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
