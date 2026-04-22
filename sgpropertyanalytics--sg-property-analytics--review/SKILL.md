---
name: review
description: Unified code review orchestrator. Runs design review, historical context discovery, plan validation, pattern analysis, simplicity checks, contract validation, risk detection, and inline tests. Use when you want a comprehensive review before pushing or merging. Use when this capability is needed.
metadata:
  author: sgpropertyanalytics
---

# Code Review Orchestrator

Comprehensive code review workflow that catches issues BEFORE pushing.

**Trigger:** `/review`, "review this", "check my changes"

**Enhanced with:** `thoughts-locator` for historical context, `thoughts-analyzer` for plan validation, improved risk detection with incident awareness.

---

## CRITICAL REQUIREMENTS

> **YOU MUST EXECUTE EVERY STEP BELOW. THIS IS NOT OPTIONAL.**
>
> 1. **USE THE TASK TOOL** to call agents in Steps 1-5
> 2. **USE THE BASH TOOL** to run tests in Step 6
> 3. **SHOW ACTUAL OUTPUT** from test commands (pass/fail counts)
> 4. **PRODUCE THE FINAL REPORT** in the exact format specified
> 5. **DESIGN REVIEW IS BLOCKING** - If design-reviewer returns RETHINK or STOP, halt and discuss with user
>
> If you skip any step, the review is INCOMPLETE and INVALID.

---

## WORKFLOW DIAGRAM

```
USER INVOKES: "/review" or "review this" or "check my changes"
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 0: SCOPE DETECTION                                         │
│ Use Bash tool: git diff --name-only                             │
│ Output: frontend | backend | contracts | data | both            │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 0.1: HISTORICAL CONTEXT DISCOVERY                          │
│ Use Task tool: subagent_type="thoughts-locator"                 │
│ Find: Related docs, historical incidents, relevant guardrails   │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 0.2: CHECK FOR RELATED PLAN                                │
│ Use Bash/Glob: Look for docs/plans/ files related to changes    │
│ If found: Use Task with thoughts-analyzer to extract key points │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 0.5: LOAD ENGINEERING CONTEXT                              │
│ Use Read tool: claude.md + insights from Step 0.1/0.2           │
│ Extract: Core Invariants, Library-First, Hard Constraints       │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ ★ STEP 1: DESIGN REVIEW (BLOCKING)                              │
│ Use Task tool: subagent_type="design-reviewer"                  │
│ Ask: Should this exist? Simpler approach? Right problem?        │
│ If RETHINK or STOP → Halt review, discuss with user             │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 2: PATTERN ANALYSIS                                        │
│ Use Task tool: subagent_type="codebase-pattern-finder"          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 3: SIMPLICITY CHECK                                        │
│ Use Task tool: subagent_type="simplicity-reviewer"              │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 4: CONTRACT & CONSISTENCY CHECK                            │
│ Use Task tool: subagent_type="fullstack-consistency-reviewer"   │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 5: RISK DETECTION                                          │
│ Use Task tool: subagent_type="risk-agent"                       │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 6: RUN INLINE TESTS                                        │
│ Use Bash tool: pytest, npm run lint, npm run typecheck          │
│ MUST show actual test output with pass/fail counts              │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 7: ITERATE ON FAILURES                                     │
│ Auto-fix lint/type errors, ask user for logic failures          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│ STEP 8: FINAL REPORT                                            │
│ Design | Pattern | Simplicity | Contracts | Risks | Tests       │
│ Verdict: READY TO PUSH or NEEDS WORK                            │
└─────────────────────────────────────────────────────────────────┘
```

---

# STEP 0: SCOPE DETECTION

**REQUIRED ACTION:** Use Bash tool to run these commands:

```bash
# Get changed files (committed)
git diff --name-only HEAD~1

# Get changed files (uncommitted)
git diff --name-only
git diff --name-only --cached
```

**REQUIRED OUTPUT:** Categorize the scope:

| Scope | Condition |
|-------|-----------|
| `frontend` | Only `frontend/**` files changed |
| `backend` | Only `backend/**` files changed |
| `contracts` | Any `api/contracts/**`, `generated/**`, or adapter files |
| `data` | Any `backend/data/**` or ETL scripts |
| `both` | Frontend + backend files changed |

**Store the scope** - you will use it in Step 5 to select the test tier.

---

# STEP 0.1: HISTORICAL CONTEXT DISCOVERY (NEW)

**PURPOSE:** Before reviewing code, discover relevant documentation and historical incidents that might inform the review.

**REQUIRED ACTION:** Use the Task tool with these parameters:

```
Tool: Task
subagent_type: "thoughts-locator"
prompt: |
  Find documentation relevant to these changed files:
  [LIST THE CHANGED FILES FROM STEP 0]

  Search for:
  1. Historical Incidents (REPO_MAP.md Section 9) related to these files/areas
  2. Architecture documentation in docs/ that covers these components
  3. Relevant guardrail skills in .claude/skills/ for this type of change
  4. Recent investigation checkpoints in git history

  Return:
  - Relevant document paths with brief descriptions
  - Specific historical incidents to watch for
  - Guardrails that should be checked
```

**WAIT for agent to complete.**

**Store findings** - you will reference these in Step 0.5 and Step 4 (risk detection).

---

# STEP 0.2: CHECK FOR RELATED PLAN (NEW)

**PURPOSE:** If implementation follows a documented plan, verify alignment.

**REQUIRED ACTION:** Use Bash tool to check for related plans:

```bash
# Check if docs/plans/ exists and list recent plans
ls -lt docs/plans/ 2>/dev/null | head -10

# Search for plans mentioning changed files
grep -l "[changed_file_pattern]" docs/plans/*.md 2>/dev/null
```

**IF a related plan is found:**

Use the Task tool to analyze it:

```
Tool: Task
subagent_type: "thoughts-analyzer"
prompt: |
  Analyze this implementation plan for review context:
  [PLAN_FILE_PATH]

  Extract:
  1. Key decisions that were made
  2. Success criteria defined
  3. Anti-patterns to avoid
  4. Files that should have been changed
  5. Historical incidents addressed

  Return: Concise summary of what to verify in the review
```

**Store findings** - compare actual changes against plan expectations in Step 7.

---

# STEP 0.5: LOAD ENGINEERING CONTEXT

**REQUIRED ACTION:** Read the core engineering principles AND integrate context from Steps 0.1/0.2.

```
Tool: Read
file_path: claude.md (or CLAUDE.md)
```

**Extract and remember these sections:**

1. **§1 Core Invariants** - Non-negotiable rules (layer responsibilities, single source of truth)
2. **§1.6 Library-First** - Check if custom code could use a library
3. **§2 Hard Constraints** - Memory limits, data immutability
4. **§5 Backend Change Protocol** - The 4 questions before any backend change
5. **§7.2 API & Code Design** - 12 principles (single source of truth, fail fast, boring is good, etc.)
6. **§7.3 Param & Data Flow Integrity** - 5 principles (parse once, canonicalize at edges, one name per concept, immutable after normalization)
7. **§9 Historical Incidents** - Check REPO_MAP.md for landmines

**Integrate findings from Steps 0.1 and 0.2:**

| Source | What to Remember |
|--------|------------------|
| Step 0.1 (thoughts-locator) | Historical incidents to watch for, guardrails to check |
| Step 0.2 (thoughts-analyzer) | Plan decisions, success criteria, expected file changes |

**Why this matters:** Reviews that don't reference these principles miss systemic issues.
For example, a review might approve code that violates layer responsibilities or
recreates functionality that a library already provides.

**Context Checklist (from preceding steps):**
- [ ] Historical incidents identified from thoughts-locator
- [ ] Relevant guardrails noted (sql-guardrails, contract-async-guardrails, etc.)
- [ ] Plan expectations extracted (if plan exists)
- [ ] Engineering principles loaded from claude.md

**Key questions to keep in mind during review:**

| Principle | Question to Ask |
|-----------|-----------------|
| Layer Responsibilities | Does this component own logic it shouldn't? |
| Single Source of Truth | Is this data duplicated elsewhere? One canonical source? |
| Reuse-First | Does this match existing patterns in sibling files? |
| Library-First | Is this >50 lines of infrastructure that a library solves? |
| Production-Grade | Is this a band-aid fix or a proper solution? |
| Data Correctness | Are invariants computed before filters? Joins on IDs not names? |
| Don't Ship Unused | Is this actually used today, or "might be useful someday"? |
| One Job | Does this component do exactly one thing? |
| Fail Fast | Does this log-and-continue, or throw on errors? |
| Boring Is Good | Is this obvious code, or clever code? |
| Delete Before Add | Can we remove something instead of adding? |
| Explicit Dependencies | Are all inputs in the function signature? |
| Parse Once | Is this param parsed at entry, or transformed through multiple layers? |
| Canonicalize at Edges | Is data converted to final form at API boundary? |
| One Name Per Concept | Can you grep ONE name to find all usages? No aliases? |
| Immutable After Parse | Are params frozen after normalization, or mutated by layers? |
| Cache Key = Query Key | Does cache key use same resolved values as the actual query? |

---

# STEP 1: DESIGN REVIEW (BLOCKING)

> **This step is BLOCKING.** If the design-reviewer returns RETHINK or STOP, halt the review and discuss with the user before proceeding.

**PURPOSE:** Before diving into implementation details, step back and ask fundamental questions about the approach. The best code is code that doesn't need to exist.

**REQUIRED ACTION:** Use the Task tool with these EXACT parameters:

```
Tool: Task
subagent_type: "design-reviewer"
prompt: |
  Conduct a design review for these changes:
  [LIST THE CHANGED FILES FROM STEP 0]

  Commit message / PR title:
  [PASTE THE COMMIT MESSAGE OR DESCRIBE THE CHANGE]

  Context from Step 0.5:
  [PASTE RELEVANT ENGINEERING PRINCIPLES AND CONSTRAINTS]

  Answer the 5 Design Questions:
  1. What problem is this solving? (State in ONE sentence)
  2. Is there a simpler approach? (10x Simpler Test)
  3. Do abstractions earn their complexity? (Used 3+ times?)
  4. Are we solving the right problem? (Root cause vs symptom?)
  5. Is scope appropriate? (Only what was requested?)

  Output format:
  - Problem Statement (1 sentence)
  - Verdict: APPROVED | SIMPLIFY | RETHINK | STOP
  - If not APPROVED: What should change and why
  - Watch-fors for implementation review
```

**WAIT for agent to complete.**

**VERDICT HANDLING:**

| Verdict | Action |
|---------|--------|
| **APPROVED** | Proceed to Step 2 |
| **SIMPLIFY** | Show simplification suggestions, ASK user if they want to proceed or revise |
| **RETHINK** | HALT. Show alternative approach. Discuss with user before proceeding. |
| **STOP** | HALT. Explain why this code shouldn't exist. Get user confirmation to proceed. |

**IF RETHINK or STOP:** Do NOT continue to Step 2. The review is blocked until user confirms the design direction.

---

# STEP 2: PATTERN ANALYSIS

**REQUIRED ACTION:** Use the Task tool with these EXACT parameters:

```
Tool: Task
subagent_type: "codebase-pattern-finder"
prompt: |
  Analyze the following changed files for pattern compliance:
  [LIST THE CHANGED FILES FROM STEP 0]

  1. Find 3+ sibling files in the same directory
  2. Run: git log -20 -- <changed_files>
  3. Extract common patterns from siblings
  4. Report any deviations from the majority pattern

  Output format:
  - Reference patterns found
  - Sibling examples
  - Deviations detected (if any)
```

**WAIT for agent to complete before proceeding to Step 3.**

---

# STEP 3: SIMPLICITY CHECK

**REQUIRED ACTION:** Use the Task tool with these EXACT parameters:

```
Tool: Task
subagent_type: "simplicity-reviewer"
prompt: |
  Review the following changed files for simplicity:
  [LIST THE CHANGED FILES FROM STEP 0]

  Check:
  1. Can this be done with fewer files?
  2. Can this be done with fewer lines?
  3. Is there a library solution? (Check CLAUDE.md §1.6)
  4. Does this match sibling patterns?
  5. Is this solving today's problem (not hypothetical future)?

  Output format:
  - Verdict: PASS | NEEDS SIMPLIFICATION | FLAGGED
  - Complexity: Lines, Files, Call depth
  - Simpler alternative (if found)
  - Library-First check: PASS | VIOLATION
  - Pattern match: ALIGNED | DIVERGENT
```

**WAIT for agent to complete.**

**IF FLAGGED:** Show the simpler alternative and ASK user before proceeding.

---

# STEP 4: CONTRACT & CONSISTENCY CHECK

**REQUIRED ACTION:** Use the Task tool with these EXACT parameters:

```
Tool: Task
subagent_type: "fullstack-consistency-reviewer"
prompt: |
  Check frontend↔backend contract alignment for:
  [LIST THE CHANGED FILES FROM STEP 0]

  Phase 0: Git state verification
  - Check uncommitted changes, file existence

  Phase 1: Contract consistency
  - Param names match (FE → BE)?
  - Response fields handled (BE → FE)?
  - Enum values match?
  - Adapters handle all fields?
  - Identity drift check: same concept uses ONE name across all layers?
    (grep for synonyms, check cache keys match query params)

  Phase 2: Chart impact (if backend changed)
  - What endpoints affected?
  - What charts consume them?
  - Risk level: HIGH | MEDIUM | LOW

  Output: Contract issues, impact assessment
```

**WAIT for agent to complete before proceeding to Step 5.**

---

# STEP 5: RISK DETECTION

**REQUIRED ACTION:** Use the Task tool with these EXACT parameters.

**CRITICAL:** Include context from Steps 0.5-4 so risk-agent doesn't make false positives.

```
Tool: Task
subagent_type: "risk-agent"
prompt: |
  ## Context from Previous Review Steps

  ### Design Review Findings (from Step 1)
  [PASTE the output from design-reviewer]
  - Design verdict: APPROVED / SIMPLIFY / RETHINK / STOP
  - Watch-fors noted for implementation review

  ### Engineering Principles (from Step 0.5)
  [PASTE key principles from CLAUDE.md that were extracted]

  ### Pattern Analysis Findings (from Step 2)
  [PASTE the output from codebase-pattern-finder]
  - What patterns are established in sibling files?
  - What git history shows about these files?

  ### Simplicity Check Findings (from Step 3)
  [PASTE the output from simplicity-reviewer]
  - Library-First violations found?
  - Is this solving today's problem or hypothetical future?

  ### Contract Check Findings (from Step 4)
  [PASTE the output from fullstack-consistency-reviewer]
  - Any contract drift detected?
  - What's the current migration state?

  ### Technology Context (CRITICAL for avoiding false positives)
  - Storage type: [sessionStorage clears on close / localStorage persists]
  - Migration state: [React Query X/Y migrated, mixed patterns EXPECTED]
  - Framework facts:
    - Tree-shaking removes unused imports (don't flag dead imports)
    - import.meta.env.DEV code doesn't run in prod
    - Zustand persist hydration is synchronous (no race possible)
    - Page-namespaced stores are isolated (no cross-page leak)

  ---

  ## Files to Review
  [LIST THE CHANGED FILES FROM STEP 0]

  ## Default Stance: SKEPTICAL

  Assume code has bugs until proven otherwise.

  1. Verify each change has test coverage
  2. Check edge cases explicitly
  3. Confirm patterns match codebase standards
  4. Question complexity — is there a simpler way?
  5. Only APPROVE when confident

  **One sentence:** A good reviewer finds the bugs the author missed, not confirms their assumptions.

  ## Your Role
  Be a senior code reviewer with a skeptical mindset. Look for:
  1. Real bugs that will break in production
  2. Inefficiencies that should be improved
  3. Better ways to implement the same functionality
  4. Security vulnerabilities
  5. Performance issues

  ## Reality Check Protocol (Prevents False Positives)
  Before reporting ANY finding, verify:
  1. READ the actual code (not just grep output)
  2. Check 20 lines of context for guards
  3. Verify the technology behaves as you assume (use the context provided)
  4. Confirm it's not already addressed by previous step findings
  5. Exclude comments, test files, and dead code from findings

  ## Output Format (Simplified)

  ### P0: Must Fix Before Merge
  🔴 **file:line** — [issue]
     Code: `[snippet]`
     Fix: `[solution]`

  ### P1: Should Fix
  🟡 **file:line** — [issue]

  ### Verified Not Issues (show your work)
  - Checked [pattern] at file:line → [why it's OK]

  ### Verdict: APPROVE | REQUEST CHANGES | NEEDS DISCUSSION
```

**WAIT for agent to complete before proceeding to Step 5.**

---

# STEP 5: RUN INLINE TESTS

**REQUIRED ACTION:** Use Bash tool to execute tests based on scope from Step 0.

## Tier Selection Logic

| Scope from Step 0 | Run These Tiers |
|-------------------|-----------------|
| `frontend` only | Tier 1 + Tier 2 (frontend only) |
| `backend` only | Tier 1 + Tier 2 (backend only) |
| `contracts` touched | Tier 1 + Tier 2 + Tier 3 |
| `both` | Tier 1 + Tier 2 + Tier 3 |
| `data` changed | Tier 1 + Tier 2 + Tier 3 |
| Pre-merge | Tier 1 + Tier 2 + Tier 3 + Tier 4 |

---

## Tier 1: Quick Checks (ALWAYS RUN - ~30s)

**REQUIRED:** Use Bash tool to run:

```bash
# Frontend lint + typecheck
cd frontend && npm run lint && npm run typecheck
```

```bash
# Backend syntax check (catches syntax errors, NOT runtime errors like NameError)
python -m py_compile backend/routes/*.py backend/services/*.py
```

```bash
# Contract drift check
python backend/scripts/generate_contracts.py --check
```

```bash
# Historical landmine check - warns if changes touch files from past incidents
python backend/scripts/check_landmines.py
```

**Record pass/fail for each command.**

**NOTE:** `py_compile` only catches **syntax errors**. Runtime errors like NameError, TypeError
are caught by endpoint smoke tests in Tier 2.

---

## Tier 2: Core Tests (DEFAULT - ~3 min)

**REQUIRED:** Use Bash tool to run:

### Backend Tests (if backend changed):
```bash
cd backend && pytest tests/test_normalize.py tests/test_api_contract.py -v
```

```bash
cd backend && pytest tests/test_sql_guardrails.py tests/test_sql_safety.py -v
```

```bash
cd backend && pytest tests/test_property_age_bucket.py tests/test_param_coverage.py -v
```

### Contract STRICT Mode (if routes/schemas changed):
```bash
# Validates ALL contracted endpoints return schema-compliant responses
# Catches undeclared fields that slip through in WARN mode
cd backend && CONTRACT_MODE=strict pytest tests/contracts/test_all_endpoints_strict.py -v --tb=short
```

**Why this matters:** On Jan 3, 2026, `/api/auth/subscription` was returning undeclared
`_debug_*` fields. The decorator logged warnings but nothing failed. This test catches
that class of bug by running all endpoints in STRICT mode.

### Endpoint Smoke Test (if routes changed):
```bash
# Catches runtime errors (NameError, TypeError, AttributeError) that py_compile misses
# Actually CALLS every endpoint - static analysis can't catch these
cd backend && pytest tests/contracts/test_endpoint_smoke.py -v --tb=short
```

**Why this matters:** On Jan 3, 2026, `/api/projects/hot` had `NameError: district_param`.
`py_compile` passed because it only checks syntax. This test actually calls the endpoint.

### Route Coverage Check (if new routes added):
```bash
# Ensures new routes have @api_contract decorator (or documented exemption)
cd backend && pytest tests/contracts/test_route_coverage.py -v
```

**Why this matters:** Prevents new endpoints from bypassing contract validation.

### Frontend Tests (if frontend changed):
```bash
# Unit tests (adapter transforms, filter logic, etc.)
cd frontend && npm run test:ci
```

```bash
# Quick E2E smoke - catches React crashes, context errors, white-screen
# Runs critical pages with mocked API, checks for console errors
cd frontend && npm run build && npm run test:e2e:smoke
```

**Why E2E smoke in Tier 2:** Unit tests don't catch React context errors, missing providers,
or "white page" crashes. The smoke test actually renders pages and checks for:
- React error boundaries
- Console errors (`Cannot read properties of`, `is not a function`)
- White-screen (body has no content)

### Scripts:
```bash
python scripts/check_route_contract.py
```

```bash
python scripts/data_guard.py --ci
```

**Record pass/fail for each command.**

---

## Tier 3: Full Suite (COMPLEX CHANGES - ~8 min)

**REQUIRED when:** contracts touched, both FE+BE changed, data changed, or pre-merge.

**Use Bash tool to run:**

### Integration Tests:
```bash
cd backend && pytest tests/test_regression_snapshots.py -v
```

```bash
cd backend && pytest tests/test_api_invariants.py -v
```

```bash
cd backend && pytest tests/test_smoke_endpoints.py -v
```

```bash
cd backend && pytest tests/test_chart_dependencies.py -v
```

```bash
cd backend && pytest tests/test_kpi_guardrails.py -v
```

### E2E Smoke:
```bash
cd frontend && npm run build && npm run e2e:smoke
```

### Mock Validation:
```bash
python scripts/validate_e2e_mocks.py
```

**Record pass/fail for each command.**

---

## Tier 4: Full E2E Runtime (PRE-MERGE - ~10 min)

**REQUIRED when:** User explicitly requests, major UI changes, filter/state changes.

```bash
cd frontend && npm run e2e:full
```

---

## Bug Detection Matrix

What each test/check catches:

| Bug Type | Static Analysis | Test That Catches It |
|----------|-----------------|----------------------|
| **BACKEND** | | |
| **Syntax errors** | `py_compile` ✅ | - |
| **Import errors** | `py_compile` ✅ | - |
| **NameError** (undefined var) | ❌ Misses | `test_endpoint_smoke.py` |
| **TypeError** (wrong args) | ❌ Misses | `test_endpoint_smoke.py` |
| **AttributeError** | ❌ Misses | `test_endpoint_smoke.py` |
| **KeyError** | ❌ Misses | `test_endpoint_smoke.py` |
| **Undeclared response fields** | ❌ Misses | `test_all_endpoints_strict.py` |
| **Missing contract decorator** | ❌ Misses | `test_route_coverage.py` |
| **SQL injection** | ❌ Misses | `test_sql_safety.py` |
| **FRONTEND** | | |
| **Lint violations** | `npm run lint` ✅ | - |
| **Type errors (TS)** | `npm run typecheck` ✅ | - |
| **React context errors** | ❌ Misses | `e2e/smoke.spec.js` |
| **White-screen crashes** | ❌ Misses | `e2e/smoke.spec.js` |
| **Missing providers** | ❌ Misses | `e2e/smoke.spec.js` |
| **Console errors** | ❌ Misses | `e2e/smoke.spec.js` |
| **CROSS-CUTTING** | | |
| **Schema drift** | ❌ Misses | `generate_contracts.py --check` |
| **Param mismatch FE↔BE** | ❌ Misses | `check_route_contract.py` |
| **Data regression** | ❌ Misses | `test_regression_snapshots.py` |
| **Historical landmines** | ❌ Misses | `check_landmines.py` |
| **Security issues** | ❌ Misses | `risk-agent` (Step 4) |

**Key insight:** Static analysis (`py_compile`, `ast.parse`) only catches syntax errors.
Runtime errors require actually calling the endpoints (smoke tests).

---

## Complete Backend Test Inventory

All tests are in `backend/tests/`. Run based on what changed:

| Test File | When to Run | What It Catches |
|-----------|-------------|-----------------|
| `contracts/test_all_endpoints_strict.py` | If routes/schemas changed | Undeclared response fields |
| `contracts/test_endpoint_smoke.py` | If routes changed | Runtime errors (NameError, TypeError) |
| `contracts/test_route_coverage.py` | If new routes added | Missing @api_contract decorator |
| `test_normalize.py` | Always (Tier 2) | Param normalization bugs |
| `test_api_contract.py` | Always (Tier 2) | Contract infrastructure |
| `test_property_age_bucket.py` | Always (Tier 2) | Age classification bugs |
| `test_sql_guardrails.py` | Always (Tier 2) | SQL pattern violations |
| `test_sql_safety.py` | Always (Tier 2) | SQL injection risks |
| `test_param_coverage.py` | Always (Tier 2) | Missing param declarations |
| `test_regression_snapshots.py` | Tier 3 | Data value regressions |
| `test_api_invariants.py` | Tier 3 | API behavior changes |
| `test_smoke_endpoints.py` | Tier 3 | Endpoint reachability |
| `test_chart_dependencies.py` | Tier 3 | Chart↔backend coupling |
| `test_kpi_guardrails.py` | Tier 3 | KPI calculation bugs |
| `test_etl_validation.py` | If data changed |
| `test_aggregate_median.py` | If aggregate changed |
| `test_filter_builder.py` | If filters changed |
| `test_timeframe_resolution.py` | If timeframe changed |
| `test_contract_timeframe_normalize.py` | If timeframe changed |
| `test_subscription_schema_guard.py` | If auth changed |
| `test_user_entitlements.py` | If auth changed |
| `test_csv_diff_detection.py` | If data changed |
| `test_districts_superset.py` | If districts changed |
| `test_supply_summary.py` | If supply changed |
| `test_exit_queue.py` | If exit queue changed |
| `test_price_bands.py` | If price bands changed |
| `test_resale_velocity_kpi.py` | If KPI changed |
| `test_new_launch_absorption.py` | If absorption changed |
| `test_compliance.py` | If compliance changed |
| `test_request_logging.py` | If logging changed |
| `test_cache_key.py` | If caching changed |
| `test_verification_service.py` | If verification changed |
| `test_insights_timeframe_integration.py` | If insights changed |
| `test_price_projects_by_district_query.py` | If district query changed |

---

# STEP 6: ITERATE ON FAILURES

**REQUIRED:** Handle failures based on type:

| Error Type | Action |
|------------|--------|
| **Lint errors** | Auto-fix with `npm run lint -- --fix`, retry up to 3x |
| **Type errors** | Auto-fix, retry up to 3x |
| **Unit test failures** | Report failure, explain cause, ASK user before fixing |
| **Integration test failures** | Report failure, explain cause, ASK user before fixing |
| **Contract drift** | Run `python backend/scripts/generate_contracts.py`, retry |

### Auto-Fix Commands:

```bash
# Lint auto-fix
cd frontend && npm run lint -- --fix
```

```bash
# Python format
python -m black backend/
```

```bash
# Regenerate contracts
python backend/scripts/generate_contracts.py
```

**After auto-fix:** Re-run the failed tests from Step 5.

**After 3 failed attempts:** Report to user and stop.

---

# STEP 7: FINAL REPORT

**REQUIRED:** Output this EXACT format:

```markdown
# 📋 Review Report

**Branch:** [branch name]
**Scope:** [frontend | backend | both | contracts | data]
**Files Changed:** [count]
**Commits Reviewed:** [list with short descriptions]
**Date:** [ISO 8601]

---

## 🎯 TL;DR — What's This About?

### The Problem (ELI5)

> **Restaurant Analogy:** [Choose appropriate analogy based on the issue type]
>
> Think of our app like a restaurant:
> - **Frontend** = The dining room (what customers see)
> - **Backend** = The kitchen (where orders are processed)
> - **API Contract** = The order ticket (how waiter communicates with kitchen)
> - **Database** = The pantry (where ingredients are stored)
>
> **What was broken:**
> [Describe the issue using the analogy. Examples:]
> - "The waiter was writing orders in French, but the kitchen only reads English"
> - "The bouncer was letting everyone in without checking IDs"
> - "The kitchen was sending out dishes the menu didn't list"
>
> **In technical terms:**
> [1-2 sentence technical description]

### Analogy Reference Guide

| Issue Type | Analogy | Role |
|------------|---------|------|
| Contract mismatch | Order ticket | Waiter ↔ Kitchen communication |
| Auth/Security | Bouncer | Checks IDs at the door |
| Data validation | Quality inspector | Checks ingredients before cooking |
| Caching | Prep station | Pre-made items for speed |
| API response | Plated dish | What gets served to customer |
| Frontend state | Table status | Reserved, occupied, ready to clear |
| Database | Pantry/Inventory | Raw ingredients storage |
| Services | Line cooks | Each handles specific dish types |
| Routes | Order window | Where tickets come in |

---

## 🏗️ Architecture Impact

### Data Flow Affected

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           FULL STACK FLOW                                │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐          │
│  │  User    │───▶│ Frontend │───▶│   API    │───▶│ Backend  │          │
│  │  Action  │    │   Page   │    │  Client  │    │  Route   │          │
│  └──────────┘    └──────────┘    └──────────┘    └──────────┘          │
│       │               │               │               │                  │
│       │          [AFFECTED?]    [AFFECTED?]     [AFFECTED?]             │
│       │           ✅/❌            ✅/❌           ✅/❌                 │
│       │               │               │               │                  │
│       │               ▼               ▼               ▼                  │
│       │         ┌──────────┐    ┌──────────┐    ┌──────────┐          │
│       │         │ Component│    │ Adapter  │    │ Service  │          │
│       │         │  /Hook   │    │          │    │          │          │
│       │         └──────────┘    └──────────┘    └──────────┘          │
│       │               │               │               │                  │
│       │          [AFFECTED?]    [AFFECTED?]     [AFFECTED?]             │
│       │           ✅/❌            ✅/❌           ✅/❌                 │
│       │               │               │               │                  │
│       │               ▼               ▼               ▼                  │
│       │         ┌──────────┐    ┌──────────┐    ┌──────────┐          │
│       │         │  Chart   │    │ Contract │    │    DB    │          │
│       │         │          │    │  Schema  │    │   Query  │          │
│       │         └──────────┘    └──────────┘    └──────────┘          │
│       │               │               │               │                  │
│       │          [AFFECTED?]    [AFFECTED?]     [AFFECTED?]             │
│       │           ✅/❌            ✅/❌           ✅/❌                 │
│       │               │               │               │                  │
│       └───────────────┴───────────────┴───────────────┘                  │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘

LEGEND: ✅ = Changed in this PR | ❌ = Not affected | ⚠️ = Indirectly affected
```

### Affected Layers

| Layer | Status | Files | Impact |
|-------|--------|-------|--------|
| **Pages** | ✅/❌/⚠️ | [list] | [what changed] |
| **Components** | ✅/❌/⚠️ | [list] | [what changed] |
| **Hooks** | ✅/❌/⚠️ | [list] | [what changed] |
| **Adapters** | ✅/❌/⚠️ | [list] | [what changed] |
| **API Client** | ✅/❌/⚠️ | [list] | [what changed] |
| **Contracts** | ✅/❌/⚠️ | [list] | [what changed] |
| **Routes** | ✅/❌/⚠️ | [list] | [what changed] |
| **Services** | ✅/❌/⚠️ | [list] | [what changed] |
| **Database** | ✅/❌/⚠️ | [list] | [what changed] |

---

## 📁 Files Changed

### By Category

```
frontend/
├── src/
│   ├── pages/           [X files] ─────────────────── Page-level logic
│   │   └── [file.jsx]   [+X/-Y lines] [brief description]
│   │
│   ├── components/      [X files] ─────────────────── UI components
│   │   └── powerbi/
│   │       └── [Chart.jsx] [+X/-Y lines] [brief description]
│   │
│   ├── adapters/        [X files] ─────────────────── API response transforms
│   │   └── [adapter.js] [+X/-Y lines] [brief description]
│   │
│   ├── hooks/           [X files] ─────────────────── Data fetching
│   │   └── [hook.js]    [+X/-Y lines] [brief description]
│   │
│   └── generated/       [X files] ─────────────────── Auto-generated contracts
│       └── apiContract.json [+X/-Y lines] [regenerated]

backend/
├── routes/              [X files] ─────────────────── API endpoints
│   └── [route.py]       [+X/-Y lines] [brief description]
│
├── services/            [X files] ─────────────────── Business logic
│   └── [service.py]     [+X/-Y lines] [brief description]
│
├── api/contracts/       [X files] ─────────────────── Schema definitions
│   └── schemas/
│       └── [schema.py]  [+X/-Y lines] [brief description]
│
└── tests/               [X files] ─────────────────── Test files
    └── [test.py]        [+X/-Y lines] [brief description]
```

### Files Summary Table

| File | Lines Changed | Category | Risk |
|------|---------------|----------|------|
| `path/to/file.jsx` | +50/-20 | Component | 🟢 Low |
| `path/to/file.py` | +30/-10 | Service | 🟡 Medium |
| `path/to/schema.py` | +5/-2 | Contract | 🔴 High |

---

## 📝 Commit-by-Commit Breakdown

### Commit 1: `[hash]` — [short message]

```
Author: [name]
Date:   [date]

[Full commit message]
```

**What Changed:**
```
[file1.jsx]  │ Component │ +20/-5  │ Added loading state
[file2.py]   │ Service   │ +15/-3  │ Fixed date parsing
```

**The Issue:**
> [ELI5 explanation of what was wrong before this commit]
>
> Like a waiter who was...

**The Change:**
> [What this commit specifically does]

**The Improvement:**
> [How things are better after this commit]

**Diagram (if applicable):**
```
BEFORE:                          AFTER:
┌──────────┐                     ┌──────────┐
│ Frontend │──── null ────▶ 💥   │ Frontend │──── data ────▶ ✅
└──────────┘                     └──────────┘
     │                                │
     ▼                                ▼
No loading state                 Shows skeleton
```

---

### Commit 2: `[hash]` — [short message]

[Repeat structure for each commit...]

---

## 🔄 Before vs After

### Issue → Change → Improvement

| # | Issue (Before) | Change (What We Did) | Improvement (After) |
|---|----------------|---------------------|---------------------|
| 1 | [Problem description] | [Code change summary] | [Benefit/fix] |
| 2 | [Problem description] | [Code change summary] | [Benefit/fix] |
| 3 | [Problem description] | [Code change summary] | [Benefit/fix] |

### Visual Comparison

```
═══════════════════════════════════════════════════════════════════════
                              BEFORE
═══════════════════════════════════════════════════════════════════════

User clicks filter
        │
        ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│    Frontend     │────▶│    API Call     │────▶│    Backend      │
│  timeframe=M6   │     │  timeframe=M6   │     │  ??? (dropped)  │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                                                        │
                                                        ▼
                                                 Defaults to Y1 ❌

═══════════════════════════════════════════════════════════════════════
                               AFTER
═══════════════════════════════════════════════════════════════════════

User clicks filter
        │
        ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│    Frontend     │────▶│    API Call     │────▶│    Backend      │
│  timeframe=M6   │     │  timeframe=M6   │     │  timeframe=M6   │
└─────────────────┘     └─────────────────┘     └─────────────────┘
                                                        │
                                                        ▼
                                                 Uses M6 filter ✅
```

---

## 🧪 Component Interaction Map

### What Talks to What

```
┌─────────────────────────────────────────────────────────────────────┐
│                        COMPONENT INTERACTIONS                        │
└─────────────────────────────────────────────────────────────────────┘

Pages (Business Logic Owner)
│
├── MarketOverview.jsx
│   │
│   ├──uses──▶ usePowerBIFilters() ──────────▶ PowerBIFilterContext
│   │                                                   │
│   ├──renders──▶ TimeTrendChart ◀──────────────────────┘
│   │                  │                         (provides filters)
│   │                  │
│   │                  ├──calls──▶ useGatedAbortableQuery()
│   │                  │                   │
│   │                  │                   ├──▶ apiClient.get('/api/aggregate')
│   │                  │                   │           │
│   │                  │                   │           ▼
│   │                  │                   │    ┌─────────────────┐
│   │                  │                   │    │ Backend Route   │
│   │                  │                   │    │ analytics.py    │
│   │                  │                   │    └────────┬────────┘
│   │                  │                   │             │
│   │                  │                   │             ▼
│   │                  │                   │    ┌─────────────────┐
│   │                  │                   │    │ Service         │
│   │                  │                   │    │ dashboard_svc   │
│   │                  │                   │    └────────┬────────┘
│   │                  │                   │             │
│   │                  │                   │             ▼
│   │                  │                   │    ┌─────────────────┐
│   │                  │                   │    │ Database        │
│   │                  │                   │    │ transactions    │
│   │                  │                   │    └─────────────────┘
│   │                  │                   │
│   │                  │                   └──▶ transformTimeSeries() ◀── adapter
│   │                  │
│   │                  └──renders──▶ Chart.js <Line />
│   │
│   └── [other charts...]

LEGEND:
  ──uses──▶     Hook/Context usage
  ──renders──▶  Component rendering
  ──calls──▶    Function/API call
  ◀────────     Data flows back
```

---

## ✅ Review Checklist Summary

| Check | Result | Details |
|-------|--------|---------|
| Pattern Match | ✅ ALIGNED / ⚠️ DIVERGENT | [from Step 1] |
| Simplicity | ✅ PASS / ⚠️ NEEDS REVIEW | [from Step 2] |
| Contracts | ✅ ALIGNED / ❌ DRIFT | [from Step 3] |
| Risks | ✅ NONE / ⚠️ MEDIUM / ❌ HIGH | [from Step 4] |
| Tests | ✅ ALL PASS / ❌ X FAILURES | [from Step 5] |

---

## 🧪 Test Results

### Tier 1 (Quick Checks) — ~30s
| Test | Status | Output |
|------|--------|--------|
| Lint | ✅/❌ | [summary] |
| Typecheck | ✅/❌ | [summary] |
| Syntax | ✅/❌ | [summary] |
| Contract drift | ✅/❌ | [summary] |

### Tier 2 (Core Tests) — ~3 min
| Test File | Passed | Failed | Skipped |
|-----------|--------|--------|---------|
| test_normalize.py | X | 0 | 0 |
| test_api_contract.py | X | 0 | 0 |
| test_sql_guardrails.py | X | 0 | 0 |
| ... | ... | ... | ... |
| **Total** | **X** | **0** | **0** |

### Tier 3 (Full Suite) — ~8 min [if run]
| Test File | Passed | Failed | Skipped |
|-----------|--------|--------|---------|
| test_regression_snapshots.py | X | 0 | 0 |
| test_api_invariants.py | X | 0 | 0 |
| ... | ... | ... | ... |
| **Total** | **X** | **0** | **0** |

### Tier 4 (E2E Full) — ~10 min [if run]
| Suite | Passed | Failed | Skipped |
|-------|--------|--------|---------|
| e2e:full | X | 0 | 0 |

---

## 📊 Agent Findings

### From Pattern Analysis (Step 1)
> **Verdict:** ALIGNED / DIVERGENT
>
> [Summary from codebase-pattern-finder]
>
> **Reference Patterns Found:**
> - [pattern 1]
> - [pattern 2]
>
> **Deviations (if any):**
> - [deviation 1 with file:line]

### From Simplicity Check (Step 2)
> **Verdict:** PASS / NEEDS SIMPLIFICATION / FLAGGED
>
> [Summary from simplicity-reviewer]
>
> | Metric | Value |
> |--------|-------|
> | Lines of code | X |
> | Files touched | Y |
> | Call depth | Z layers |
>
> **Library-First Check:** PASS / VIOLATION

### From Contract Check (Step 3)
> **Verdict:** ALIGNED / DRIFT
>
> [Summary from fullstack-consistency-reviewer]
>
> **Param Coverage:**
> | Frontend Param | Backend Schema | Status |
> |----------------|----------------|--------|
> | timeframe | AGGREGATE_PARAM_SCHEMA | ✅/❌ |
> | district | AGGREGATE_PARAM_SCHEMA | ✅/❌ |

### From Risk Detection (Step 4)
> **Verdict:** APPROVE / REQUEST CHANGES / NEEDS DISCUSSION
>
> **🔴 MUST FIX (Blocking):**
> - [issue with file:line]
>
> **🟡 SHOULD FIX (Recommended):**
> - [issue with file:line]
>
> **💡 CONSIDER (Optional):**
> - [suggestion]
>
> **✅ LOOKS GOOD:**
> - [what's good about the code]

### From Historical Context (Step 0.1)
> **Relevant Historical Incidents:**
> - [Incident from REPO_MAP.md Section 9]
> - Verification: ✅ Avoided / ⚠️ Potentially Violated
>
> **Relevant Guardrails:**
> - [Guardrail from .claude/skills/] — [Checked/Not Applicable]

### From Plan Validation (Step 0.2) — *if plan exists*
> **Plan:** `docs/plans/[filename].md`
>
> **Plan Expectations vs Actual:**
> | Expected (from plan) | Actual | Status |
> |---------------------|--------|--------|
> | [File should change] | [Did/Didn't change] | ✅/❌ |
> | [Success criterion] | [Met/Not met] | ✅/❌ |
>
> **Deviations from Plan:**
> - [Any unexpected changes or missing changes]

---

## 🎯 Final Verdict

### **[READY TO PUSH]** ✅

All checks pass. No P0 or P1 issues found.

**OR**

### **[MERGE WITH FOLLOW-UP]** ⚠️

No P0 blockers, but P1 items need attention:
- [ ] [P1 item 1 with file:line]
- [ ] [P1 item 2 with file:line]

**OR**

### **[NEEDS WORK]** ❌

P0 blockers found:
- [ ] [P0 item 1 with file:line]
- [ ] [P0 item 2 with file:line]

**Action required before merge:**
1. [Specific action 1]
2. [Specific action 2]

---

## 📚 Quick Reference

### Pages Affected
- `/market-overview` — [affected/not affected]
- `/district-overview` — [affected/not affected]
- `/new-launch-market` — [affected/not affected]
- `/supply-inventory` — [affected/not affected]
- `/explore` — [affected/not affected]
- `/value-check` — [affected/not affected]
- `/exit-risk` — [affected/not affected]

### Manual Verification Needed
- [ ] [Page/Chart to manually check]
- [ ] [Page/Chart to manually check]

---

*Generated by `/review` • [timestamp]*
```

---

# AGENT/TOOL CALL SUMMARY

| Step | Tool | subagent_type | Purpose |
|------|------|---------------|---------|
| 0 | Bash | - | `git diff` scope detection |
| 0.1 | Task | `thoughts-locator` | Find relevant docs, incidents, guardrails |
| 0.2 | Bash + Task | `thoughts-analyzer` | Check for related plans, extract key decisions |
| 0.5 | Read | - | Load `claude.md` + integrate 0.1/0.2 context |
| 1 | Task | `codebase-pattern-finder` | Find sibling patterns |
| 2 | Task | `simplicity-reviewer` | Proactive simplicity check |
| 3 | Task | `fullstack-consistency-reviewer` | Contract validation |
| 4 | Task | `risk-agent` | Bug detection (with context from 0.1/0.2) |
| 5 | Bash | - | pytest, npm run lint/typecheck |
| 6 | Bash | - | Auto-fix commands |
| 7 | - | - | Generate report with plan validation |

---

# CI COVERAGE

This workflow covers ALL blocking CI checks:

| CI Check | In Review | Tier | Command |
|----------|-----------|------|---------|
| Contract Guard | ✅ | 1 | `generate_contracts.py --check` |
| Frontend Import Guard | ✅ | 1 | `npm run typecheck` |
| SQL Safety | ✅ | 2 | `test_sql_safety.py` + `test_sql_guardrails.py` |
| Data Guard | ✅ | 2 | `data_guard.py --ci` |
| Route Contract | ✅ | 2 | `check_route_contract.py` |
| Unit Tests | ✅ | 2 | Multiple test files |
| Lint + Typecheck | ✅ | 1 | `npm run lint && typecheck` |
| Frontend Build | ✅ | 3 | `npm run build` |
| Smoke Tests | ✅ | 3 | `test_smoke_endpoints.py` |
| Integration Tests | ✅ | 3 | `test_regression_snapshots.py` |
| E2E Smoke | ✅ | 3 | `npm run e2e:smoke` |
| E2E Full | ✅ | 4 | `npm run e2e:full` |
| Mock Validation | ✅ | 3 | `validate_e2e_mocks.py` |

**Coverage: 13/13 blocking checks (100%)**

---

# CHECKLIST FOR CLAUDE

Before marking review complete, verify:

- [ ] Step 0: Ran `git diff` and determined scope
- [ ] Step 0.1: Called `thoughts-locator` to find relevant docs/incidents/guardrails
- [ ] Step 0.2: Checked for related plans in docs/plans/, analyzed with `thoughts-analyzer` if found
- [ ] Step 0.5: Read `claude.md` AND integrated context from 0.1/0.2
- [ ] Step 1: Called `codebase-pattern-finder` agent via Task tool
- [ ] Step 2: Called `simplicity-reviewer` agent via Task tool
- [ ] Step 3: Called `fullstack-consistency-reviewer` agent via Task tool
- [ ] Step 4: Called `risk-agent` with historical incident context from Step 0.1
- [ ] Step 5: Ran pytest/npm commands via Bash tool based on tier
- [ ] Step 6: Auto-fixed any lint/type errors, asked user for logic failures
- [ ] Step 7: Produced final report with plan validation (if plan exists)

**If any box is unchecked, the review is INCOMPLETE.**

---

# RELATED COMMANDS

The `/review` skill works well with these other workflow commands:

| Command | When to Use | Relationship to /review |
|---------|-------------|------------------------|
| `/create_plan` | Before implementing a feature | Creates plans that /review validates against |
| `/implement_plan` | During implementation | Produces code that /review checks |
| `/validate_plan` | After implementation | Deeper validation than /review for plan compliance |
| `/local_review` | Reviewing someone else's PR | Set up environment, then run /review |
| `/debug` | When tests fail during review | Debug issues found by /review |
| `/create_handoff` | After /review identifies blockers | Hand off work if blocked |
| `/research_codebase` | Need more context for review | Deep dive before running /review |

## Recommended Workflow

```
/create_plan     →  Design the implementation
       ↓
/implement_plan  →  Execute the plan
       ↓
/review          →  Verify implementation (includes plan validation)
       ↓
  ┌─────┴─────┐
  │           │
PASS        FAIL
  │           │
  ↓           ↓
Push       /debug or fix issues
           then re-run /review
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sgpropertyanalytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
