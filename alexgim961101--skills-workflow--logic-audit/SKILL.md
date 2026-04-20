---
name: logic-audit
description: | Use when this capability is needed.
metadata:
  author: alexgim961101
---

# Logic Audit (Mental Sandbox Simulation)

## Goal
Proactively discover logical flaws in design drafts and suggest safeguards.
Catch defects through "mental simulation" before the design is converted to code.

## Instructions

### Step 0: Summarize the Design
Before starting verification, extract the following elements from the current design:

- **Entities**: What data models/tables are involved?
- **State presence**: Are there entities with state? (order status, user status, etc.)
- **Shared resources**: Is there data that multiple requests access concurrently?
- **External dependencies**: Are there external APIs, message queues, 3rd-party services?
- **Side Effects**: Are there hard-to-reverse operations like notifications, emails, payments, event publishing?

This list becomes the input for the following 4 verifications.

---

### (1) State Machine Verification

Performed when entities with state exist.

#### Checklist
- [ ] **All states listed**: Are all possible states defined without gaps?
- [ ] **Illegal transition check**: Are "unreachable states" specified for each state?
- [ ] **Termination path**: Does a path to a terminal state exist from every state?
- [ ] **Initial state**: What state does the entity start in upon creation?
- [ ] **Concurrent transitions**: What if two transitions are requested simultaneously for the same entity?
- [ ] **Transition conditions**: Are preconditions (guards) specified for each transition?
- [ ] **Transition side effects**: Are side effects defined for each state transition?

#### Verification Method: Transition Matrix
Create the matrix below from the states extracted from the design.
✅ = Allowed, ❌ = Illegal, 🔒 = Conditional (guard required)

```
        → Pending  Active  Paused  Completed  Cancelled
Pending     -       ✅      ❌       ❌        ✅
Active      ❌       -      ✅       ✅        ✅
Paused      ❌      ✅       -       ❌        ✅
Completed   ❌      ❌      ❌        -        ❌
Cancelled   ❌      ❌      ❌       ❌         -
```

#### Common Defect Patterns
| Pattern | Problem | Solution |
|---------|---------|----------|
| **Zombie state** | Cannot exit a state (deadlock) | Add timeout transition or admin force transition |
| **Resurrection transition** | Returns from terminal to active state | Block terminal state transitions at code level |
| **Implicit state** | Exists only in code, no explicit definition | Define all states as enum |
| **Missing transition** | A→B defined but A→C path is needed but missing | Verify all combinations with matrix |
| **Side effect mismatch** | Transition succeeds but notification fails | Saga/Outbox pattern or compensating transaction |

#### Output Format
```
🔍 State Machine Verification: {Entity name}
States: [Pending, Active, Paused, Completed, Cancelled]

⚠️ [Med] Zombie state found
- Location: Paused state
- Problem: Cannot transition directly from Paused to Completed. If a user wants to
           complete after Pause, they must go through Active first — is this intentional?
- Suggestion A: Add Paused → Completed transition (guard: progress = 100%)
- Suggestion B: If intentional constraint, show guidance message in UI
- Needs confirmation ← ask the user
```

---

### (2) Concurrency Verification

Performed when shared resources exist.

#### Checklist
- [ ] **Concurrent writes**: What if two users modify the same record simultaneously?
- [ ] **Concurrent creation**: What if simultaneous INSERTs with the same unique key?
- [ ] **Read-Modify-Write**: What if another request intervenes between Read → Modify → Write?
- [ ] **Counters/Balances**: Can concurrent deductions go negative?
- [ ] **Distributed environment**: Is it safe with multiple server instances?

#### Verification Method: 2-Request Simulation
Pick the most dangerous API and simulate **2 requests arriving at the same millisecond**.

```
Time →  T0          T1          T2          T3
────────────────────────────────────────────────
Req A:  READ(stock=5)  ---         WRITE(stock=4)  ---
Req B:  ---         READ(stock=5)  ---         WRITE(stock=4)
                                                ↑ Problem! Should be 3
```

#### Concurrency Resolution Strategy Guide

| Situation | Strategy | Implementation | Tradeoff |
|-----------|----------|---------------|----------|
| Low conflict frequency, performance matters | **Optimistic Lock** | version column + WHERE version = X | Retry needed on conflict |
| High conflict frequency, integrity matters | **Pessimistic Lock** | SELECT ... FOR UPDATE | Performance degradation, deadlock risk |
| Distributed env, multiple servers | **Distributed Lock** | Redis SETNX, Redlock | Infra dependency, failure propagation |
| Idempotent handling needed | **Idempotency Key** | unique key + upsert | Client cooperation needed |
| Numeric increment/decrement | **Atomic Operation** | UPDATE SET x = x - 1 WHERE x > 0 | Only DB-level atomicity |
| Event ordering matters | **Optimistic Lock + Queue** | version check → retry via queue on failure | Increased complexity |

#### Output Format
```
🔍 Concurrency Verification: {API name}

⚠️ [High] Lost Update possible
- Location: PUT /orders/:id/status
- Simulation:
  Req A: READ(status=pending) → WRITE(status=active)
  Req B: READ(status=pending) → WRITE(status=cancelled)
  → Last-Write-Wins problem
- Suggestion: Optimistic Lock
  - Add `version INT DEFAULT 0` to orders table
  - UPDATE orders SET status = ?, version = version + 1
    WHERE id = ? AND version = ?
  - If affected rows = 0, return 409 Conflict
```

---

### (3) Data Integrity Verification

Performed when relationships between entities exist.

#### Checklist
- [ ] **Orphan check**: What happens to children when parent is deleted?
- [ ] **Referential integrity**: Are FK constraints set everywhere they're needed?
- [ ] **Soft delete ripple**: Does soft delete affect related entity queries?
- [ ] **Cascade scope**: Does CASCADE DELETE unintentionally delete a wide range?
- [ ] **Nullable relation**: Can one side of the relationship be null? (optional relation)
- [ ] **Circular reference**: Does A → B → C → A circular dependency exist?
- [ ] **Many-to-many**: Is the unique constraint on the junction table correct?

#### Verification Method: Deletion Scenario Simulation
For each entity, trace "what happens to the rest when this entity is deleted?"

```
When User is deleted:
├── User's Orders → ?
│   ├── A) CASCADE: Orders also deleted (Dangerous! Accounting data loss)
│   ├── B) SET NULL: order.user_id = NULL (anonymized order)
│   ├── C) RESTRICT: Cannot delete if orders exist
│   └── D) SOFT DELETE: Set user.deleted_at, orders remain
│
├── User's Comments → ?
│   ├── A) CASCADE: Comments also deleted
│   └── B) SET NULL: Display as "deleted user"
│
└── User's Sessions → ?
    └── A) CASCADE: Immediately invalidate all sessions
```

#### Common Integrity Issues

| Pattern | Problem | Solution |
|---------|---------|----------|
| **Cascade bomb** | Deleting User simultaneously deletes thousands of related records → transaction timeout | Soft delete + background cleanup |
| **Orphan records** | Application-level reference without FK → orphaned data after parent deletion | Add FK constraint or periodic cleanup job |
| **Phantom read** | Another transaction inserts during transaction → aggregate mismatch | Serializable isolation or explicit lock |
| **Soft delete ghost** | Missing deleted_at filter → deleted data appears in queries | Auto filter in global scope/middleware |
| **Many-to-many duplicates** | Duplicate relationship inserted without unique constraint | Composite unique index |

#### Output Format
```
🔍 Data Integrity Verification: When {Entity name} is deleted

⚠️ [High] Cascade bomb risk
- Location: When User is deleted, Orders CASCADE
- Problem: Deleting a user with 10K orders may cause transaction timeout
          + permanent loss of accounting data
- Suggestion: User should be Soft Delete Only
  - Set users.deleted_at
  - Keep related Orders, display user info as "Deleted user"
  - Anonymize personal data via batch job after 30 days
```

---

### (4) Race Condition Verification

Performed when shared resources + time-gap access exist.

#### Checklist
- [ ] **Check-then-Act**: In patterns that check a condition then act, is there a gap between check and action?
- [ ] **Double Spend**: Can another deduction occur between checking and deducting balance/points/inventory?
- [ ] **TOCTOU**: Time Of Check vs Time Of Use inconsistency
- [ ] **Pub/Sub ordering**: Is ordering guaranteed between event publishing and consumption?
- [ ] **Cache Invalidation**: Consistency between cache update and DB update
- [ ] **File/Resource contention**: Concurrent writes to the same file/S3 key

#### Verification Method: Timeline Diagram
List the suspected paths chronologically to verify.

```
Scenario: Coupon usage (1 remaining)

Time →  T0              T1              T2              T3
───────────────────────────────────────────────────────────
Req A:  CHECK(rem=1)    ---             USE(rem=0) ✅    ---
Req B:  ---             CHECK(rem=1)    ---             USE(rem=-1) 💥
                        ↑ Still sees 1 because A hasn't USEd yet

Result: 1 coupon but 2 people used it → Double Spend
```

#### Race Condition Resolution Patterns

| Pattern | Use Case | Implementation |
|---------|----------|---------------|
| **Atomic Check-and-Set** | Simple numeric increment/decrement | `UPDATE SET qty = qty - 1 WHERE qty > 0` + check affected rows |
| **Pessimistic Lock** | Complex business logic | `SELECT ... FOR UPDATE` → logic → COMMIT |
| **Optimistic Lock** | Rare conflicts | version check → retry on failure |
| **Redis Lock** | Distributed environment | `SET key value NX EX 30` → work → DEL key |
| **Unique Constraint** | Duplicate creation prevention | DB unique index + ON CONFLICT handling |
| **Outbox Pattern** | DB write + event publish atomicity | Insert into outbox table in same transaction → separate poller publishes |
| **Saga Pattern** | Distributed transactions | Each service local transaction + compensating transaction |

#### Output Format
```
🔍 Race Condition Verification: {Feature name}

⚠️ [Critical] Double Spend possible
- Location: POST /coupons/:id/use
- Scenario:
  T0: Req A → CHECK(remaining=1) → OK
  T1: Req B → CHECK(remaining=1) → OK (A not yet reflected)
  T2: Req A → USE → remaining=0
  T3: Req B → USE → remaining=-1 💥
- Suggestion: Atomic Check-and-Set
  ```sql
  UPDATE coupons
  SET remaining = remaining - 1
  WHERE id = ? AND remaining > 0
  ```
  If affected_rows = 0 → 409 "Coupon already exhausted"
```

---

### Step Final: Comprehensive Risk Report

After all verifications, report in the format below:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 Logic Audit Complete
━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Verification Summary:
  (1) State Machine:  ✅ Pass / ⚠️ N issue(s) found
  (2) Concurrency:    ✅ Pass / ⚠️ N issue(s) found
  (3) Data Integrity: ✅ Pass / ⚠️ N issue(s) found
  (4) Race Condition: ✅ Pass / ⚠️ N issue(s) found

Findings:
  🔴 Critical: N
  🟡 High: N
  🟢 Med: N
  ⚪ Low: N

Applied Safeguards:
  1. {Entity} — Optimistic Lock (version field)
  2. {API} — Atomic Check-and-Set (WHERE qty > 0)
  3. {Deletion} — Soft Delete Only + 30-day batch cleanup

⚠️ User Confirmation Required:
  1. Allow direct Paused → Completed transition? (A/B)
  2. User deletion policy: Soft Delete vs Hard Delete? (A/B/C)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Constraints
- Skip non-applicable verifications with "N/A (stateless design)"
- When defects are found, always provide **specific fix proposals + code/SQL-level suggestions**
- Risk levels: Critical > High > Med > Low
  - **Critical**: Data loss or financial damage possible
  - **High**: Data inconsistency possible
  - **Med**: Issue possible only in edge cases
  - **Low**: Code quality/maintainability issue
- Do not apply fixes without user confirmation
- Critical/High must be reflected in the design before proceeding; Med/Low can proceed after being recorded

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexgim961101) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
