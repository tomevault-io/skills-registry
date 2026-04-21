---
name: skill-check
description: Skill quality analysis with honest, tiered pass/fail checklists. No fake precision. Use when this capability is needed.
metadata:
  author: demithras
---

# SKILL-CHECKING — Skill Quality Analysis

Honest skill quality assessment using tiered pass/fail checklists. No fake precision, no pseudo-quantitative scores.

> **Philosophy**: A checklist that says "passes these, fails those" is more honest than a score claiming "82.1%" precision for subjective judgments.

---

## Commands

| Command | Purpose |
|---------|---------|
| `/skill-check <skill>` | Full analysis → smoke → prompt for additional phases |
| `/skill-check <skill> --compare=<other>` | Compare two skills side-by-side |
| `/skill-check smoke <skill>` | Smoke test only (no analysis) |
| `/skill-check <skill> --manual` | Skip prompt, run manual validation directly |
| `/skill-check <skill> --recommend` | Skip prompt, run test recommendations directly |
| `/skill-check <skill> --manual --recommend` | Skip prompt, run both phases |

---

## Unified Flow

The default `/skill-check <skill>` command runs the full interactive flow:

```
1. ANALYZE — Run all checklist checks
2. SMOKE — Verify skill loads and invokes without errors
3. PROMPT — AskUserQuestion (multiselect):
   "What additional phases do you want to run?"
   [ ] Generate test coverage recommendations
   [ ] Run manual validation
   (User can select both, one, or neither)
4. Execute selected phases
```

**Flags as shortcuts**: When you specify `--recommend` or `--manual`, the prompt is skipped — the flag implies "yes" for that phase.

| Invocation | Behavior |
|------------|----------|
| `/skill-check skill` | Analyze → Smoke → **Prompt** → Run selected |
| `/skill-check skill --recommend` | Analyze → Smoke → Recommendations (no prompt) |
| `/skill-check skill --manual` | Analyze → Smoke → Manual validation (no prompt) |
| `/skill-check skill --manual --recommend` | Analyze → Smoke → Both (no prompt) |

---

## CLEAR META Mapping

> **CLEAR META** = Each phase executed in its *most effective form* for skill quality analysis.

| CLEAR Phase | skill-check Implementation | Why This Is META |
|-------------|---------------------------|------------------|
| **C** (Clarity) | ANALYZE — Tiered checklist with `[auto]/[manual]/[proxy]` labels | Most effective: Separates verifiable from judgment-based checks. No fake precision. |
| **L** (Legitimacy) | L-labels on every check | Most effective: Makes epistemic status explicit — you know what's automated vs requires human judgment. |
| **E** (Exit) | Pass/fail thresholds by lifecycle (Draft 40%, Active 80%, Production 100%) | Most effective: Context-sensitive criteria. A draft skill has different needs than production. |
| **A** (Action) | SMOKE, MANUAL, RECOMMEND — Three action modes | Most effective: Right tool for right situation. Quick smoke for basic check, manual for validation, recommend for growth. |
| **R** (Review) | History recording + `/retro` handoff | Most effective: Captures learnings in SKILL.md history; suggests `/retro` for significant changes. |

### The META Principle Applied

Each phase answers: *"What's the most effective way to check skill quality at this stage?"*

- **C-META**: Don't just list checks — categorize them by verifiability
- **L-META**: Don't just pass/fail — show *confidence* in each result
- **E-META**: Don't use fixed thresholds — adapt to lifecycle status
- **A-META**: Don't force one workflow — offer appropriate action for context
- **R-META**: Don't just output results — persist learnings for future sessions

---

## 1. ANALYZE — Quality Checklist

> **Note**: Consider the skill's lifecycle status and domain when interpreting results. A draft exploration skill has different needs than a production automation skill.

### L-Labels (Epistemic Clarity)

Each check is labeled to distinguish what can be automated from what requires judgment:

| Label | Meaning |
|-------|---------|
| `[auto]` | Verifiable programmatically |
| `[manual]` | Requires human judgment |
| `[proxy]` | Indirect measure of something harder to assess |

**Syntax**: `/skill-check <skill-name-or-path> [--verbose]`

### Tiered Requirements

Skills have different requirements based on lifecycle status:

| Status | Required Checks | Recommended Checks |
|--------|:---------------:|:------------------:|
| **Draft** | 40% pass | — |
| **Active** | 80% pass | All encouraged |
| **Production** | 100% required | All must pass |

### Checklist Categories

#### Structure Checks
- [ ] `[auto]` `SKILL.md` exists
- [ ] `[auto]` Valid YAML frontmatter
- [ ] `[auto]` Has `name` field
- [ ] `[auto]` Has `version` field (semver format)
- [ ] `[auto]` Has `description` field
- [ ] `[auto]` Has `allowed-tools` field

#### Documentation Checks
- [ ] `[proxy]` First paragraph explains purpose *(proxies for: skill is understandable)*
- [ ] `[auto]` Command syntax documented
- [ ] `[proxy]` At least one usage example *(proxies for: skill is learnable)*

#### Quality Checks
- [ ] `[auto]` Has tests.json
- [ ] `[auto]` No TODO markers
- [ ] `[auto]` No wildcard permissions
- [ ] `[manual]` Allowed tools match actual usage

#### Integration Checks
- [ ] `[manual]` Dependencies documented if any
- [ ] `[auto]` Has unique trigger (no conflicts)
- [ ] `[manual]` Human validated (via `--manual` or external tracking)
- [ ] `[manual]` Scope is appropriate (not >10 commands)

---

### Output Format

```
Skill Analysis: my-skill
Status: active (requires 80% pass)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Structure (5/6)
  ✓ SKILL.md exists
  ✓ Valid YAML frontmatter
  ✓ Has name field
  ✓ Has version field
  ✓ Has description field
  ✗ Missing allowed-tools field

Documentation (2/3)
  ✓ First paragraph explains purpose
  ✓ Command syntax documented
  ✗ No usage examples found

Quality (3/3)
  ✓ ...

Integration (2/4)
  ...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Result: 12/16 passed (75%)
Status: FAIL — Active skills require 80%

Top Priority Fixes:
1. Add allowed-tools to frontmatter
2. Add usage example section

💡 Strategic fit unclear? Run: /meta "Is <skill> solving the right problem?"
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 2. COMPARE — Side-by-Side Analysis

**Syntax**: `/skill-check <skill-a> --compare=<skill-b>`

Runs checklist on both skills and shows comparison:

```
Comparison: skill-a vs skill-b
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

                    skill-a    skill-b
Structure           5/6        6/6
Documentation       3/3        2/3
Quality             4/4        4/4
Integration         2/4        3/4
────────────────────────────────────────
Total               14/17      15/17

Winner: skill-b (slightly)

Key Differences:
- skill-a missing: allowed-tools
- skill-b missing: usage examples
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 3. SMOKE — Real Invocation Test

**Syntax**: `/skill-check smoke <skill-name> [--scenario=<name>]`

Unlike simulated tests, smoke tests actually invoke the skill and verify:

1. **Skill loads** — Can be parsed and activated
2. **Basic invocation works** — Produces non-error output
3. **Output is reasonable** — Contains expected elements

### Process

```
1. Load skill from path
2. Invoke with minimal trigger: /<skill-name>
3. Check response:
   - Not an error message
   - Contains skill-relevant content
   - Completes within timeout (30s)
4. Report pass/fail with details
```

### Smoke Test Output

```
Smoke Test: my-skill
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Invocation: /my-skill
Status: PASS

Checks:
  ✓ Skill loaded successfully
  ✓ Produced output (247 chars)
  ✓ No error messages detected
  ✓ Completed in 1.2s

Note: Smoke tests verify basic functionality only.
      They do not replace comprehensive testing.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Failure Output

```
Smoke Test: broken-skill
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Invocation: /broken-skill
Status: FAIL

Checks:
  ✓ Skill loaded successfully
  ✗ Error in output: "TypeError: Cannot read property..."
  ✗ No meaningful content produced

Debug Info:
  Full output saved to: /tmp/smoke-test-broken-skill.log
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## 4. MANUAL — Interactive Validation

**Syntax**: `/skill-check <skill> --manual` (or select via prompt after smoke)

Interactively validate `[manual]` checks by actually invoking the skill with a suggested scenario.

### Flow

```
1. (If --manual flag used, skip to step 2)
2. Agent analyzes skill and suggests ONE optimal test scenario
3. Agent generates pre-invocation rubric (pass/fail criteria)
4. Agent invokes skill via /<skill-name> with suggested scenario
5. Agent evaluates output against rubric
6. Record result to SKILL.md history
```

> **Note**: When using the unified flow (`/skill-check <skill>` without flags), you'll be prompted via checkbox whether to run this phase.

### Scenario Selection Algorithm

The agent picks the optimal scenario by analyzing:

| Factor | Weight | What to Look For |
|--------|--------|------------------|
| **Coverage** | 40% | Tests the skill's primary documented capability |
| **Complexity** | 30% | Exercises multiple features in one invocation |
| **Edge Cases** | 20% | Tests a non-trivial or boundary condition |
| **Speed** | 10% | Can complete within reasonable time |

**Heuristic**: Pick the scenario that best answers: *"If this works, I'm confident the skill does what it claims."*

### Pre-Invocation Rubric

Before invoking, the agent MUST define explicit pass/fail criteria:

```
Rubric for: /meta "Should I use Redis or Memcached?"
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Pass Criteria (all must be true):
  [ ] Output follows CLEAR META format (C-L-E-A-R phases present)
  [ ] Contains L-labeled claims (fact/model/hypothesis/etc.)
  [ ] Provides actionable recommendation
  [ ] No errors or hallucinated tool calls
  [ ] Completes within 60 seconds

Fail Triggers (any triggers failure):
  [ ] Missing required sections
  [ ] Generic advice without structured analysis
  [ ] Error messages in output
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Evaluation Output

```
Manual Validation: meta
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Scenario: /meta "Should I use Redis or Memcached for session storage?"
Rationale: Tests core decision-analysis capability with concrete trade-offs

Rubric Check:
  ✓ CLEAR META format present
  ✓ L-labeled claims found (3 facts, 2 hypotheses)
  ✓ Actionable recommendation provided
  ✓ No errors detected
  ✓ Completed in 12s

Result: PASS (5/5 criteria met)

Recording to SKILL.md history...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### History Recording Format

On pass, append to target skill's SKILL.md `history` section:

```yaml
  - version: "2.6.0"
    date: "2026-01-22"
    changes: "..."
    manual_validated: "2026-01-22 - scenario: 'Redis vs Memcached decision' - PASS (5/5)"
```

On fail, record with details:

```yaml
    manual_validated: "2026-01-22 - scenario: 'Redis vs Memcached' - FAIL (3/5: missing L-labels, no recommendation)"
```

### When to Use

| Situation | Recommendation |
|-----------|----------------|
| After making changes to a skill | Run `--manual` to verify behavior |
| Before promoting draft → active | Required validation |
| Routine quality check | Auto-checks sufficient |
| Investigating a reported issue | Use `--manual` with specific scenario |

---

## 5. RECOMMEND — Test Coverage Recommendations

**Syntax**: `/skill-check <skill> --recommend` (or select via prompt after smoke)

Analyze the skill's `tests.json` and suggest additional test cases that would increase coverage.

### Flow

```
1. (If --recommend flag used, skip to step 2)
2. Load skill's tests.json
3. Analyze current assertion types used
4. Compare against full assertion vocabulary
5. Generate prioritized recommendations
6. Show lifecycle impact (what's needed for next tier)
```

> **Note**: When using the unified flow (`/skill-check <skill>` without flags), you'll be prompted via checkbox whether to run this phase.

### Assertion Vocabulary

The following assertion types are available for `tests.json`:

| Category | Assertion Type | Description |
|----------|----------------|-------------|
| **Structure** | `file_exists` | Check file presence |
| | `dir_exists` | Check directory presence |
| | `frontmatter_valid` | Validate YAML frontmatter |
| | `has_field` | Required field exists |
| | `field_equals` | Field has exact value |
| | `field_not_matches` | Field doesn't match pattern |
| **Content** | `content_contains` | Substring present |
| | `content_not_contains` | Substring absent |
| **Version** | `version_semver` | Valid semver format |
| | `version_gte` | Minimum version check |
| **Tools** | `has_tool` | Tool in allowed-tools |
| | `excludes_tool` | Tool NOT in allowed-tools |
| **Triggers** | `trigger_matches` | Command pattern validation |
| **Lifecycle** | `status_is` | Lifecycle status check |
| | `not_deprecated` | Not superseded |
| **Behavioral** | `input/expected` | Input produces expected output |

### Recommendation Output

```
Test Coverage Recommendations: my-skill
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Current Coverage: 4/16 assertion types (25%)
Assertions Used: file_exists, frontmatter_valid, has_field, content_contains

### High Priority (recommended first)

1. ❌ **Behavioral Tests** — Test actual skill behavior
   Fix: Safe — Add input/expected test case
   ```json
   {
     "id": "primary-function",
     "input": "/my-skill basic-input",
     "expected": { "output_contains": ["expected-keyword"] }
   }
   ```
   Why: Your SKILL.md documents capabilities but tests don't verify them

2. ❌ **Negative Tests** — Verify anti-patterns absent
   Fix: Safe — Add content_not_contains assertions
   ```json
   { "type": "content_not_contains", "text": "TODO" }
   { "type": "field_not_matches", "field": "allowed-tools", "pattern": "\\*" }
   ```
   Why: Catches common quality issues

### Medium Priority

3. ⚠️ **Tool Assertions** — Verify declared tools
   Fix: Judgment — Review which tools need explicit verification
   Your allowed-tools: [Read, Glob, Bash]
   Add: `has_tool` assertions for each, `excludes_tool` for sensitive tools

4. ⚠️ **Version Constraints** — For stable skills
   Fix: Safe — Add version_gte assertion
   ```json
   { "type": "version_gte", "value": "1.0.0" }
   ```

### Low Priority (Quick Wins)

5. 💡 You have 3 examples → Fix: Enhancement — Add `content_contains` for each
6. 💡 You list 5 limitations → Fix: Enhancement — Add `content_contains` for "## Limitations"
7. 💡 You have scripts/ dir → Fix: Safe — Add `dir_exists` assertion

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Lifecycle Impact:
  Current: Draft (4/16 = 25%)
  To reach Active (80%): Add 9 more assertions
  To reach Production (100%): Add 12 more assertions

Skill-forge handoff:
  → /skill-farm improve my-skill "add behavioral tests"
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Recommendation Prioritization

| Priority | Category | Fix Confidence | Rationale |
|----------|----------|----------------|-----------|
| **High** | Behavioral tests | Safe | Verifies skill actually works, not just exists |
| **High** | Negative tests | Safe | Catches anti-patterns and quality issues |
| **Medium** | Tool assertions | Judgment | Documents permissions; review which tools matter |
| **Medium** | Version constraints | Safe | Important once skill is stable |
| **Low** | Content assertions | Enhancement | Easy wins but less valuable |
| **Low** | Structure assertions | Enhancement | Usually already covered |

### Fix Confidence Definitions

Each recommendation includes a **Fix Confidence** label indicating how safe it is to act:

| Label | Meaning | Action | Icon |
|-------|---------|--------|------|
| **Safe** | Clear fix, low risk, no trade-offs | Do it — straightforward improvement | ❌/⚠️ |
| **Judgment** | Trade-offs exist, context matters | Evaluate — consider breaking changes, scope | ⚠️ |
| **Enhancement** | Nice-to-have, optional polish | Backlog — address when convenient | 💡 |

**Why two dimensions?** Priority (High/Medium/Low) indicates *impact*. Fix Confidence (Safe/Judgment/Enhancement) indicates *certainty*. A High-priority + Safe fix should be done immediately. A High-priority + Judgment fix needs review first.

**Skill-forge integration**: Recommendations with `Safe` confidence can be auto-applied:
```bash
/skill-farm improve <skill> --confidence=safe    # Apply safe fixes
/skill-farm improve <skill> --interactive        # Review judgment calls
```

### When to Use

| Situation | Recommendation |
|-----------|----------------|
| After smoke passes | Run `--recommend` to improve coverage |
| Before draft → active | Essential — need 80% coverage |
| New skill created | Run early to establish test baseline |
| After adding features | Check if new capabilities need tests |

---

## Why Checklists Over Scores

| Aspect | Old MetaScore | New Checklist |
|--------|---------------|---------------|
| **Precision** | "82.1%" (false) | "12/16 passed" (honest) |
| **Actionability** | Vague | Specific items to fix |
| **Subjectivity** | Hidden in weights | Transparent in criteria |
| **Gaming risk** | High (optimize for score) | Low (binary checks) |
| **Interpretation** | Requires context | Self-evident |

---

## Implementation Notes

### Automated Checks (can be scripted)
- File existence
- YAML parsing
- Field presence
- Semver validation
- Directory structure

### Manual Assessment (requires reading)
- Documentation clarity
- Example quality
- Scope appropriateness

The checklist explicitly separates these — no pretending we can automate judgment.

---

## Limitations

> **What this tool cannot do** — Honesty about boundaries is itself a quality signal.

| Limitation | Why It Exists | Workaround |
|------------|---------------|------------|
| **Cannot measure "clarity"** | Clarity is subjective — what's clear to experts confuses beginners | Check proxies: "has examples", "first paragraph explains purpose" |
| **Cannot verify runtime correctness** | Would require actually running with Claude (expensive) | Use `smoke` for basic test, `--manual` for full validation |
| **Cannot detect subtle scope creep** | "Related" is subjective | Manual review + the ">10 commands" heuristic |
| **Cannot assess real-world usefulness** | Requires user feedback over time | Track in external system, review outcomes |
| **Checklist is opinionated** | Our checks reflect our values, not universal truth | Fork and customize for your needs |

**The fundamental limit**: This tool checks *form*, not *function*. A skill can pass all checks and still be useless. A skill can fail checks and still be valuable. The checklist is a **starting point**, not a verdict.

---

## Examples

### Example 1: Full unified flow (with AX output)

```
/skill-check skill-forge

→ UX Output (visible):
Skill Analysis: skill-forge
Status: active (requires 80% pass)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Structure (6/6) ✓
Documentation (3/3) ✓
Quality (3/4)
  ✗ [auto] No tests.json
Integration (3/3) ✓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Result: 15/16 passed (94%) — PASS

Smoke Test: skill-forge — PASS ✓
  ✓ Loaded ✓ Invoked ✓ No errors ✓ Completed (0.8s)

→ AX Output (hidden):
<!-- skill-check:state
target: skill-forge
phase: smoke
result: {pass: 15, fail: 1, total: 16, percentage: 94}
threshold_met: true
failed_checks: ["tests.json"]
phases_run: [analyze, smoke]
phases_available: [manual, recommend]
/skill-check:state -->

→ Agent asks (multiselect):
  "What additional phases do you want to run?"
  [ ] Generate test coverage recommendations
  [ ] Run manual validation

→ User selects: [x] recommendations, [ ] manual

→ Runs RECOMMEND phase...

→ Final CLEAR Check:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 CLEAR: C✓ L✓ E✓ A✓ R○ | Result: PASS 15/16 (94%) | Next: add tests.json
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

<!-- skill-check:state
target: skill-forge
phase: complete
result: {pass: 15, fail: 1, total: 16, percentage: 94}
clear: {C: done, L: done, E: done, A: done, R: pending}
phases_run: [analyze, smoke, recommend]
recommendations: ["Add tests.json"]
next: "add tests.json or run /retro"
/skill-check:state -->
```

### Example 2: Compare two skills

```
/skill-check skill-forge --compare=skill-check

→ Shows side-by-side checklist results with winner
```

### Example 3: Smoke test

```
/skill-check smoke my-skill

→ Actually invokes /my-skill and checks for errors
```

### Example 4: Manual validation (shortcut flag)

```
/skill-check meta --manual

→ Output:
Skill Analysis: meta
Status: active (requires 80% pass)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[Auto-check results...]
Result: 14/16 passed (88%) — PASS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Smoke Test: PASS

→ (--manual flag skips prompt, proceeds directly to manual validation)

→ Agent suggests scenario:
  "Recommended: /meta 'Should I use Redis or Memcached for session storage?'"
  "Rationale: Tests decision analysis with concrete trade-offs"

→ Agent defines rubric, invokes /meta, evaluates output

→ Records result to meta/SKILL.md history
```

### Example 5: Test recommendations (shortcut flag)

```
/skill-check interview --recommend

→ Skill Analysis: [results...]
→ Smoke Test: PASS (skill loads, invokes correctly)

→ (--recommend flag skips prompt, proceeds directly to recommendations)

→ Output:
Test Coverage Recommendations: interview
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Current Coverage: 5/16 assertion types (31%)
Assertions Used: file_exists, frontmatter_valid, has_field, trigger_matches, content_contains

### High-Value Additions

1. **Behavioral Tests**
   Your skill documents question generation — add:
   { "input": "/meta --plan", "expected": { "output_contains": ["Task"] } }

2. **Negative Tests**
   { "type": "content_not_contains", "text": "TODO" }

### Medium-Value Additions

3. **Tool Assertions**
   You list [Read, Glob, AskUserQuestion] — verify each with has_tool

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Lifecycle Impact:
  Current: Draft (31%) — needs 40% for Draft ✓
  To reach Active (80%): Add 8 more assertions
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Validation Workflow

The "Human validated" check can be verified two ways:

1. **Interactive**: Use `/skill-check <skill> --manual` for immediate validation with rubric
2. **Tracked**: Use any external task system (Beads, Jira, GitHub Issues, etc.) to formally track validation

### How It Works

1. **Run manual validation**: `/skill-check <skill> --manual` generates a rubric and tests the skill
2. **Human tests the skill**: Invoke the skill with real inputs, verify output is useful
3. **Record result**: Add `manual_validated: "date - scenario - PASS/FAIL"` to SKILL.md history
4. **(Optional)** Track in external system if using one

### What "Validated" Means

- ✓ Skill was invoked by a human (not just smoke test)
- ✓ Output matched documented behavior
- ✓ Human judged the output was **useful**, not just error-free

---

## Error Handling

When `/skill-check` encounters failures, it should produce parseable error output:

### Error Output Format (UX)

```
❌ skill-check ERROR: [category]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Problem: [what went wrong]
Cause: [why it happened]
Fix: [actionable recovery step]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Error Categories

| Category | Cause | Recovery |
|----------|-------|----------|
| `SKILL_NOT_FOUND` | Path invalid or skill missing | Verify path, check glob pattern |
| `PARSE_ERROR` | Invalid YAML frontmatter | Fix syntax, validate with YAML linter |
| `SMOKE_FAILURE` | Skill invocation errored | Check skill syntax, review error log |
| `TIMEOUT` | Manual validation exceeded 60s | Simplify scenario or increase timeout |
| `TESTS_MISSING` | No tests.json found | Create tests.json with minimum 4 tests |

### Error State Block (AX)

Errors also emit structured state for agent recovery:

```
<!-- skill-check:state
status: error
error_category: SKILL_NOT_FOUND
target: unknown-skill
recoverable: true
suggested_action: "Verify skill path exists"
/skill-check:state -->
```

---

## CLEAR Check (Required Exit)

**Every `/skill-check` invocation must end with a CLEAR check.** *(model — universal exit pattern)*

### Compact Format (Default)

```
📋 CLEAR: C✓ L✓ E✓ A✓ R○ | Result: PASS 14/16 | Next: [action]
```

### Full Format (When NOT CLEAR)

```
📋 CLEAR: C✓ L✓ E✗ A○ R-
   ↳ E blocked: smoke test failed
   ⚡ Fix skill errors before proceeding

🌾 Uncommitted: skill.md — run `git add && git commit`
```

### Hidden State Block (AX)

Every CLEAR Check includes hidden agent state for continuity:

```
<!-- skill-check:state
target: skill-name
phase: analyze|smoke|manual|recommend|complete
result: {pass: 14, fail: 2, total: 16, percentage: 88}
lifecycle: active
threshold_met: true
clear: {C: done, L: done, E: done, A: done, R: pending}
phases_run: [analyze, smoke]
phases_available: [manual, recommend]
recommendations: ["Add tests.json", "Add usage examples"]
next: "run manual validation or commit changes"
/skill-check:state -->
```

### State Field Definitions

| Field | Type | Description |
|-------|------|-------------|
| `target` | string | Skill being analyzed |
| `phase` | enum | Current/last phase executed |
| `result` | object | Pass/fail counts for agent logic |
| `lifecycle` | string | Target skill's lifecycle status |
| `threshold_met` | boolean | Did skill meet its tier threshold? |
| `clear` | object | CLEAR phase completion status |
| `phases_run` | array | Which phases have executed |
| `phases_available` | array | Which phases can still run |
| `recommendations` | array | Top priority fixes (extractable) |
| `next` | string | Suggested next action |

---

## Agent Output Templates

### ANALYZE Phase Output

**UX (visible)**:
```
Skill Analysis: my-skill
Status: active (requires 80% pass)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Structure (5/6)
  ✓ [auto] SKILL.md exists
  ✓ [auto] Valid YAML frontmatter
  ✗ [auto] Missing allowed-tools field
...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Result: 12/16 passed (75%) — FAIL
Top Priority: Add allowed-tools, Add examples
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**AX (hidden)**:
```
<!-- skill-check:state
target: my-skill
phase: analyze
result: {pass: 12, fail: 4, total: 16, percentage: 75}
lifecycle: active
threshold_met: false
checks: {structure: {pass: 5, fail: 1}, documentation: {pass: 2, fail: 1}, quality: {pass: 3, fail: 1}, integration: {pass: 2, fail: 1}}
failed_checks: ["allowed-tools", "usage-examples", "tests.json", "human-validated"]
recommendations: ["Add allowed-tools to frontmatter", "Add usage example section"]
/skill-check:state -->
```

### SMOKE Phase Output

**UX (visible)**:
```
Smoke Test: my-skill — PASS ✓
  ✓ Loaded (0.2s) ✓ Invoked ✓ No errors ✓ Completed (1.2s)
```

**AX (hidden)**:
```
<!-- skill-check:state
target: my-skill
phase: smoke
smoke: {loaded: true, invoked: true, errors: false, duration_ms: 1200}
/skill-check:state -->
```

### MANUAL Phase Output

**UX (visible)**:
```
Manual Validation: my-skill
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Scenario: /my-skill "test input"
Rubric Check: 5/5 ✓
Result: PASS — recorded to SKILL.md history
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**AX (hidden)**:
```
<!-- skill-check:state
target: my-skill
phase: manual
manual: {scenario: "/my-skill 'test input'", rubric_pass: 5, rubric_fail: 0, result: "pass", recorded: true}
/skill-check:state -->
```

### Full Session Output (End of Check)

After all phases complete, emit final CLEAR Check with complete state:

**UX (visible)**:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 CLEAR: C✓ L✓ E✓ A✓ R○ | Result: PASS 14/16 (88%) | Next: commit or /retro
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**AX (hidden)**:
```
<!-- skill-check:state
target: my-skill
phase: complete
result: {pass: 14, fail: 2, total: 16, percentage: 88}
lifecycle: active
threshold_met: true
clear: {C: done, L: done, E: done, A: done, R: pending}
phases_run: [analyze, smoke, manual]
manual: {scenario: "...", result: "pass"}
next: "commit changes or run /retro"
/skill-check:state -->
```

---

## Self-Analysis

This skill should pass its own checklist:

```
/skill-check skill-check
```

If it doesn't, that's a bug to fix, not a score to rationalize.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demithras) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
