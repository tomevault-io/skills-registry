---
name: ralph-green
description: Execute GREEN phase of TDD for Ralph automation. Minimal implementation to make tests pass with gate tracking. Use via Skill tool in Ralph loops. Use when this capability is needed.
metadata:
  author: toonvos
---

# Ralph GREEN Phase Skill

Execute the complete GREEN phase (minimal implementation) as part of autonomous Ralph TDD workflow.

## Invocation

```typescript
Skill(skill: "ralph-green", args: "[fase-dir]")
```

**Examples:**

```typescript
Skill(skill: "ralph-green", args: "fase-01")
Skill(skill: "ralph-green", args: "fase-02")
```

## Input Parsing

| Arg        | Format    | Example   |
| ---------- | --------- | --------- |
| `fase-dir` | `fase-XX` | `fase-01` |

**Note:** Feature and operations are read from `[fase-dir]/gates.json` (created by ralph-red).

## Output

- **Promise:** `<promise>GREEN_PHASE_COMPLETE</promise>`
- **Artifact:** `[fase-dir]/gates.json` updated with GREEN phase gates
- **Progress:** `[fase-dir]/implementation/PROGRESS.md` with step-by-step completion log
- **Implementation files:** Committed to git

---

## Gate Variables

```
GATE_GREEN_PREREQ_DONE    = false  # After Step 0
GATE_GREEN_EXPLORE_DONE   = false  # After Step 1
GATE_GREEN_MIGRATION_DONE = false  # After Step 2 (or skipped)
GATE_GREEN_PLAN_DONE      = false  # After Step 3
GATE_GREEN_CODE_DONE      = false  # After Step 5
GATE_GREEN_TESTS_PASS     = false  # After Step 6 (all GREEN)
GATE_GREEN_COVERAGE_OK    = false  # After Step 7
```

---

## Step-by-Step Execution

### Step 0: PREREQUISITES VALIDATION [GATE: GATE_GREEN_PREREQ_DONE]

**Read:** `[fase-dir]/gates.json`

- Extract: feature, operations from RED phase
- Verify: `red.GATE_RED_AUDIT_PASSED = true`

**Execute:**

```bash
# Verify tests exist
ls app/src/server/**/*.test.ts

# Verify tests are RED (failing)
cd app && wasp test client run 2>&1 | grep -E "(FAIL|PASS)"

# Verify RED phase commit exists
git log --oneline -10 | grep "test:.*RED"

# Verify test-plan.md exists
cat [fase-dir]/tests/test-plan.md | head -20
```

**Validation:**

- ✅ gates.json exists with RED phase complete
- ✅ Tests exist and are RED
- ✅ RED phase commit exists
- ❌ If any fail → STOP with `<promise>GREEN_PHASE_BLOCKED</promise>`

**After completion:**

- Update gates.json: `green.GATE_GREEN_PREREQ_DONE = true`
- Initialize PROGRESS.md (see template below)
- TodoWrite: Mark Step 0 completed

**Write:** `[fase-dir]/implementation/PROGRESS.md`

```markdown
# GREEN Phase Progress - Fase [FASE_NR]: [FEATURE]

**Started:** [ISO_TIMESTAMP]
**Status:** IN_PROGRESS

## Step Completion Log

| Step | Description             | Status | Completed At | Notes                       |
| ---- | ----------------------- | ------ | ------------ | --------------------------- |
| 0    | Prerequisites           | ✅     | [TIMESTAMP]  | RED phase verified complete |
| 1    | Explore Schema          | ⏳     | -            | -                           |
| 2    | Schema Migration        | ⏳     | -            | -                           |
| 3    | Plan Implementation     | ⏳     | -            | -                           |
| 4    | Architecture Review     | ⏳     | -            | -                           |
| 5    | Generate Implementation | ⏳     | -            | -                           |
| 6    | Validation Loop         | ⏳     | -            | -                           |
| 7    | Coverage Validation     | ⏳     | -            | -                           |
| 8    | Write Artifacts         | ⏳     | -            | -                           |
| 9    | Git Commit              | ⏳     | -            | -                           |
| 10   | Output Promise          | ⏳     | -            | -                           |

## Gates Status

- [ ] GATE_GREEN_PREREQ_DONE
- [ ] GATE_GREEN_EXPLORE_DONE
- [ ] GATE_GREEN_MIGRATION_DONE
- [ ] GATE_GREEN_PLAN_DONE
- [ ] GATE_GREEN_CODE_DONE
- [ ] GATE_GREEN_TESTS_PASS
- [ ] GATE_GREEN_COVERAGE_OK
```

---

### Step 1: EXPLORE SCHEMA IMPACT [GATE: GATE_GREEN_EXPLORE_DONE]

**Blocked until:** GATE_GREEN_PREREQ_DONE = true

**Invoke:**

```
Task(
  subagent_type='Explore',
  model='haiku',
  thoroughness='medium',
  prompt="""
  Analyze schema requirements for [FEATURE] (Fase [FASE_NR]):

  Operations: [OPERATIONS]

  Check:
  1. Are new entities needed?
  2. Do existing entities need modification?
  3. What are the relationships between entities?
  4. Are enum values required?
  5. Any parallel worktree schema changes to coordinate?
  """
)
```

**After completion:**

- Update gates.json: `green.GATE_GREEN_EXPLORE_DONE = true`
- Update PROGRESS.md: Step 1 → ✅, note schema requirements
- TodoWrite: Mark Step 1 completed

---

### Step 2: SCHEMA MIGRATION [GATE: GATE_GREEN_MIGRATION_DONE]

**Blocked until:** GATE_GREEN_EXPLORE_DONE = true

**Invoke:**

```
Task(
  subagent_type='wasp-migration-helper',
  model='haiku',
  prompt="""
  Analyze and execute schema migration for [FEATURE] Fase [FASE_NR]:

  1. CHECK if schema changes required based on exploration
  2. IF changes needed:
     - Edit schema.prisma (add/modify models, fields, enums)
     - Run: wasp db migrate-dev --name "[feature]-schema"
     - MANDATORY restart: ./scripts/safe-start.sh
     - Verify types regenerated (check imports work)
  3. IF no changes needed:
     - Document "No schema changes required" in schema-coordination.md

  ALWAYS document in schema-coordination.md:
  - Schema changes made (or "none required")
  - Multi-worktree coordination notes (if applicable)
  - Types regenerated successfully: yes/no
  """
)
```

**After completion:**

- Update gates.json: `green.GATE_GREEN_MIGRATION_DONE = true`
- Update PROGRESS.md: Step 2 → ✅, note schema status
- Write `[fase-dir]/implementation/schema-coordination.md`
- TodoWrite: Mark Step 2 completed

---

### Step 3: PLAN IMPLEMENTATION [GATE: GATE_GREEN_PLAN_DONE]

**Blocked until:** GATE_GREEN_MIGRATION_DONE = true

**Read:** `[fase-dir]/tests/test-plan.md` for guidance

**Invoke:**

```
Task(
  subagent_type='Plan',
  model='haiku',
  prompt="""
  Plan minimal implementation for [FEATURE] Fase [FASE_NR]:

  Operations: [OPERATIONS]

  Based on test-plan.md, for each operation:
  1. Define function signature (args, return type)
  2. Add auth check: if (!context.user) throw HttpError(401)
  3. Add permission check (403)
  4. Add entity lookup (404)
  5. Add validation (400)
  6. Implement business logic (minimal to pass tests)

  Output: implementation-plan.md with code structure
  """
)
```

**After completion:**

- Update gates.json: `green.GATE_GREEN_PLAN_DONE = true`
- Update PROGRESS.md: Step 3 → ✅, note operation count
- Write `[fase-dir]/implementation/implementation-plan.md`
- TodoWrite: Mark Step 3 completed

---

### Step 4: ARCHITECTURE REVIEW [GATE: GATE_GREEN_REVIEW_DONE]

**Blocked until:** GATE_GREEN_PLAN_DONE = true

**Invoke:**

```
Task(
  subagent_type='wasp-backend-architect',
  model='sonnet',
  prompt="""
  REVIEW implementation plan for [FEATURE] Fase [FASE_NR]:

  📋 CHECKLIST:
  □ Error handling order correct? (Auth → Exists → Permission → Validate)
  □ Multi-tenant isolation enforced?
  □ Wasp operation patterns followed?
  □ Performance implications acceptable?
  □ Type safety maintained?

  OUTPUT: "APPROVED" or "CONCERNS: [issues]"
  """
)
```

**After completion:**

- Update PROGRESS.md: Step 4 → ✅, note approval status
- TodoWrite: Mark Step 4 completed

---

### Step 5: GENERATE IMPLEMENTATION [GATE: GATE_GREEN_CODE_DONE]

**Blocked until:** GATE_GREEN_PLAN_DONE = true

**Invoke:**

```
Task(
  subagent_type='wasp-code-generator',
  model='haiku',
  prompt="""
  Generate MINIMAL implementation for [FEATURE] Fase [FASE_NR]:

  Operations: [OPERATIONS]

  RULES:
  - Only implement what tests require
  - Follow existing patterns in codebase
  - Use correct imports (wasp/entities, @prisma/client for enums)
  - Add operations to main.wasp
  - No premature optimization
  - Error handling order: Auth → Exists → Permission → Validate

  Output: Implementation files (operations.ts, main.wasp updates)
  """
)
```

**After completion:**

- Update gates.json: `green.GATE_GREEN_CODE_DONE = true`
- Update PROGRESS.md: Step 5 → ✅, note files created
- TodoWrite: Mark Step 5 completed

---

### Step 6: VALIDATION LOOP [GATE: GATE_GREEN_TESTS_PASS]

**Blocked until:** GATE_GREEN_CODE_DONE = true

```
FOR attempt = 1 to 3:
  → Run tests: cd app && wasp test client run

  IF ALL TESTS GREEN:
    ✅ GATE_GREEN_TESTS_PASS = true
    → Proceed to Step 7
    → BREAK loop

  IF TESTS RED:
    → Analyze failure pattern:
      * LOGIC: Fix business logic
      * TYPE: Fix type annotations
      * SCHEMA: Re-run migration
      * IMPORT: Fix import paths
      * AUTH: Add auth checks
    → Generate targeted fix (not blind retry)
    → Document in implementation/green-failures.md
    → Continue to next attempt

IF attempt > 3 AND STILL RED:
  → Invoke TEST_INCORRECT analysis (see Step 6a below)
```

### Step 6a: TEST_INCORRECT ANALYSIS (After 3 Failed Attempts)

**Purpose:** Determine if the issue is an implementation problem or a fundamentally incorrect test.

**Step 6a.1: First Agent Analysis (wasp-backend-architect)**

```
Task(
  subagent_type='wasp-backend-architect',
  model='sonnet',
  prompt="""
  Analyze GREEN phase failure for [FEATURE] Fase [FASE_NR]:

  CONTEXT:
  - Implementation attempts: 3 (all failed)
  - Failures documented in: implementation/green-failures.md
  - Test plan: tests/test-plan.md

  DETERMINE by analyzing the evidence:
  1. Is the implementation approach correct per business requirements?
  2. Does the test contain an INCORRECT ASSUMPTION about requirements?

  VALID INCORRECT ASSUMPTION CATEGORIES (only these 4):
  - INCORRECT_BUSINESS_RULE: Test assumes wrong business logic
  - INCORRECT_TYPE_CONTRACT: Test assumes wrong schema/type relationship
  - INCORRECT_ERROR_CODE: Test assumes wrong error code for scenario
  - INCORRECT_SIDE_EFFECT: Test assumes wrong side effect trigger

  OUTPUT one of:
  - "IMPLEMENTATION_ISSUE: [specific fix needed]"
  - "TEST_INCORRECT_CONFIRMED:
     Category: [one of 4 valid categories]
     Specific assumption: [exact statement of what test assumes incorrectly]
     Evidence: [test line reference, expected vs actual behavior]"
  """
)
```

**If IMPLEMENTATION_ISSUE:**

```
→ Extend validation loop by 2 additional attempts
→ Apply suggested fix
→ Continue to attempt 4-5
→ If still failing after 5 attempts:
  → Output: <promise>GREEN_PHASE_BLOCKED</promise>
```

**If TEST_INCORRECT_CONFIRMED:**

```
→ Proceed to Step 6a.2 (Second Agent Confirmation)
```

---

**Step 6a.2: Second Agent Confirmation (test-quality-auditor)**

```
Task(
  subagent_type='test-quality-auditor',
  model='opus',
  prompt="""
  SECOND OPINION on TEST_INCORRECT claim for [FEATURE] Fase [FASE_NR]:

  FIRST AGENT (wasp-backend-architect) CLAIMED:
  Category: [CATEGORY from Step 6a.1]
  Assumption: "[SPECIFIC_ASSUMPTION from Step 6a.1]"
  Evidence: [EVIDENCE from Step 6a.1]

  YOUR TASK:
  1. Independently verify if the test assumption is actually incorrect
  2. Check if this could still be an implementation issue
  3. Verify the category is valid (one of 4)
  4. Confirm the assumption statement is specific (not vague)

  CRITICAL: Tests passed Architecture Review + Quality Audit in RED phase.
  A TEST_INCORRECT claim means those reviews missed something.
  Be rigorous in your verification.

  OUTPUT one of:
  - "CONFIRMED: Test assumption is incorrect. Category [X] is valid.
     [Your independent reasoning why]"
  - "REJECTED: This is an implementation issue, not a test issue.
     Reason: [specific reason]
     Suggested fix: [implementation fix]"
  """
)
```

**If REJECTED (agents disagree):**

```
→ Update gates.json: green.blocked_reason = "AGENTS_DISAGREE"
→ Document disagreement in implementation/green-failures.md
→ Output: <promise>GREEN_PHASE_BLOCKED</promise>
```

**If CONFIRMED (both agents agree):**

```
→ Update gates.json:
  - green.blocked_reason = "TEST_INCORRECT"
  - green.blocked_at = [ISO_TIMESTAMP]
  - green.validation_attempts = [attempt count]

→ Invoke ralph-red-revision:
  Skill(
    skill: "ralph-red-revision",
    args: "[fase-dir] category:[CATEGORY] assumption:'[SPECIFIC_ASSUMPTION]'"
  )

→ After revision completes (<promise>REVISION_COMPLETE_RESUME_GREEN</promise>):
  - Reset validation_attempts to 0
  - Resume validation loop from attempt 1
```

---

**Decision Tree Summary:**

```
3 attempts failed
      ↓
wasp-backend-architect analysis
      ↓
┌─────────────────────────────────────────┐
│ IMPLEMENTATION_ISSUE                    │
│ → +2 attempts (total 5)                 │
│ → If still failing → GREEN_PHASE_BLOCKED│
└─────────────────────────────────────────┘
      ↓ (if TEST_INCORRECT_CONFIRMED)
test-quality-auditor second opinion
      ↓
┌─────────────────────────────────────────┐
│ REJECTED (agents disagree)              │
│ → GREEN_PHASE_BLOCKED                   │
└─────────────────────────────────────────┘
      ↓ (if CONFIRMED)
┌─────────────────────────────────────────┐
│ Both agents agree                       │
│ → Invoke ralph-red-revision             │
│ → Resume GREEN after revision           │
└─────────────────────────────────────────┘
```

**After completion:**

- Update gates.json: `green.GATE_GREEN_TESTS_PASS = true`
- Update PROGRESS.md: Step 6 → ✅, note test count and attempt count
- TodoWrite: Mark Step 6 completed

---

### Step 7: COVERAGE VALIDATION [GATE: GATE_GREEN_COVERAGE_OK]

**Blocked until:** GATE_GREEN_TESTS_PASS = true

**Execute:**

```bash
cd app && wasp test client run --coverage
```

**Read:** `[fase-dir]/tests/coverage-targets.json`

**Compare:**

- Statements: actual ≥ 80%
- Branches: actual ≥ 75%

**If coverage below target:**

- Document in `implementation/coverage-gaps.md`
- Continue (don't block GREEN phase)

**After completion:**

- Update gates.json: `green.GATE_GREEN_COVERAGE_OK = true`
- Update PROGRESS.md: Step 7 → ✅, note coverage percentages
- Write `[fase-dir]/implementation/coverage-actual.json`
- TodoWrite: Mark Step 7 completed

---

### Step 8: WRITE ARTIFACTS

**Write to `[fase-dir]/implementation/`:**

- `implementation-notes.md` - Implementation decisions
- `coverage-actual.json` - Actual coverage achieved
- `green-failures.md` - Validation loop failures (if any)
- `schema-coordination.md` - Schema changes

**After completion:**

- Update PROGRESS.md: Step 8 → ✅, note artifacts written
- TodoWrite: Mark Step 8 completed

---

### Step 9: GIT COMMIT

**Execute:**

```bash
git add app/src/**/*.ts app/main.wasp app/schema.prisma [fase-dir]/implementation/ [fase-dir]/gates.json
git commit -m "feat: Implement [FEATURE] - Fase [FASE_NR]

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

**After completion:**

- Update PROGRESS.md: Step 9 → ✅, note commit hash
- TodoWrite: Mark Step 9 completed

---

### Step 10: UPDATE GATES & OUTPUT PROMISE

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
    "GATE_GREEN_TESTS_PASS": true,
    "GATE_GREEN_COVERAGE_OK": true,
    "started_at": "[ISO_TIMESTAMP]",
    "completed_at": "[ISO_TIMESTAMP]"
  }
}
```

**Task Tracking:**

```typescript
// Update fase task to indicate transition to REFACTOR phase
TaskUpdate(faseTaskId, {
  metadata: {
    ...existing,
    currentPhase: "REFACTOR",
  },
});
```

**Output:**

```
GREEN PHASE COMPLETE - Fase [FASE_NR]: [FEATURE]

✅ Tests: X/X GREEN
✅ Coverage: Y% statements, Z% branches
✅ All 7 gates: ✅✅✅✅✅✅✅
✅ Committed: [hash]
✅ Artifacts: [fase-dir]/implementation/

<promise>GREEN_PHASE_COMPLETE</promise>
```

**After completion:**

- Update PROGRESS.md: Step 10 → ✅, Status → COMPLETE
- Update PROGRESS.md Gates Status: All checkboxes → [x]

---

### Step 11: AUTO-CONTINUE TO REFACTOR PHASE (CRITICAL)

**ONLY if `<promise>GREEN_PHASE_COMPLETE</promise>` was output (NOT BLOCKED):**

IMMEDIATELY invoke the next phase without waiting for user input:

```
Skill(skill: "ralph-refactor", args: "[fase-dir]")
```

**If `<promise>GREEN_PHASE_BLOCKED</promise>` was output:**

- Do NOT auto-continue
- Wait for user to resolve the blocker
- User can manually resume with `/ralph-refactor [fase-dir]`

---

## Error Recovery

**If prerequisites fail:**

```
⚠️ Prerequisites validation failed.
Missing: [list]
RED phase must be completed first.

<promise>GREEN_PHASE_BLOCKED</promise>
```

**If tests still RED after 3 attempts:**

```
⚠️ Tests still failing after 3 attempts.
See implementation/green-failures.md
Escalating to human review.

<promise>GREEN_PHASE_BLOCKED</promise>
```

---

## Agent Reference

| Step | Agent                  | Model  | Purpose                 | Gate                      |
| ---- | ---------------------- | ------ | ----------------------- | ------------------------- |
| 1    | Explore (built-in)     | haiku  | Analyze schema impact   | GATE_GREEN_EXPLORE_DONE   |
| 2    | wasp-migration-helper  | haiku  | Schema migration        | GATE_GREEN_MIGRATION_DONE |
| 3    | Plan (built-in)        | haiku  | Implementation strategy | GATE_GREEN_PLAN_DONE      |
| 4    | wasp-backend-architect | sonnet | Architecture (optional) | —                         |
| 5    | wasp-code-generator    | haiku  | Generate implementation | GATE_GREEN_CODE_DONE      |

---

## Cross-Phase Integration

**Reads from ralph-red:**

- `[fase-dir]/gates.json` - Feature, operations, RED phase status
- `[fase-dir]/tests/test-plan.md` - Implementation guidance
- `[fase-dir]/tests/coverage-targets.json` - Coverage validation

**Creates for ralph-refactor:**

- `[fase-dir]/implementation/implementation-notes.md` - Refactoring guidance
- `[fase-dir]/implementation/coverage-actual.json` - Coverage baseline
- `[fase-dir]/gates.json` - GREEN phase completion

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
