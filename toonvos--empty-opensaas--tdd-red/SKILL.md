---
name: tdd-red
description: Execute RED phase of TDD for interactive development. Complete test specification & generation with gate tracking and artifact storage. Called by /red-tdd command. Use when this capability is needed.
metadata:
  author: toonvos
---

# TDD RED Phase Skill

Execute the complete RED phase (test specification & generation) for interactive TDD development workflow.

## Invocation

```typescript
Skill(skill: "tdd-red", args: "[sprintdag-path] '[feature]' ops:[operations]")
```

**Examples:**

```typescript
Skill(skill: "tdd-red", args: "tasks/sprints/sprint-7/day-05 'Priority filtering' ops:createTask,filterByPriority")
Skill(skill: "tdd-red", args: "tasks/sprints/sprint-7/day-02 'User preferences' ops:updatePreferences,getPreferences")
```

## Input Parsing

| Arg          | Format                         | Example                           |
| ------------ | ------------------------------ | --------------------------------- |
| `sprintdag`  | `tasks/sprints/sprint-X/day-Y` | `tasks/sprints/sprint-7/day-05`   |
| `feature`    | Quoted string                  | `'Priority filtering'`            |
| `operations` | `ops:` prefix, comma-separated | `ops:createTask,filterByPriority` |

## Output

- **Promise:** `<promise>TDD_RED_COMPLETE</promise>`
- **Artifact:** `[sprintdag]/gates.json` with RED phase gate status
- **Progress:** `[sprintdag]/tests/PROGRESS.md` with step-by-step completion log
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

1. Parse input args (sprintdag, feature, operations)
2. Validate sprintdag directory exists: `[sprintdag]/`
3. Validate README.md exists (feature doelstelling)
4. Create tests/ subdirectory if not exists
5. Initialize gates.json with all gates false
6. Initialize PROGRESS.md for step tracking
7. **Create or update phase task** (Task Tracking)

**Task Tracking:**

```typescript
// Check if task already exists (from /red-tdd command)
TaskList(); // Find task with metadata.phase="RED" and matching sprintdag

// If no task exists, create one
TaskCreate({
  subject: "RED: [feature]",
  description: "Write tests for [feature] - ops: [operations]",
  activeForm: "Writing RED phase tests",
  metadata: {
    type: "phase",
    phase: "RED",
    sprintdag: "[sprintdag]",
    feature: "[feature]",
    operations: "[operations]",
    startedAt: "[ISO_TIMESTAMP]",
  },
});

// Mark task in_progress
TaskUpdate(taskId, { status: "in_progress" });
```

**Execute:**

```bash
mkdir -p [sprintdag]/tests
```

**Write:** `[sprintdag]/gates.json`

```json
{
  "sprintdag": "[SPRINTDAG_PATH]",
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

**Write:** `[sprintdag]/tests/PROGRESS.md`

```markdown
# RED Phase Progress: [FEATURE]

**Sprintdag:** [SPRINTDAG_PATH]
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
  prompt="""
  Gather context for [FEATURE]:

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

---

### Step 2: PLAN [GATE: GATE_RED_PLAN_DONE]

**Blocked until:** GATE_RED_EXPLORE_DONE = true

**Invoke:**

```
Task(
  subagent_type='Plan',
  model='sonnet',
  prompt="""
  Create test strategy for [FEATURE]:

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
- Write: `[sprintdag]/tests/test-plan.md`

---

### Step 3: ARCHITECTURE REVIEW [GATE: GATE_RED_REVIEW_APPROVED] - CRITICAL

**Blocked until:** GATE_RED_PLAN_DONE = true

**Invoke:**

```
Task(
  subagent_type='wasp-backend-architect',
  model='sonnet',
  prompt="""
  REVIEW the test plan for [FEATURE].
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
- Max 3 iterations, then escalate to user

**If APPROVED:**

- Update gates.json: `GATE_RED_REVIEW_APPROVED = true`
- Update PROGRESS.md: Step 3 → ✅, note "APPROVED"

---

### Step 4: GENERATE TESTS [GATE: GATE_RED_TESTS_GENERATED]

**Blocked until:** GATE_RED_REVIEW_APPROVED = true

**Invoke:**

```
Task(
  subagent_type='wasp-test-automator',
  model='haiku',
  prompt="""
  Generate tests based on approved test plan for [FEATURE].

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

---

### Step 6: TEST QUALITY AUDIT [GATE: GATE_RED_AUDIT_PASSED] - CRITICAL

**Blocked until:** GATE_RED_TESTS_GENERATED = true

**Invoke:**

```
Task(
  subagent_type='test-quality-auditor',
  model='opus',
  prompt="""
  Audit generated tests for [FEATURE].

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
- Max 3 iterations, then escalate to user

**If PASSED:**

- Update gates.json: `GATE_RED_AUDIT_PASSED = true`
- Update PROGRESS.md: Step 6 → ✅, note "AUDIT PASSED"

---

### Step 7: VERIFY ALL TESTS RED

**Execute:**

```bash
cd app && wasp test client run
```

**Expected:** All tests FAIL (RED state - no implementation exists)

**After completion:**

- Update PROGRESS.md: Step 7 → ✅, note test count and RED status

---

### Step 8: WRITE ARTIFACTS

**Write to `[sprintdag]/tests/`:**

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

---

### Step 10: GIT COMMIT

**Execute:**

```bash
git add app/src/**/*.test.ts [sprintdag]/tests/ [sprintdag]/gates.json
git commit -m "test: Add [FEATURE] tests (RED)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

**After completion:**

- Update PROGRESS.md: Step 10 → ✅, note commit hash

---

### Step 11: UPDATE GATES & OUTPUT PROMISE

**Update `[sprintdag]/gates.json`:**

```json
{
  "sprintdag": "[SPRINTDAG_PATH]",
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

**Output:**

```
TDD RED PHASE COMPLETE: [FEATURE]

✅ Tests written: [X] files, [Y] test cases
✅ wasp-backend-architect: APPROVED
✅ test-quality-auditor: PASSED
✅ All 5 gates: ✅✅✅✅✅
✅ Committed: [hash]
✅ Artifacts: [sprintdag]/tests/

<promise>TDD_RED_COMPLETE</promise>
```

**Task Tracking (on completion):**

```typescript
TaskUpdate(taskId, {
  status: "completed",
  metadata: {
    completedAt: "[ISO_TIMESTAMP]",
    testFiles: "[X]",
    testCases: "[Y]",
    commitHash: "[hash]",
    architectApproved: true,
    auditPassed: true,
    gatesPath: "[sprintdag]/gates.json",
  },
});
```

**After completion:**

- Update PROGRESS.md: Step 11 → ✅, Status → COMPLETE
- Update PROGRESS.md Gates Status: All checkboxes → [x]

---

## Error Recovery

**If architect REJECTS (max 3 times):**

```
⚠️ Architecture review rejected 3 times.
Issues: [list]
Escalating to user for guidance.

<promise>TDD_RED_BLOCKED</promise>
```

**If audit FAILS (max 3 times):**

```
⚠️ Test quality audit failed 3 times.
Issues: [list]
Escalating to user for guidance.

<promise>TDD_RED_BLOCKED</promise>
```

---

## Resume from Crash

**If skill was interrupted, check `[sprintdag]/gates.json`:**

1. Read gates.json to find last completed gate
2. Read PROGRESS.md to understand state
3. Resume from next incomplete step

**Example:**

```
gates.json shows GATE_RED_PLAN_DONE = true, GATE_RED_REVIEW_APPROVED = false
→ Resume from Step 3 (Architecture Review)
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

**Creates for tdd-green:**

- `[sprintdag]/tests/test-plan.md` - Implementation guidance
- `[sprintdag]/tests/coverage-targets.json` - Coverage validation
- `[sprintdag]/gates.json` - Phase completion tracking
- Committed test files (prerequisite for GREEN)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
