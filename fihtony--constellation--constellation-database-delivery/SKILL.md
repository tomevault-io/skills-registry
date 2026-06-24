---
name: constellation-database-delivery
description: > Use when this capability is needed.
metadata:
  author: fihtony
---

# Database Delivery

## When To Apply

- Schema evolution, migrations, query changes, repository/data-access edits, or performance-sensitive persistence work.
- Any feature that changes stored data shape, constraints, lifecycle, or reporting behavior.
- Review of SQL, ORM usage, indexing, or data consistency assumptions.

## Build Rules

- Model the data change explicitly: current state, target state, migration path, and rollback path.
- Prefer additive, reversible migrations unless destructive changes are explicitly justified.
- Keep queries correct before optimizing them; then remove obvious scans, N+1 patterns, and unnecessary round trips.
- Enforce integrity in the data model or persistence boundary, not only in UI code.

## Safety Checklist

- Consider nullability, defaults, uniqueness, foreign-key relationships, and backfill behavior.
- Verify the effect on existing reads and writes, not just the new happy path.
- For performance-sensitive changes, document expected indexes, query filters, and sort paths.
- Include tests that prove the persistence behavior or query contract required by the feature.

## Review Standards

- Reject schema changes with no migration story.
- Reject query changes that are hard to reason about, unbounded by default, or likely to regress under production-sized data.
- Reject persistence logic that leaks storage details across unrelated layers.

---
> Source: [fihtony/constellation](https://github.com/fihtony/constellation) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
