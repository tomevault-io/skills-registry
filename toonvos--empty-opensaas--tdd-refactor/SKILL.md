---
name: tdd-refactor
description: Execute REFACTOR phase of TDD for interactive development. Improve code quality while keeping tests green. MEDIUM+ threshold enforced with 80% resolution rate. Called by /refactor-tdd command. Use when this capability is needed.
metadata:
  author: toonvos
---

# TDD REFACTOR Phase Skill

Execute the complete REFACTOR phase (code simplification) for interactive TDD development workflow.

## Invocation

```typescript
Skill(skill: "tdd-refactor", args: "[sprintdag-path]")
```

**Examples:**

```typescript
Skill(skill: "tdd-refactor", args: "tasks/sprints/sprint-7/day-05")
Skill(skill: "tdd-refactor", args: "tasks/sprints/sprint-7/day-02")
```

## Input Parsing

| Arg         | Format                         | Example                         |
| ----------- | ------------------------------ | ------------------------------- |
| `sprintdag` | `tasks/sprints/sprint-X/day-Y` | `tasks/sprints/sprint-7/day-05` |

**Note:** Feature and operations are read from `[sprintdag]/gates.json`.

## Output

- **Promise:** `<promise>TDD_REFACTOR_COMPLETE</promise>`
- **Artifact:** `[sprintdag]/gates.json` updated with REFACTOR phase gates
- **Progress:** `[sprintdag]/refactor/PROGRESS.md` with step-by-step completion log
- **Refactored code:** Committed to git (tests stay GREEN)

---

## MEDIUM+ Threshold (CRITICAL)

**This phase enforces MEDIUM+ code smell resolution:**

- **HIGH** impact smells → MUST FIX
- **MEDIUM** impact smells → MUST FIX
- **LOW** impact smells → SKIP (document only)

**Gate `GATE_REFACTOR_MEDIUM_PLUS` requires:**

1. **Resolution rate ≥ 80%** of detected MEDIUM+ smells actually resolved
2. **OR** All remaining SKIPs approved via Step 5a validation

---

## SKIP Categories (STRICT) {#skip-categories}

**SKIP is NOT a free pass.** Only these categories are valid:

| Category           | When Allowed                             | Required Evidence          |
| ------------------ | ---------------------------------------- | -------------------------- |
| `BLOCKED_SCHEMA`   | Would require breaking schema change     | Migration impact analysis  |
| `BLOCKED_EXTERNAL` | Blocked by external dependency           | Dependency issue link      |
| `DEFERRED_TICKET`  | Effort > 4 hours, creates ticket         | **Ticket number required** |
| `TEST_RISK_HIGH`   | Would break 10+ tests with uncertain fix | Risk assessment document   |

**INVALID Skip Justifications (REJECTED):**

- ❌ "Low impact" - All MEDIUM+ have impact by definition
- ❌ "Single line" - DRY applies to all duplication
- ❌ "Not worth it" - Subjective, not allowed
- ❌ "Outside scope" - Scope was set in detection phase
- ❌ "Following previous day pattern" - Each day judged independently
- ❌ "Intentional pattern" - Must be validated by second agent

**CRITICAL: Precedent Chain Prevention**

```
⚠️ EACH SPRINTDAG IS JUDGED INDEPENDENTLY

"Following day-X pattern" is NOT a valid justification.
"Previous day did documentation only" is NOT a valid justification.
Evaluate each smell on its own merit for THIS sprintdag.
```

---

## Gate Variables

```
GATE_REFACTOR_PREREQ_DONE   = false  # After Step 0
GATE_REFACTOR_EXPLORE_DONE  = false  # After Step 1
GATE_REFACTOR_DETECT_DONE   = false  # After Step 2
GATE_REFACTOR_PLAN_DONE     = false  # After Step 3
GATE_REFACTOR_EXECUTE_DONE  = false  # After Step 5
GATE_REFACTOR_SKIP_APPROVED = false  # After Step 5a (if SKIPs exist)
GATE_REFACTOR_VALIDATED     = false  # After Step 6
GATE_REFACTOR_MEDIUM_PLUS   = false  # After 80% resolution OR all SKIPs approved
```

---

## Step-by-Step Execution

### Step 0: PREREQUISITES VALIDATION [GATE: GATE_REFACTOR_PREREQ_DONE]

**Task Tracking:**

```typescript
// Check if task already exists (from /refactor-tdd command)
TaskList(); // Find task with metadata.phase="REFACTOR" and matching sprintdag

// If no task exists, create one with dependency on GREEN
const greenTask = TaskList().find(
  (t) =>
    t.metadata?.phase === "GREEN" && t.metadata?.sprintdag === "[sprintdag]",
);

TaskCreate({
  subject: "REFACTOR: [feature]",
  description: "Improve code quality for [feature]",
  activeForm: "Executing REFACTOR phase",
  metadata: {
    type: "phase",
    phase: "REFACTOR",
    sprintdag: "[sprintdag]",
    feature: "[feature]",
    startedAt: "[ISO_TIMESTAMP]",
  },
});

// Set dependency and mark in_progress
TaskUpdate(taskId, { addBlockedBy: [greenTask.id], status: "in_progress" });
```

**Read:** `[sprintdag]/gates.json`

- Verify: `green.GATE_GREEN_TESTS_PASS = true`

**Execute:**

```bash
# Verify implementation exists
ls app/src/server/**/*operations.ts

# Verify tests are GREEN (passing)
cd app && wasp test client run 2>&1 | grep -E "(FAIL|PASS)"

# Verify GREEN phase commit exists
git log --oneline -10 | grep "feat:"
```

**Validation:**

- ✅ Implementation exists
- ✅ Tests are GREEN
- ✅ GREEN phase commit exists
- ❌ If any fail → `<promise>TDD_REFACTOR_BLOCKED</promise>`

**After completion:**

- Update gates.json: `refactor.GATE_REFACTOR_PREREQ_DONE = true`
- Initialize PROGRESS.md (see template below)

**Write:** `[sprintdag]/refactor/PROGRESS.md`

```markdown
# REFACTOR Phase Progress: [FEATURE]

**Sprintdag:** [SPRINTDAG_PATH]
**Started:** [ISO_TIMESTAMP]
**Status:** IN_PROGRESS

## Step Completion Log

| Step | Description           | Status | Completed At | Notes                         |
| ---- | --------------------- | ------ | ------------ | ----------------------------- |
| 0    | Prerequisites         | ✅     | [TIMESTAMP]  | GREEN phase verified complete |
| 1    | Explore Opportunities | ⏳     | -            | -                             |
| 2    | Code Smell Detection  | ⏳     | -            | -                             |
| 3    | Plan Refactorings     | ⏳     | -            | -                             |
| 4    | Baseline Metrics      | ⏳     | -            | -                             |
| 5    | Execute Refactorings  | ⏳     | -            | -                             |
| 5a   | Skip Validation       | ⏳     | -            | -                             |
| 6    | Validation Gate       | ⏳     | -            | -                             |
| 7    | Final Metrics         | ⏳     | -            | -                             |
| 8    | Write Artifacts       | ⏳     | -            | -                             |
| 9    | Git Commit            | ⏳     | -            | -                             |
| 10   | Output Promise        | ⏳     | -            | -                             |

## Gates Status

- [ ] GATE_REFACTOR_PREREQ_DONE
- [ ] GATE_REFACTOR_EXPLORE_DONE
- [ ] GATE_REFACTOR_DETECT_DONE
- [ ] GATE_REFACTOR_PLAN_DONE
- [ ] GATE_REFACTOR_EXECUTE_DONE
- [ ] GATE_REFACTOR_SKIP_APPROVED
- [ ] GATE_REFACTOR_VALIDATED
- [ ] GATE_REFACTOR_MEDIUM_PLUS
```

---

### Step 1: EXPLORE REFACTORING OPPORTUNITIES [GATE: GATE_REFACTOR_EXPLORE_DONE]

**Invoke parallel (2 agents):**

```
// Agent 1: Explore agent (structural analysis)
Task(
  subagent_type='Explore',
  model='haiku',
  prompt="""
  Analyze [FEATURE] implementation for refactoring:

  1. Find duplicate code patterns
  2. Identify long methods (>50 LOC)
  3. Check for magic numbers/strings
  4. Review complex conditionals
  5. Look for feature envy
  6. Check parameter lists (>4 params)
  """
)

// Agent 2: code-simplifier (clarity/consistency analysis)
Task(
  subagent_type='code-simplifier:code-simplifier',
  model='haiku',
  prompt="""
  Focus on recently modified files in [FEATURE]. Identify:

  CLARITY ISSUES:
  - Unclear variable/function naming
  - Complex logic that could be simplified
  - Missing or misleading comments

  CONSISTENCY ISSUES:
  - Mixed coding patterns within same file
  - Inconsistent error handling approaches
  - Style violations

  MAINTAINABILITY ISSUES:
  - Tight coupling between modules
  - Missing abstractions
  - Hard-coded dependencies

  Output: List of simplification opportunities with file:line references.
  """
)
```

**Combine results:** Merge findings from both agents into smell-candidates list for Step 2.

**After completion:**

- Update gates.json: `refactor.GATE_REFACTOR_EXPLORE_DONE = true`
- Update PROGRESS.md: Step 1 → ✅, note refactoring opportunities found (from both agents)

---

### Step 2: CODE SMELL DETECTION [GATE: GATE_REFACTOR_DETECT_DONE]

**Invoke:**

```
Task(
  subagent_type='wasp-code-reviewer',
  model='sonnet',
  prompt="""
  Identify code smells in [FEATURE]:

  CODE SMELLS TO DETECT:
  - Duplicated Code (same logic ≥2 times)
  - Long Method (>50 LOC)
  - Large Class (>300 LOC)
  - Long Parameter List (>4 parameters)
  - Magic Numbers/Strings (hardcoded values)
  - Complex Conditionals (nested if/switch)
  - Feature Envy (accessing other object's data)
  - Data Clumps (same variables passed together)

  PRIORITIZE:
  - HIGH impact, LOW effort → MUST DO
  - MEDIUM impact, any effort → MUST DO
  - LOW impact → SKIP (document)

  OUTPUT:
  - List of MEDIUM+ smells with file:line
  - Recommended refactoring per smell
  - Priority order
  - Effort estimate per smell (hours)
  """
)
```

**After completion:**

- Update gates.json: `refactor.GATE_REFACTOR_DETECT_DONE = true`
- Update gates.json: `refactor.metrics.detected_medium_plus = [count]`
- Update PROGRESS.md: Step 2 → ✅, note MEDIUM+ smell count
- Write `[sprintdag]/refactor/smell-report.md`

---

### Step 3: PLAN REFACTORING SEQUENCE [GATE: GATE_REFACTOR_PLAN_DONE]

**Invoke:**

```
Task(
  subagent_type='Plan',
  model='haiku',
  prompt="""
  Create refactoring execution plan for [FEATURE]:

  From code smell detection, prioritize:
  1. HIGH impact, LOW effort → First
  2. HIGH impact, HIGH effort → Second
  3. MEDIUM impact → Third
  4. LOW impact → Skip (document only)

  For each refactoring:
  - Name: e.g., "Extract validateSection helper"
  - File: app/src/server/.../operations.ts
  - Before: Current pattern
  - After: Target pattern
  - Risk: LOW/MEDIUM/HIGH
  - Effort: X hours
  - Test impact: Which tests verify this

  CRITICAL: If effort > 4 hours, must create ticket for DEFERRED_TICKET skip.

  OUTPUT: refactoring-plan.md with ordered execution steps
  """
)
```

**After completion:**

- Update gates.json: `refactor.GATE_REFACTOR_PLAN_DONE = true`
- Update PROGRESS.md: Step 3 → ✅, note refactoring count
- Write `[sprintdag]/refactor/refactoring-plan.md`

---

### Step 4: MEASURE BASELINE METRICS

**Execute:**

```bash
# Line count baseline
wc -l app/src/server/*/operations.ts

# Complexity estimation (function count)
grep -c "export function\|export const.*=" app/src/server/*/operations.ts

# Test coverage baseline
cd app && wasp test client run --coverage 2>&1 | grep -A5 "Coverage summary"
```

**Write:** `[sprintdag]/refactor/baseline-metrics.json`

**After completion:**

- Update PROGRESS.md: Step 4 → ✅, note LOC and coverage baseline

---

### Step 5: EXECUTE REFACTORINGS [GATE: GATE_REFACTOR_EXECUTE_DONE]

```
FOR EACH refactoring in plan (MEDIUM+ only):
  1. Apply ONE refactoring only

  2. Run tests immediately:
     → cd app && wasp test client run

     IF TESTS RED:
       → REVERT immediately: git checkout -- [changed files]
       → Document in refactor/revert-log.md with category TEST_RISK_HIGH
       → Mark as SKIP with category
       → Continue to next

     IF TESTS GREEN:
       → Check coverage: cd app && wasp test client run --coverage

       IF COVERAGE DECREASED:
         → REVERT immediately
         → Document reason
         → Continue to next

       IF COVERAGE MAINTAINED:
         → Commit: git commit -m "refactor: [specific change]"
         → Increment resolved_count
         → Continue to next
```

**Track metrics during execution:**

```json
{
  "detected_medium_plus": 6,
  "resolved": 0,
  "skipped": []
}
```

**After completion:**

- Update gates.json: `refactor.GATE_REFACTOR_EXECUTE_DONE = true`
- Update gates.json: `refactor.metrics.resolved = [count]`
- Update PROGRESS.md: Step 5 → ✅, note refactorings applied

---

### Step 5a: SKIP VALIDATION [GATE: GATE_REFACTOR_SKIP_APPROVED] (NEW)

**ONLY if any MEDIUM+ smells were marked as SKIP:**

**Validate each SKIP has valid category:**

```
FOR EACH skipped smell:
  IF category NOT IN [BLOCKED_SCHEMA, BLOCKED_EXTERNAL, DEFERRED_TICKET, TEST_RISK_HIGH]:
    → REJECT skip
    → Must execute refactoring or provide valid category

  IF category == DEFERRED_TICKET:
    → Verify ticket number exists
    → If no ticket → REJECT skip
```

**Invoke second agent review:**

```
Task(
  subagent_type='wasp-code-reviewer',
  model='sonnet',
  prompt="""
  REVIEW SKIP JUSTIFICATIONS for [FEATURE]:

  SKIPPED SMELLS:
  [list from Step 5]

  VALIDATION CHECKLIST (for each skip):
  □ Category is valid (BLOCKED_SCHEMA, BLOCKED_EXTERNAL, DEFERRED_TICKET, TEST_RISK_HIGH)
  □ Evidence provided matches category requirements
  □ Skip is NOT using invalid justification (low impact, single line, not worth it, etc.)
  □ Skip is NOT referencing "previous day pattern"

  CRITICAL: "Following previous day" or "documentation approach" are INVALID.
  Each sprintdag is judged independently.

  OUTPUT one of:
  - "ALL SKIPS APPROVED: [brief rationale per skip]"
  - "SKIPS REJECTED: [list of rejected skips with reason]. Must resolve or provide valid category."
  """
)
```

**If REJECTED:**

- Return to Step 5 to resolve rejected skips
- Max 2 iterations, then escalate to user

**If APPROVED (or no SKIPs):**

- Update gates.json: `refactor.GATE_REFACTOR_SKIP_APPROVED = true`
- Update PROGRESS.md: Step 5a → ✅, note skip review status

---

### Step 6: VALIDATION GATE [GATE: GATE_REFACTOR_VALIDATED]

**Execute:**

```bash
# Verify ALL tests still GREEN
cd app && wasp test client run

# Verify coverage not decreased
cd app && wasp test client run --coverage
```

**Calculate resolution rate:**

```
resolution_rate = resolved / detected_medium_plus * 100

IF resolution_rate >= 80%:
  → GATE_REFACTOR_MEDIUM_PLUS = true

ELSE IF resolution_rate < 80% AND all SKIPs approved:
  → GATE_REFACTOR_MEDIUM_PLUS = true (via approved exceptions)

ELSE:
  → GATE_REFACTOR_MEDIUM_PLUS = false
  → Cannot proceed to SECURITY phase
```

**After completion:**

- Update gates.json: `refactor.GATE_REFACTOR_VALIDATED = true`
- Update gates.json: `refactor.GATE_REFACTOR_MEDIUM_PLUS = true` (if criteria met)
- Update gates.json: `refactor.metrics.resolution_rate = "[X]%"`
- Update PROGRESS.md: Step 6 → ✅, note resolution rate

---

### Step 7: MEASURE FINAL METRICS

**Execute:**

```bash
# Line count final
wc -l app/src/server/*/operations.ts

# Test coverage final
cd app && wasp test client run --coverage
```

**Calculate delta:**

- LOC delta: baseline - final (positive = reduction)
- Coverage delta (should be 0 or positive)

**Write:** `[sprintdag]/refactor/final-metrics.json`

**After completion:**

- Update PROGRESS.md: Step 7 → ✅, note LOC delta and coverage

---

### Step 8: WRITE ARTIFACTS

**Write to `[sprintdag]/refactor/`:**

- `refactor-report.md` - All detected smells and resolutions
- `refactor-metrics.json` - Before/after metrics with resolution rate
- `complexity-analysis.md` - Complexity reduction
- `baseline-metrics.json` - Starting metrics
- `final-metrics.json` - Ending metrics
- `revert-log.md` - Reverted refactorings (if any)
- `skip-approvals.md` - Approved SKIPs with categories and evidence (if any)

**After completion:**

- Update PROGRESS.md: Step 8 → ✅, note artifacts written

---

### Step 9: GIT COMMIT (Final)

**Execute:**

```bash
git add [sprintdag]/refactor/ [sprintdag]/gates.json
git commit -m "refactor: Simplify [FEATURE]

Resolution rate: [X]% ([resolved]/[detected] MEDIUM+ smells)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

**After completion:**

- Update PROGRESS.md: Step 9 → ✅, note commit hash

---

### Step 10: UPDATE GATES & OUTPUT PROMISE

**Update `[sprintdag]/gates.json`:**

```json
{
  "refactor": {
    "GATE_REFACTOR_PREREQ_DONE": true,
    "GATE_REFACTOR_EXPLORE_DONE": true,
    "GATE_REFACTOR_DETECT_DONE": true,
    "GATE_REFACTOR_PLAN_DONE": true,
    "GATE_REFACTOR_EXECUTE_DONE": true,
    "GATE_REFACTOR_SKIP_APPROVED": true,
    "GATE_REFACTOR_VALIDATED": true,
    "GATE_REFACTOR_MEDIUM_PLUS": true,
    "started_at": "[ISO_TIMESTAMP]",
    "completed_at": "[ISO_TIMESTAMP]",
    "metrics": {
      "detected_medium_plus": 6,
      "resolved": 5,
      "skipped_approved": 1,
      "resolution_rate": "83%"
    },
    "skips": [
      {
        "smell_id": "H2",
        "category": "DEFERRED_TICKET",
        "ticket": "PROJ-123",
        "approved_by": "wasp-code-reviewer"
      }
    ]
  }
}
```

**Output:**

```
TDD REFACTOR PHASE COMPLETE: [FEATURE]

✅ Tests: All GREEN (maintained)
✅ Coverage: Maintained or improved
✅ LOC delta: -X lines (simplified)
✅ MEDIUM+ resolution: [resolved]/[detected] ([X]%)
✅ SKIPs approved: [count] (with valid categories)
✅ All 8 gates: ✅✅✅✅✅✅✅✅
✅ Artifacts: [sprintdag]/refactor/

<promise>TDD_REFACTOR_COMPLETE</promise>
```

**Task Tracking (on completion):**

```typescript
TaskUpdate(taskId, {
  status: "completed",
  metadata: {
    completedAt: "[ISO_TIMESTAMP]",
    locDelta: "[-X lines]",
    smellsResolved: "[Y MEDIUM+ smells]",
    smellsSkipped: "[Z LOW smells]",
    testsStillGreen: true,
    coverageMaintained: true,
    commitHash: "[hash]",
    gatesPath: "[sprintdag]/gates.json",
  },
});
```

**After completion:**

- Update PROGRESS.md: Step 10 → ✅, Status → COMPLETE
- Update PROGRESS.md Gates Status: All checkboxes → [x]

---

## Error Recovery

**If resolution rate < 80% AND SKIPs not approved:**

```
⚠️ TDD REFACTOR PHASE BLOCKED

Resolution rate: [X]% (below 80% threshold)
Detected: [N] MEDIUM+ smells
Resolved: [M]
Skipped (unapproved): [K]

REQUIRED ACTIONS:
1. Resolve more MEDIUM+ smells to reach 80%, OR
2. Provide valid SKIP categories for remaining items

Invalid skip justifications:
- "Low impact" ❌
- "Not worth it" ❌
- "Following previous day" ❌
- "Documentation only" ❌

Valid skip categories:
- BLOCKED_SCHEMA (with migration analysis)
- BLOCKED_EXTERNAL (with dependency link)
- DEFERRED_TICKET (with ticket number)
- TEST_RISK_HIGH (with risk assessment)

<promise>TDD_REFACTOR_BLOCKED</promise>
```

---

## Resume from Crash

**If skill was interrupted, check `[sprintdag]/gates.json`:**

1. Read gates.json to find last completed gate
2. Read PROGRESS.md to understand state
3. Resume from next incomplete step

---

## Agent Reference

| Step | Agent                  | Model  | Purpose                  | Gate                        |
| ---- | ---------------------- | ------ | ------------------------ | --------------------------- |
| 1    | Explore (built-in)     | haiku  | Find refactoring targets | GATE_REFACTOR_EXPLORE_DONE  |
| 2    | wasp-code-reviewer     | sonnet | Identify MEDIUM+ smells  | GATE_REFACTOR_DETECT_DONE   |
| 3    | Plan (built-in)        | haiku  | Sequence refactorings    | GATE_REFACTOR_PLAN_DONE     |
| 5    | wasp-refactor-executor | haiku  | Apply refactorings       | GATE_REFACTOR_EXECUTE_DONE  |
| 5a   | wasp-code-reviewer     | sonnet | Validate SKIP decisions  | GATE_REFACTOR_SKIP_APPROVED |

---

## Cross-Phase Integration

**Reads from tdd-green:**

- `[sprintdag]/gates.json` - Feature, GREEN phase status
- `[sprintdag]/implementation/implementation-notes.md` - Known issues
- `[sprintdag]/implementation/coverage-actual.json` - Coverage baseline

**Creates for tdd-security:**

- `[sprintdag]/refactor/refactor-report.md` - Final code state
- `[sprintdag]/gates.json` - REFACTOR phase completion with metrics

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
