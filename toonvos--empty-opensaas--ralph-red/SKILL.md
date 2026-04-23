---
name: ralph-red
description: Execute RED phase of TDD for Ralph automation. Complete test specification & generation with gate tracking and artifact storage. Use via Skill tool in Ralph loops. Use when this capability is needed.
metadata:
  author: toonvos
---

# Ralph RED Phase Skill

Execute the complete RED phase (test specification & generation) as part of autonomous Ralph TDD workflow.

## Invocation

```typescript
Skill(skill: "ralph-red", args: "[fase-dir] [feature] ops:[operations]")
```

**Examples:**

```typescript
Skill(skill: "ralph-red", args: "fase-01 'Foundation Schema' ops:createConversation,listConversations")
Skill(skill: "ralph-red", args: "fase-02 'Tool Validation' ops:validateToolSection,generateToolText")
```

## Input Parsing

| Arg          | Format                         | Example                                    |
| ------------ | ------------------------------ | ------------------------------------------ |
| `fase-dir`   | `fase-XX`                      | `fase-01`                                  |
| `feature`    | Quoted string                  | `'Foundation Schema'`                      |
| `operations` | `ops:` prefix, comma-separated | `ops:createConversation,listConversations` |

## Output

- **Promise:** `<promise>RED_PHASE_COMPLETE</promise>`
- **Artifact:** `[fase-dir]/gates.json` with RED phase gate status
- **Progress:** `[fase-dir]/tests/PROGRESS.md` with step-by-step completion log
- **Test files:** Committed to git

---

## Gate Variables

```
GATE_RED_EXPLORE_DONE     = false  # After Step 1
GATE_RED_PLAN_DONE        = false  # After Step 2
GATE_RED_REVIEW_APPROVED  = false  # After Step 3 APPROVES
GATE_RED_TESTS_GENERATED  = false  # After Step 4
GATE_RED_AUDIT_PASSED     = false  # After Step 6 PASSES
```

---

## Step-by-Step Execution

### Step 0: SETUP

1. Parse input args (fase-dir, feature, operations)
2. Validate fase directory exists: `[sprint-dir]/[fase-dir]/`
3. Create tests/ subdirectory if not exists
4. Initialize gates.json with all gates false
5. Initialize PROGRESS.md for step tracking
6. **Update fase task status** (Task Tracking)

**Execute:**

```bash
mkdir -p [fase-dir]/tests
```

**Task Tracking:**

```typescript
// Find fase task by gatesPath
TaskList(); // → find task with metadata.gatesPath matching [fase-dir]/gates.json

TaskUpdate(faseTaskId, {
  status: "in_progress",
  metadata: {
    ...existing,
    currentPhase: "RED",
    startedAt: "[ISO_TIMESTAMP]",
  },
});
```

**Write:** `[fase-dir]/gates.json`

```json
{
  "fase": "[FASE_NR]",
  "feature": "[FEATURE]",
  "operations": ["op1", "op2"],
  "red": {
    "GATE_RED_EXPLORE_DONE": false,
    "GATE_RED_PLAN_DONE": false,
    "GATE_RED_REVIEW_APPROVED": false,
    "GATE_RED_TESTS_GENERATED": false,
    "GATE_RED_AUDIT_PASSED": false,
    "started_at": "[ISO_TIMESTAMP]"
  }
}
```

**Write:** `[fase-dir]/tests/PROGRESS.md`

```markdown
# RED Phase Progress - Fase [FASE_NR]: [FEATURE]

**Started:** [ISO_TIMESTAMP]
**Status:** IN_PROGRESS

## Step Completion Log

| Step | Description         | Status | Completed At | Notes                                  |
| ---- | ------------------- | ------ | ------------ | -------------------------------------- |
| 0    | Setup               | ✅     | [TIMESTAMP]  | Initialized gates.json and PROGRESS.md |
| 1    | Explore             | ⏳     | -            | -                                      |
| 2    | Plan                | ⏳     | -            | -                                      |
| 3    | Architecture Review | ⏳     | -            | -                                      |
| 4    | Generate Tests      | ⏳     | -            | -                                      |
| 5    | Verify Execution    | ⏳     | -            | -                                      |
| 6    | Quality Audit       | ⏳     | -            | -                                      |
| 7    | Verify Tests RED    | ⏳     | -            | -                                      |
| 8    | Write Artifacts     | ⏳     | -            | -                                      |
| 9    | Pre-Commit Check    | ⏳     | -            | -                                      |
| 10   | Git Commit          | ⏳     | -            | -                                      |
| 11   | Output Promise      | ⏳     | -            | -                                      |

## Gates Status

- [ ] GATE_RED_EXPLORE_DONE
- [ ] GATE_RED_PLAN_DONE
- [ ] GATE_RED_REVIEW_APPROVED
- [ ] GATE_RED_TESTS_GENERATED
- [ ] GATE_RED_AUDIT_PASSED
```

---

### Step 1: EXPLORE [GATE: GATE_RED_EXPLORE_DONE]

**Invoke:**

```
Task(
  subagent_type='Explore',
  model='haiku',
  thoroughness='very thorough',
  prompt="""
  Gather context for [FEATURE] (Fase [FASE_NR]):

  Operations to implement: [OPERATIONS]

  Search for:
  1. Similar operations in src/server/*/operations.ts
  2. Existing test patterns in src/server/**/*.test.ts
  3. Relevant models in schema.prisma
  4. Permission patterns in src/permissions/
  5. Validation patterns (Zod schemas)
  6. Error handling patterns

  Output: Context summary with file paths and patterns found.
  """
)
```

**After completion:**

- Update gates.json: `GATE_RED_EXPLORE_DONE = true`
- Update PROGRESS.md: Step 1 → ✅, add context files found
- TodoWrite: Mark Step 1 completed

---

### Step 2: PLAN [GATE: GATE_RED_PLAN_DONE]

**Blocked until:** GATE_RED_EXPLORE_DONE = true

**Invoke:**

```
Task(
  subagent_type='Plan',
  model='sonnet',
  prompt="""
  Create test strategy for [FEATURE] (Fase [FASE_NR]):

  Operations to test: [OPERATIONS]

  For EACH operation, plan tests for:
  1. Happy path (authenticated user, valid input)
  2. Auth failure (401 - not authenticated)
  3. Permission failure (403 - wrong role/org)
  4. Validation failure (400 - invalid input)
  5. Not found (404 - entity doesn't exist)
  6. Edge cases (empty, null, boundaries)

  Output:
  - Test scenarios list with expected outcomes
  - Mock strategy (unit vs integration)
  - Coverage targets (≥80% statements, ≥75% branches)
  - Test file locations
  """
)
```

**After completion:**

- Update gates.json: `GATE_RED_PLAN_DONE = true`
- Update PROGRESS.md: Step 2 → ✅, add test scenario count
- Write: `[fase-dir]/tests/test-plan.md`
- TodoWrite: Mark Step 2 completed

---

### Step 3: ARCHITECTURE REVIEW [GATE: GATE_RED_REVIEW_APPROVED] - CRITICAL

**Blocked until:** GATE_RED_PLAN_DONE = true

**Invoke:**

```
Task(
  subagent_type='wasp-backend-architect',
  model='sonnet',
  prompt="""
  REVIEW the test plan for [FEATURE] Fase [FASE_NR].
  This is VALIDATION, not planning.

  📋 CHECKLIST (answer each explicitly):
  □ Are ALL auth scenarios covered? (401, 403)
  □ Are ALL validation scenarios covered? (400)
  □ Are ALL edge cases identified? (empty, null, boundaries)
  □ Is the mock strategy appropriate?
  □ Is the test pattern (unit vs integration) correct?
  □ Are there MISSING scenarios?

  OUTPUT one of:
  - "APPROVED: All 6 items verified. No gaps."
  - "REJECTED: [specific issues]. Return to PLAN phase."
  """
)
```

**If REJECTED:**

- GATE_RED_REVIEW_APPROVED stays FALSE
- Return to Step 2 with architect feedback
- Max 3 iterations, then escalate

**If APPROVED:**

- Update gates.json: `GATE_RED_REVIEW_APPROVED = true`
- Update PROGRESS.md: Step 3 → ✅, note "APPROVED"
- TodoWrite: Mark Step 3 completed

---

### Step 4: GENERATE TESTS [GATE: GATE_RED_TESTS_GENERATED]

**Blocked until:** GATE_RED_REVIEW_APPROVED = true

**Invoke:**

```
Task(
  subagent_type='wasp-test-automator',
  model='haiku',
  prompt="""
  Generate tests based on approved test plan for [FEATURE] Fase [FASE_NR].

  Operations: [OPERATIONS]

  REQUIREMENTS:
  - Follow Wasp test patterns from existing tests
  - Use correct imports (wasp/entities, @prisma/client for enums)
  - Include mock factories with all required Prisma fields
  - Test behavior, not implementation
  - Use expect.rejects for error tests (NOT try-catch)
  - Self-validate all imports resolve

  Output: *.test.ts files next to source files
  """
)
```

**After completion:**

- Update gates.json: `GATE_RED_TESTS_GENERATED = true`
- Update PROGRESS.md: Step 4 → ✅, add test file count
- TodoWrite: Mark Step 4 completed

---

### Step 5: VERIFY EXECUTION

**Execute:**

```bash
cd app && wasp test client run 2>&1 | head -50
```

**Verify:**

- Tests EXECUTE (no timeouts)
- Tests FAIL with clear error (expected - no implementation yet)
- No import errors

**After completion:**

- Update PROGRESS.md: Step 5 → ✅, note execution status
- TodoWrite: Mark Step 5 completed

---

### Step 6: TEST QUALITY AUDIT [GATE: GATE_RED_AUDIT_PASSED] - CRITICAL

**Blocked until:** GATE_RED_TESTS_GENERATED = true

**Invoke:**

```
Task(
  subagent_type='test-quality-auditor',
  model='opus',
  prompt="""
  Audit generated tests for [FEATURE] Fase [FASE_NR].

  📋 5 TDD CRITERIA (all must pass):
  □ 1. Tests business logic (NOT existence checks)
  □ 2. Meaningful assertions (NOT just .toBeDefined())
  □ 3. Tests error paths (401, 403, 404, 400)
  □ 4. Tests edge cases (empty, null, boundaries)
  □ 5. Tests behavior (NOT implementation details)

  DETECT test theater:
  - Side effect checks without behavior verification
  - Mocks that aren't used
  - Tests that pass even if logic is wrong

  OUTPUT one of:
  - "AUDIT PASSED: All 5 TDD criteria verified. Quality: GOOD."
  - "AUDIT FAILED: [specific issues]. Return to PLAN phase."
  """
)
```

**If FAILED:**

- GATE_RED_AUDIT_PASSED stays FALSE
- Return to Step 2 with audit feedback
- Max 3 iterations, then escalate

**If PASSED:**

- Update gates.json: `GATE_RED_AUDIT_PASSED = true`
- Update PROGRESS.md: Step 6 → ✅, note "AUDIT PASSED"
- TodoWrite: Mark Step 6 completed

---

### Step 7: VERIFY ALL TESTS RED

**Execute:**

```bash
cd app && wasp test client run
```

**Expected:** All tests FAIL (RED state - no implementation exists)

**After completion:**

- Update PROGRESS.md: Step 7 → ✅, note test count and RED status
- TodoWrite: Mark Step 7 completed

---

### Step 8: WRITE ARTIFACTS

**Write to `[fase-dir]/tests/`:**

- `test-plan.md` - Approved test plan
- `coverage-targets.json`:

```json
{
  "statements": 80,
  "branches": 75,
  "functions": 80,
  "lines": 80
}
```

**After completion:**

- Update PROGRESS.md: Step 8 → ✅, note artifacts written
- TodoWrite: Mark Step 8 completed

---

### Step 9: PRE-COMMIT GATE CHECK

```
┌─────────────────────────────────────────────────────────┐
│ RED PHASE PRE-COMMIT CHECK                              │
├─────────────────────────────────────────────────────────┤
│ □ GATE_RED_EXPLORE_DONE     = true                      │
│ □ GATE_RED_PLAN_DONE        = true                      │
│ □ GATE_RED_REVIEW_APPROVED  = true ← wasp-backend-architect│
│ □ GATE_RED_TESTS_GENERATED  = true                      │
│ □ GATE_RED_AUDIT_PASSED     = true ← test-quality-auditor │
├─────────────────────────────────────────────────────────┤
│ 🚫 IF ANY FALSE → STOP → COMPLETE MISSING STEP         │
│ ✅ ALL TRUE → PROCEED TO COMMIT                         │
└─────────────────────────────────────────────────────────┘
```

**After completion:**

- Update PROGRESS.md: Step 9 → ✅, note all gates verified
- TodoWrite: Mark Step 9 completed

---

### Step 10: GIT COMMIT

**Execute:**

```bash
git add app/src/**/*.test.ts [fase-dir]/tests/ [fase-dir]/gates.json
git commit -m "test: Add [FEATURE] tests (RED) - Fase [FASE_NR]

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

**After completion:**

- Update PROGRESS.md: Step 10 → ✅, note commit hash
- TodoWrite: Mark Step 10 completed

---

### Step 11: UPDATE GATES & OUTPUT PROMISE

**Update `[fase-dir]/gates.json`:**

```json
{
  "fase": "[FASE_NR]",
  "feature": "[FEATURE]",
  "operations": ["op1", "op2"],
  "red": {
    "GATE_RED_EXPLORE_DONE": true,
    "GATE_RED_PLAN_DONE": true,
    "GATE_RED_REVIEW_APPROVED": true,
    "GATE_RED_TESTS_GENERATED": true,
    "GATE_RED_AUDIT_PASSED": true,
    "started_at": "[ISO_TIMESTAMP]",
    "completed_at": "[ISO_TIMESTAMP]"
  }
}
```

**Task Tracking:**

```typescript
// Update fase task to indicate transition to GREEN phase
TaskUpdate(faseTaskId, {
  metadata: {
    ...existing,
    currentPhase: "GREEN",
  },
});
```

**Output:**

```
RED PHASE COMPLETE - Fase [FASE_NR]: [FEATURE]

✅ Tests written: [X] files, [Y] test cases
✅ wasp-backend-architect: APPROVED
✅ test-quality-auditor: PASSED
✅ All 5 gates: ✅✅✅✅✅
✅ Committed: [hash]
✅ Artifacts: [fase-dir]/tests/

<promise>RED_PHASE_COMPLETE</promise>
```

**After completion:**

- Update PROGRESS.md: Step 11 → ✅, Status → COMPLETE
- Update PROGRESS.md Gates Status: All checkboxes → [x]

---

### Step 12: AUTO-CONTINUE TO GREEN PHASE (CRITICAL)

**ONLY if `<promise>RED_PHASE_COMPLETE</promise>` was output (NOT BLOCKED):**

IMMEDIATELY invoke the next phase without waiting for user input:

```
Skill(skill: "ralph-green", args: "[fase-dir]")
```

**If `<promise>RED_PHASE_BLOCKED</promise>` was output:**

- Do NOT auto-continue
- Wait for user to resolve the blocker
- User can manually resume with `/ralph-green [fase-dir]`

---

## Error Recovery

**If architect REJECTS (max 3 times):**

```
⚠️ Architecture review rejected 3 times.
Issues: [list]
Escalating to human review.

<promise>RED_PHASE_BLOCKED</promise>
```

**If audit FAILS (max 3 times):**

```
⚠️ Test quality audit failed 3 times.
Issues: [list]
Escalating to human review.

<promise>RED_PHASE_BLOCKED</promise>
```

---

## Agent Reference

| Step | Agent                  | Model  | Purpose                 | Gate                        |
| ---- | ---------------------- | ------ | ----------------------- | --------------------------- |
| 1    | Explore (built-in)     | haiku  | Gather codebase context | GATE_RED_EXPLORE_DONE       |
| 2    | Plan (built-in)        | sonnet | Create test strategy    | GATE_RED_PLAN_DONE          |
| 3    | wasp-backend-architect | sonnet | Review & approve plan   | GATE_RED_REVIEW_APPROVED ⚠️ |
| 4    | wasp-test-automator    | haiku  | Generate test files     | GATE_RED_TESTS_GENERATED    |
| 6    | test-quality-auditor   | opus   | Verify 5 TDD criteria   | GATE_RED_AUDIT_PASSED ⚠️    |

---

## Cross-Phase Integration

**Creates for ralph-green:**

- `[fase-dir]/tests/test-plan.md` - Implementation guidance
- `[fase-dir]/tests/coverage-targets.json` - Coverage validation
- `[fase-dir]/gates.json` - Phase completion tracking
- Committed test files (prerequisite for GREEN)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
