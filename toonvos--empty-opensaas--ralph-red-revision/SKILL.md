---
name: ralph-red-revision
description: Execute test revision when GREEN phase identifies incorrect test assumptions. Re-runs RED steps 3-6 with strict criteria and QA documentation. Use via Skill tool when both wasp-backend-architect and test-quality-auditor confirm TEST_INCORRECT. Use when this capability is needed.
metadata:
  author: toonvos
---

# Ralph RED Revision Skill

Execute controlled test revision workflow when GREEN phase identifies fundamentally incorrect test assumptions. This skill re-runs RED steps 3-6 with additional gates for revision tracking and mandatory QA documentation.

## Critical Constraints

**Tests are NOT easily wrong.** They passed:

1. Architecture Review (wasp-backend-architect, Sonnet)
2. Quality Audit (test-quality-auditor, Opus 4.5)
3. 5 TDD criteria verification

This skill should ONLY be invoked when BOTH agents have confirmed `TEST_INCORRECT_CONFIRMED`.

## Invocation

```typescript
Skill(skill: "ralph-red-revision", args: "[fase-dir] category:[CATEGORY] assumption:'[SPECIFIC_ASSUMPTION]'")
```

**Examples:**

```typescript
Skill(skill: "ralph-red-revision", args: "fase-01 category:INCORRECT_BUSINESS_RULE assumption:'Test expects status ACTIVE on first edit, but rule is ACTIVE on all sections complete'")
Skill(skill: "ralph-red-revision", args: "fase-02 category:INCORRECT_TYPE_CONTRACT assumption:'Test expects comment.author.email but Prisma relation only has username'")
```

## Input Parsing

| Arg          | Format                      | Example                             |
| ------------ | --------------------------- | ----------------------------------- |
| `fase-dir`   | `fase-XX`                   | `fase-01`                           |
| `category`   | `category:` prefix          | `category:INCORRECT_BUSINESS_RULE`  |
| `assumption` | `assumption:` quoted string | `assumption:'Test expects X but Y'` |

## Valid Categories (ONLY these 4)

| Category                  | Description                                      |
| ------------------------- | ------------------------------------------------ |
| `INCORRECT_BUSINESS_RULE` | Requirements were misunderstood during RED phase |
| `INCORRECT_TYPE_CONTRACT` | Schema explored incorrectly during RED phase     |
| `INCORRECT_ERROR_CODE`    | Security/error pattern not understood            |
| `INCORRECT_SIDE_EFFECT`   | Business logic for side effects misinterpreted   |

## Output

- **Promise:** `<promise>REVISION_COMPLETE_RESUME_GREEN</promise>`
- **Artifact:** `[fase-dir]/gates.json` updated with revision tracking
- **QA Report:** `reports/qa/YYYY-MM-DD-qa-test-revision-[feature].md`
- **Progress:** `[fase-dir]/tests/REVISION-PROGRESS.md`
- **Archive:** `[fase-dir]/tests/original/` with original test files

---

## Gate Variables

```
GATE_REVISION_PREREQ_DONE       = false  # After Step 0
GATE_REVISION_ORIGINAL_PRESERVED = false  # After Step 1
GATE_REVISION_EXPLORE_DONE      = false  # After Step 2
GATE_REVISION_PLAN_DONE         = false  # After Step 3
GATE_REVISION_REVIEW_APPROVED   = false  # After Step 4
GATE_REVISION_TESTS_GENERATED   = false  # After Step 5
GATE_REVISION_AUDIT_PASSED      = false  # After Step 6
GATE_REVISION_VERIFIED          = false  # After Step 7
GATE_REVISION_DOCUMENTED        = false  # After Step 8
```

---

## Step-by-Step Execution

### Step 0: PREREQUISITES VALIDATION [GATE: GATE_REVISION_PREREQ_DONE]

**Read:** `[fase-dir]/gates.json`

**Verify:**

- GREEN phase blocked: `green.blocked_reason = "TEST_INCORRECT"`
- Revision count: `revision.count < revision.max_allowed` (max 2)
- Category is valid (one of 4)
- Assumption is specific (not vague)

**Execute:**

```bash
# Verify green-failures.md exists with 3+ attempts
cat [fase-dir]/implementation/green-failures.md | grep -c "Attempt"

# Read current revision count
cat [fase-dir]/gates.json | jq '.revision.count // 0'
```

**Validation:**

- ✅ GREEN phase blocked with TEST_INCORRECT
- ✅ Revision count < 2
- ✅ Category is valid
- ❌ If revision count >= 2 → STOP with `<promise>FEATURE_ESCALATED</promise>`

**Task Tracking:**

```typescript
// Update fase task to indicate REVISION phase
TaskUpdate(faseTaskId, {
  metadata: {
    ...existing,
    currentPhase: "REVISION",
    revisionCount: [N],
    revisionCategory: "[CATEGORY]",
    blockedReason: "TEST_INCORRECT - revision in progress",
  },
});
```

**After completion:**

- Update gates.json: Add revision entry with `GATE_REVISION_PREREQ_DONE = true`
- Initialize REVISION-PROGRESS.md
- TodoWrite: Mark Step 0 completed

**Write:** `[fase-dir]/tests/REVISION-PROGRESS.md`

```markdown
# Test Revision Progress - Fase [FASE_NR]: [FEATURE]

**Revision Number:** [COUNT + 1]
**Started:** [ISO_TIMESTAMP]
**Status:** IN_PROGRESS
**Category:** [CATEGORY]
**Assumption:** [SPECIFIC_ASSUMPTION]

## Step Completion Log

| Step | Description         | Status | Completed At | Notes                 |
| ---- | ------------------- | ------ | ------------ | --------------------- |
| 0    | Prerequisites       | ✅     | [TIMESTAMP]  | Revision [N] of max 2 |
| 1    | Archive Original    | ⏳     | -            | -                     |
| 2    | Re-Explore          | ⏳     | -            | -                     |
| 3    | Re-Plan             | ⏳     | -            | -                     |
| 4    | Architecture Review | ⏳     | -            | -                     |
| 5    | Generate Tests      | ⏳     | -            | -                     |
| 6    | Quality Audit       | ⏳     | -            | -                     |
| 7    | Verify RED          | ⏳     | -            | -                     |
| 8    | Create QA Report    | ⏳     | -            | -                     |
| 9    | Git Commit          | ⏳     | -            | -                     |
| 10   | Resume GREEN        | ⏳     | -            | -                     |

## Gates Status

- [ ] GATE_REVISION_PREREQ_DONE
- [ ] GATE_REVISION_ORIGINAL_PRESERVED
- [ ] GATE_REVISION_EXPLORE_DONE
- [ ] GATE_REVISION_PLAN_DONE
- [ ] GATE_REVISION_REVIEW_APPROVED
- [ ] GATE_REVISION_TESTS_GENERATED
- [ ] GATE_REVISION_AUDIT_PASSED
- [ ] GATE_REVISION_VERIFIED
- [ ] GATE_REVISION_DOCUMENTED
```

---

### Step 1: ARCHIVE ORIGINAL TESTS [GATE: GATE_REVISION_ORIGINAL_PRESERVED]

**Blocked until:** GATE_REVISION_PREREQ_DONE = true

**Execute:**

```bash
# Create archive directory
mkdir -p [fase-dir]/tests/original

# Copy original test files (preserve for audit trail)
cp app/src/server/[feature]/*.test.ts [fase-dir]/tests/original/

# Document original test locations
find app/src -name "*.test.ts" -newer [fase-dir]/gates.json > [fase-dir]/tests/original/file-list.txt
```

**After completion:**

- Update gates.json: `GATE_REVISION_ORIGINAL_PRESERVED = true`
- Update REVISION-PROGRESS.md: Step 1 → ✅
- TodoWrite: Mark Step 1 completed

---

### Step 2: RE-EXPLORE [GATE: GATE_REVISION_EXPLORE_DONE]

**Blocked until:** GATE_REVISION_ORIGINAL_PRESERVED = true

**Invoke:**

```
Task(
  subagent_type='Explore',
  model='haiku',
  thoroughness='very thorough',
  prompt="""
  Re-explore for [FEATURE] (Fase [FASE_NR]) - TEST REVISION:

  PREVIOUS INCORRECT ASSUMPTION:
  Category: [CATEGORY]
  Statement: "[SPECIFIC_ASSUMPTION]"

  FOCUS: Verify the CORRECT requirement by:
  1. Reading source documentation (PRD, specs)
  2. Checking similar operations for patterns
  3. Verifying schema relationships
  4. Confirming error code conventions
  5. Checking side effect patterns

  OUTPUT: Evidence that confirms the CORRECT behavior.
  """
)
```

**After completion:**

- Update gates.json: `GATE_REVISION_EXPLORE_DONE = true`
- Update REVISION-PROGRESS.md: Step 2 → ✅, note corrected requirement
- Write: `[fase-dir]/tests/revision-context.md` with findings
- TodoWrite: Mark Step 2 completed

---

### Step 3: RE-PLAN (Only Affected Tests) [GATE: GATE_REVISION_PLAN_DONE]

**Blocked until:** GATE_REVISION_EXPLORE_DONE = true

**Read:**

- `[fase-dir]/tests/test-plan.md` (original plan)
- `[fase-dir]/tests/revision-context.md` (corrected requirement)

**Invoke:**

```
Task(
  subagent_type='Plan',
  model='sonnet',
  prompt="""
  Create REVISED test plan for [FEATURE] (Fase [FASE_NR]):

  ORIGINAL PLAN: See test-plan.md

  INCORRECT ASSUMPTION:
  Category: [CATEGORY]
  Statement: "[SPECIFIC_ASSUMPTION]"

  CORRECTED REQUIREMENT:
  [From revision-context.md]

  RULES:
  1. ONLY revise tests affected by the incorrect assumption
  2. PRESERVE all correct tests unchanged
  3. Document what changed and why
  4. Tag revised tests with "REVISED: [date] - [reason]"

  OUTPUT: revised-test-plan.md with:
  - Tests to PRESERVE (unchanged)
  - Tests to REVISE (with before/after)
  - Justification for each change
  """
)
```

**After completion:**

- Update gates.json: `GATE_REVISION_PLAN_DONE = true`
- Update REVISION-PROGRESS.md: Step 3 → ✅
- Write: `[fase-dir]/tests/revised-test-plan.md`
- TodoWrite: Mark Step 3 completed

---

### Step 4: ARCHITECTURE REVIEW [GATE: GATE_REVISION_REVIEW_APPROVED]

**Blocked until:** GATE_REVISION_PLAN_DONE = true

**Invoke:**

```
Task(
  subagent_type='wasp-backend-architect',
  model='sonnet',
  prompt="""
  REVIEW revised test plan for [FEATURE] Fase [FASE_NR]:

  CONTEXT: This is a TEST REVISION, not initial planning.

  Original incorrect assumption: "[SPECIFIC_ASSUMPTION]"
  Category: [CATEGORY]

  📋 CHECKLIST (answer each explicitly):
  □ Does revision ONLY change affected tests?
  □ Are preserved tests truly unaffected?
  □ Does the corrected test match the actual requirement?
  □ Is the justification clear and valid?
  □ Does the revision NOT introduce new issues?
  □ Is test coverage maintained or improved?

  OUTPUT one of:
  - "APPROVED: Revision addresses incorrect assumption correctly."
  - "REJECTED: [specific issues]. Revise plan."
  """
)
```

**If REJECTED:**

- GATE_REVISION_REVIEW_APPROVED stays FALSE
- Return to Step 3 with architect feedback
- Max 2 iterations, then escalate

**If APPROVED:**

- Update gates.json: `GATE_REVISION_REVIEW_APPROVED = true`
- Update REVISION-PROGRESS.md: Step 4 → ✅, note "APPROVED"
- TodoWrite: Mark Step 4 completed

---

### Step 5: GENERATE REVISED TESTS [GATE: GATE_REVISION_TESTS_GENERATED]

**Blocked until:** GATE_REVISION_REVIEW_APPROVED = true

**Invoke:**

```
Task(
  subagent_type='wasp-test-automator',
  model='haiku',
  prompt="""
  Generate REVISED tests based on approved revision plan for [FEATURE] Fase [FASE_NR].

  RULES:
  - ONLY modify tests listed in revised-test-plan.md
  - PRESERVE all other tests UNCHANGED
  - Add comment to revised tests:
    // REVISED: [date] - [category]: [brief reason]
  - Follow Wasp test patterns
  - Use correct imports (wasp/entities, @prisma/client for enums)

  Output: Updated *.test.ts files
  """
)
```

**After completion:**

- Update gates.json: `GATE_REVISION_TESTS_GENERATED = true`
- Update REVISION-PROGRESS.md: Step 5 → ✅, note revised test count
- TodoWrite: Mark Step 5 completed

---

### Step 6: QUALITY AUDIT [GATE: GATE_REVISION_AUDIT_PASSED]

**Blocked until:** GATE_REVISION_TESTS_GENERATED = true

**Invoke:**

```
Task(
  subagent_type='test-quality-auditor',
  model='opus',
  prompt="""
  Audit REVISED tests for [FEATURE] Fase [FASE_NR]:

  CONTEXT: This is a TEST REVISION audit.

  Original incorrect assumption: "[SPECIFIC_ASSUMPTION]"
  Category: [CATEGORY]

  📋 5 TDD CRITERIA (all must pass for REVISED tests):
  □ 1. Tests business logic (NOT existence checks)
  □ 2. Meaningful assertions (NOT just .toBeDefined())
  □ 3. Tests error paths (401, 403, 404, 400)
  □ 4. Tests edge cases (empty, null, boundaries)
  □ 5. Tests behavior (NOT implementation details)

  ADDITIONAL CHECKS:
  □ Revision addresses the incorrect assumption
  □ Revision does NOT introduce test theater
  □ Preserved tests are truly unchanged

  OUTPUT one of:
  - "AUDIT PASSED: Revised tests meet all criteria."
  - "AUDIT FAILED: [specific issues]. Revise tests."
  """
)
```

**If FAILED:**

- GATE_REVISION_AUDIT_PASSED stays FALSE
- Return to Step 3 with audit feedback
- Max 2 iterations, then escalate

**If PASSED:**

- Update gates.json: `GATE_REVISION_AUDIT_PASSED = true`
- Update REVISION-PROGRESS.md: Step 6 → ✅, note "AUDIT PASSED"
- TodoWrite: Mark Step 6 completed

---

### Step 7: VERIFY TESTS RED [GATE: GATE_REVISION_VERIFIED]

**Execute:**

```bash
cd app && wasp test client run
```

**Expected:** All tests FAIL (RED state - no implementation exists)

**After completion:**

- Update gates.json: `GATE_REVISION_VERIFIED = true`
- Update REVISION-PROGRESS.md: Step 7 → ✅, note test count and RED status
- TodoWrite: Mark Step 7 completed

---

### Step 8: CREATE QA REPORT [GATE: GATE_REVISION_DOCUMENTED]

**Write:** `reports/qa/YYYY-MM-DD-qa-test-revision-[feature].md`

````markdown
# Test Revision QA Report: [FEATURE]

**Report Type:** QA - Test Revision
**Date:** [YYYY-MM-DD]
**Auditor:** ralph-red-revision + test-quality-auditor
**Fase:** [fase-dir]
**Revision Number:** [N] of max 2

---

## Executive Summary

**Verdict:** TEST_INCORRECT_CONFIRMED
**Category:** [CATEGORY]
**Agent Confirmations:**

- wasp-backend-architect (Sonnet): ✅ CONFIRMED
- test-quality-auditor (Opus): ✅ CONFIRMED

---

## Original Test Analysis

### Incorrect Assumption

**Statement:** "[SPECIFIC_ASSUMPTION]"
**Test File:** `app/src/[path]/[component].test.ts`
**Line Numbers:** [X-Y]

### Original Test Code

```typescript
// Original test (lines X-Y)
[exact test code that was incorrect]
```
````

### Expected vs Actual

| Aspect   | Test Expected | Actual Requirement |
| -------- | ------------- | ------------------ |
| [aspect] | [expected]    | [actual]           |

---

## Root Cause Analysis

### Why Was Test Incorrect?

1. **Source of Misunderstanding:** [Where did incorrect assumption come from?]
2. **RED Phase Gap:** [What was missed during Explore/Plan/Review?]
3. **Contributing Factors:** [Any process issues?]

### Why Wasn't This Caught Earlier?

| Gate                | What Was Checked | Why It Passed |
| ------------------- | ---------------- | ------------- |
| Architecture Review | [X]              | [Reason]      |
| Quality Audit       | [X]              | [Reason]      |
| 5 TDD Criteria      | [X]              | [Reason]      |

---

## Revision Details

### Revised Test Code

```typescript
// REVISED: [date] - [category]: [reason]
[exact revised test code]
```

### What Changed

| Change   | Before   | After   | Justification |
| -------- | -------- | ------- | ------------- |
| [change] | [before] | [after] | [why]         |

### Tests Preserved (Unchanged)

- [Test 1 - still correct]
- [Test 2 - still correct]

---

## Verification

### Quality Audit Results

| Criterion             | Status | Notes |
| --------------------- | ------ | ----- |
| Business Logic        | ✅     |       |
| Meaningful Assertions | ✅     |       |
| Error Paths           | ✅     |       |
| Edge Cases            | ✅     |       |
| Behavior Focus        | ✅     |       |

### Execution Verification

- [x] Revised tests execute without syntax errors
- [x] Revised tests FAIL for business logic reasons (RED)
- [x] Original correct tests unchanged

---

## Lessons Learned

### Process Improvement

**Recommendation:** [How to prevent this in future RED phases]

### Pattern to Document

[If this represents a new pattern to add to LESSONS-LEARNED.md]

---

## Document Metadata

**Document Version:** 1.0
**Generated:** [ISO_TIMESTAMP]
**Generator:** ralph-red-revision skill
**Revision Count:** [N]

---

**END OF REPORT**

````

**After completion:**

- Update gates.json: `GATE_REVISION_DOCUMENTED = true`
- Update REVISION-PROGRESS.md: Step 8 → ✅, note QA report path
- TodoWrite: Mark Step 8 completed

---

### Step 9: GIT COMMIT

**Execute:**

```bash
git add app/src/**/*.test.ts [fase-dir]/tests/ [fase-dir]/gates.json reports/qa/*test-revision*.md
git commit -m "test: Revise [FEATURE] tests - [CATEGORY]

Revision [N] of max 2. See QA report for details.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
````

**After completion:**

- Update REVISION-PROGRESS.md: Step 9 → ✅, note commit hash
- TodoWrite: Mark Step 9 completed

---

### Step 10: UPDATE GATES & RESUME GREEN

**Update `[fase-dir]/gates.json`:**

```json
{
  "fase": "[FASE_NR]",
  "feature": "[FEATURE]",
  "operations": ["op1", "op2"],
  "red": { ... },
  "green": {
    "GATE_GREEN_PREREQ_DONE": true,
    "GATE_GREEN_EXPLORE_DONE": true,
    "GATE_GREEN_MIGRATION_DONE": true,
    "GATE_GREEN_PLAN_DONE": true,
    "GATE_GREEN_CODE_DONE": true,
    "GATE_GREEN_TESTS_PASS": false,
    "GATE_GREEN_COVERAGE_OK": false,
    "blocked_at": null,
    "blocked_reason": null,
    "validation_attempts": 0
  },
  "revision": {
    "count": [N],
    "max_allowed": 2,
    "history": [
      {
        "revision_number": [N],
        "triggered_at": "[ISO_TIMESTAMP]",
        "category": "[CATEGORY]",
        "specific_assumption": "[ASSUMPTION]",
        "confirmed_by": {
          "architect": "wasp-backend-architect (Sonnet)",
          "auditor": "test-quality-auditor (Opus)"
        },
        "gates": {
          "GATE_REVISION_PREREQ_DONE": true,
          "GATE_REVISION_ORIGINAL_PRESERVED": true,
          "GATE_REVISION_EXPLORE_DONE": true,
          "GATE_REVISION_PLAN_DONE": true,
          "GATE_REVISION_REVIEW_APPROVED": true,
          "GATE_REVISION_TESTS_GENERATED": true,
          "GATE_REVISION_AUDIT_PASSED": true,
          "GATE_REVISION_VERIFIED": true,
          "GATE_REVISION_DOCUMENTED": true
        },
        "qa_report": "reports/qa/[YYYY-MM-DD]-qa-test-revision-[feature].md",
        "completed_at": "[ISO_TIMESTAMP]"
      }
    ]
  }
}
```

**Task Tracking:**

```typescript
// Update fase task to resume GREEN phase (unblock)
TaskUpdate(faseTaskId, {
  metadata: {
    ...existing,
    currentPhase: "GREEN", // Back to GREEN phase
    blockedReason: null, // Clear blocker
  },
});
```

**Output:**

```
TEST REVISION COMPLETE - Fase [FASE_NR]: [FEATURE]

✅ Category: [CATEGORY]
✅ Assumption corrected: "[SPECIFIC_ASSUMPTION]"
✅ wasp-backend-architect: APPROVED
✅ test-quality-auditor: PASSED
✅ All 9 revision gates: ✅✅✅✅✅✅✅✅✅
✅ QA Report: reports/qa/[YYYY-MM-DD]-qa-test-revision-[feature].md
✅ Committed: [hash]

GREEN phase gates reset. Resuming validation loop.

<promise>REVISION_COMPLETE_RESUME_GREEN</promise>
```

---

## Error Recovery

**If revision count >= 2:**

```
⚠️ MAXIMUM REVISIONS REACHED (2 of 2)

This feature has undergone 2 test revisions and still requires changes.
This indicates systemic issues requiring human intervention:

1. Requirements may be fundamentally unclear
2. Architecture may need redesign
3. Test/implementation approach may be flawed

ACTION REQUIRED:
→ Human must review entire feature from PRD level
→ Consider complete redesign before continuing

<promise>FEATURE_ESCALATED</promise>
```

**If architect REJECTS revision (max 2 times):**

```
⚠️ Architecture review rejected revision 2 times.
Issues: [list]
Escalating to feature review.

<promise>REVISION_BLOCKED</promise>
```

**If audit FAILS revision (max 2 times):**

```
⚠️ Quality audit failed revision 2 times.
Issues: [list]
Escalating to feature review.

<promise>REVISION_BLOCKED</promise>
```

---

## Agent Reference

| Step | Agent                  | Model  | Purpose                | Gate                          |
| ---- | ---------------------- | ------ | ---------------------- | ----------------------------- |
| 2    | Explore (built-in)     | haiku  | Re-explore correct req | GATE_REVISION_EXPLORE_DONE    |
| 3    | Plan (built-in)        | sonnet | Create revision plan   | GATE_REVISION_PLAN_DONE       |
| 4    | wasp-backend-architect | sonnet | Review revision plan   | GATE_REVISION_REVIEW_APPROVED |
| 5    | wasp-test-automator    | haiku  | Generate revised tests | GATE_REVISION_TESTS_GENERATED |
| 6    | test-quality-auditor   | opus   | Audit revised tests    | GATE_REVISION_AUDIT_PASSED    |

---

## Cross-Phase Integration

**Reads from GREEN phase:**

- `[fase-dir]/gates.json` - Blocked state with TEST_INCORRECT
- `[fase-dir]/implementation/green-failures.md` - Failure documentation
- `[fase-dir]/tests/test-plan.md` - Original test plan

**Creates for GREEN phase resume:**

- `[fase-dir]/tests/revised-test-plan.md` - Updated plan
- `[fase-dir]/gates.json` - Reset GREEN gates, add revision history
- `reports/qa/YYYY-MM-DD-qa-test-revision-[feature].md` - Audit trail
- Committed revised test files

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
