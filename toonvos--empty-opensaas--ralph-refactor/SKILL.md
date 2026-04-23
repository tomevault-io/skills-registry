---
name: ralph-refactor
description: Execute REFACTOR phase of TDD for Ralph automation. Improve code quality while keeping tests green. MEDIUM+ threshold enforced with 80% resolution rate. Use via Skill tool in Ralph loops. Use when this capability is needed.
metadata:
  author: toonvos
---

# Ralph REFACTOR Phase Skill

Execute the complete REFACTOR phase (code simplification) as part of autonomous Ralph TDD workflow.

## Invocation

```typescript
Skill(skill: "ralph-refactor", args: "[fase-dir]")
```

**Examples:**

```typescript
Skill(skill: "ralph-refactor", args: "fase-01")
Skill(skill: "ralph-refactor", args: "fase-02")
```

## Input Parsing

| Arg        | Format    | Example   |
| ---------- | --------- | --------- |
| `fase-dir` | `fase-XX` | `fase-01` |

**Note:** Feature and operations are read from `[fase-dir]/gates.json` (created by ralph-red/green).

## Output

- **Promise:** `<promise>REFACTOR_PHASE_COMPLETE</promise>`
- **Artifact:** `[fase-dir]/gates.json` updated with REFACTOR phase gates
- **Progress:** `[fase-dir]/refactor/PROGRESS.md` with step-by-step completion log
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
- ❌ "Following previous fase pattern" - Each fase judged independently
- ❌ "Intentional pattern" - Must be validated by second agent

**CRITICAL: Precedent Chain Prevention**

```
⚠️ EACH FASE IS JUDGED INDEPENDENTLY

"Following Fase-X pattern" is NOT a valid justification.
"Previous fase did documentation only" is NOT a valid justification.
Evaluate each smell on its own merit for THIS fase.
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

**Read:** `[fase-dir]/gates.json`

- Verify: `green.GATE_GREEN_TESTS_PASS = true`

**Execute:**

```bash
# Verify implementation exists
ls app/src/server/**/*operations.ts

# Verify tests are GREEN (passing)
cd app && wasp test client run 2>&1 | grep -E "(FAIL|PASS)"

# Verify GREEN phase commit exists
git log --oneline -10 | grep "feat:.*Fase"
```

**Validation:**

- ✅ Implementation exists
- ✅ Tests are GREEN
- ✅ GREEN phase commit exists
- ❌ If any fail → `<promise>REFACTOR_PHASE_BLOCKED</promise>`

**After completion:**

- Update gates.json: `refactor.GATE_REFACTOR_PREREQ_DONE = true`
- Initialize PROGRESS.md (see template below)
- TodoWrite: Mark Step 0 completed

**Write:** `[fase-dir]/refactor/PROGRESS.md`

```markdown
# REFACTOR Phase Progress - Fase [FASE_NR]: [FEATURE]

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
  thoroughness='medium',
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
  Focus on recently modified files in [FEATURE] Fase [FASE_NR]. Identify:

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
- TodoWrite: Mark Step 1 completed

---

### Step 2: CODE SMELL DETECTION [GATE: GATE_REFACTOR_DETECT_DONE]

**Invoke:**

```
Task(
  subagent_type='wasp-code-reviewer',
  model='sonnet',
  prompt="""
  Identify code smells in [FEATURE] Fase [FASE_NR]:

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
- Write `[fase-dir]/refactor/smell-report.md`
- TodoWrite: Mark Step 2 completed

---

### Step 3: PLAN REFACTORING SEQUENCE [GATE: GATE_REFACTOR_PLAN_DONE]

**Invoke:**

```
Task(
  subagent_type='Plan',
  model='haiku',
  prompt="""
  Create refactoring execution plan for [FEATURE] Fase [FASE_NR]:

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
- Write `[fase-dir]/refactor/refactoring-plan.md`
- TodoWrite: Mark Step 3 completed

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

**Write:** `[fase-dir]/refactor/baseline-metrics.json`

**After completion:**

- Update PROGRESS.md: Step 4 → ✅, note LOC and coverage baseline
- TodoWrite: Mark Step 4 completed

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
- TodoWrite: Mark Step 5 completed

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
  REVIEW SKIP JUSTIFICATIONS for [FEATURE] Fase [FASE_NR]:

  SKIPPED SMELLS:
  [list from Step 5]

  VALIDATION CHECKLIST (for each skip):
  □ Category is valid (BLOCKED_SCHEMA, BLOCKED_EXTERNAL, DEFERRED_TICKET, TEST_RISK_HIGH)
  □ Evidence provided matches category requirements
  □ Skip is NOT using invalid justification (low impact, single line, not worth it, etc.)
  □ Skip is NOT referencing "previous fase pattern"

  CRITICAL: "Following previous fase" or "documentation approach" are INVALID.
  Each fase is judged independently.

  OUTPUT one of:
  - "ALL SKIPS APPROVED: [brief rationale per skip]"
  - "SKIPS REJECTED: [list of rejected skips with reason]. Must resolve or provide valid category."
  """
)
```

**If REJECTED:**

- Return to Step 5 to resolve rejected skips
- Max 2 iterations, then escalate

**If APPROVED (or no SKIPs):**

- Update gates.json: `refactor.GATE_REFACTOR_SKIP_APPROVED = true`
- Update PROGRESS.md: Step 5a → ✅, note skip review status
- TodoWrite: Mark Step 5a completed

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
- TodoWrite: Mark Step 6 completed

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

**Write:** `[fase-dir]/refactor/final-metrics.json`

**After completion:**

- Update PROGRESS.md: Step 7 → ✅, note LOC delta and coverage
- TodoWrite: Mark Step 7 completed

---

### Step 8: WRITE ARTIFACTS

**Write to `[fase-dir]/refactor/`:**

- `refactor-report.md` - All detected smells and resolutions
- `refactor-metrics.json` - Before/after metrics with resolution rate
- `complexity-analysis.md` - Complexity reduction
- `baseline-metrics.json` - Starting metrics
- `final-metrics.json` - Ending metrics
- `revert-log.md` - Reverted refactorings (if any)
- `skip-approvals.md` - Approved SKIPs with categories and evidence (if any)

**After completion:**

- Update PROGRESS.md: Step 8 → ✅, note artifacts written
- TodoWrite: Mark Step 8 completed

---

### Step 9: GIT COMMIT (Final)

**Execute:**

```bash
git add [fase-dir]/refactor/ [fase-dir]/gates.json
git commit -m "refactor: Simplify [FEATURE] - Fase [FASE_NR]

Resolution rate: [X]% ([resolved]/[detected] MEDIUM+ smells)

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

**Task Tracking:**

```typescript
// Update fase task to indicate transition to SECURITY phase
TaskUpdate(faseTaskId, {
  metadata: {
    ...existing,
    currentPhase: "SECURITY",
  },
});
```

**Output:**

```
REFACTOR PHASE COMPLETE - Fase [FASE_NR]: [FEATURE]

✅ Tests: All GREEN (maintained)
✅ Coverage: Maintained or improved
✅ LOC delta: -X lines (simplified)
✅ MEDIUM+ resolution: [resolved]/[detected] ([X]%)
✅ SKIPs approved: [count] (with valid categories)
✅ All 8 gates: ✅✅✅✅✅✅✅✅
✅ Artifacts: [fase-dir]/refactor/

<promise>REFACTOR_PHASE_COMPLETE</promise>
```

**After completion:**

- Update PROGRESS.md: Step 10 → ✅, Status → COMPLETE
- Update PROGRESS.md Gates Status: All checkboxes → [x]

---

### Step 11: AUTO-CONTINUE TO SECURITY PHASE (CRITICAL)

**ONLY if `<promise>REFACTOR_PHASE_COMPLETE</promise>` was output (NOT BLOCKED):**

IMMEDIATELY invoke the next phase without waiting for user input:

```
Skill(skill: "ralph-security", args: "[fase-dir]")
```

**If `<promise>REFACTOR_PHASE_BLOCKED</promise>` was output:**

- Do NOT auto-continue
- Wait for user to resolve the blocker
- User can manually resume with `/ralph-security [fase-dir]`

---

## Error Recovery

**If resolution rate < 80% AND SKIPs not approved:**

```
⚠️ REFACTOR PHASE BLOCKED

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
- "Following previous fase" ❌
- "Documentation only" ❌

Valid skip categories:
- BLOCKED_SCHEMA (with migration analysis)
- BLOCKED_EXTERNAL (with dependency link)
- DEFERRED_TICKET (with ticket number)
- TEST_RISK_HIGH (with risk assessment)

<promise>REFACTOR_PHASE_BLOCKED</promise>
```

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

**Reads from ralph-green:**

- `[fase-dir]/gates.json` - Feature, GREEN phase status
- `[fase-dir]/implementation/implementation-notes.md` - Known issues
- `[fase-dir]/implementation/coverage-actual.json` - Coverage baseline

**Creates for ralph-security:**

- `[fase-dir]/refactor/refactor-report.md` - Final code state
- `[fase-dir]/gates.json` - REFACTOR phase completion with metrics

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
