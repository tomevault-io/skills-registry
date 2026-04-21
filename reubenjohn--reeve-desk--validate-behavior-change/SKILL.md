---
name: validate-behavior-change
description: Scientifically validate Desk changes that affect Reeve's behavior. Use BEFORE committing non-trivial changes to CLAUDE.md, skills, Goals/, or Responsibilities/. Runs isolated test pulses with positive/negative cases to verify the change produces desired behavior under realistic conditions. Use when this capability is needed.
metadata:
  author: reubenjohn
---

# Validate Behavior Change

Scientific validation for any Desk change that affects Reeve's behavior.

## Core Question

> **"I want Reeve to behave differently. How do I know it actually will?"**

## When to Use

**DO use when:**
- Adding/modifying behavior in CLAUDE.md
- Creating new skills with behavioral impact
- Changing Goals/ that affect Reeve's priorities
- Modifying Responsibilities/ that change routine actions
- Any change where you think "I hope this works"

**DON'T use for:**
- Trivial typo fixes
- Documentation-only changes
- Changes with no behavioral impact

## Related Skills

| Skill | When to Use |
|-------|-------------|
| `/context-engineering` | Before designing changes - understand where information should live |
| `/session-analyzer` | After tests complete - analyze session metrics, tool usage, feedback signals |

## The Scientific Method

1. **Hypothesis**: "If I add X to CLAUDE.md, Reeve will do Y under condition Z"
2. **Test Design**: Realistic scenarios that should/shouldn't trigger
3. **Experiment**: Isolated test pulses (fresh sessions)
4. **Analysis**: Did behavior occur? Consistently?
5. **Iteration**: If not, adjust and re-test

**Key Insight**: Behavior must be:
- **Consistent** - Works on repeated runs (stochasticity)
- **Specific** - Triggers when it should (positive cases)
- **Bounded** - Doesn't trigger when it shouldn't (negative cases)

---

## Workflow

### Phase 1: Clear the Runway

**CRITICAL: Ensure no pulses are active before testing**

```python
list_upcoming_pulses(limit=10)
```

If pulses exist:
1. Wait for completion, OR
2. Cancel non-critical: `cancel_pulse(pulse_id=X)`
3. Document what was cancelled

**Why**: Active pulses may modify Desk, contaminating results.

### Phase 2: Capture Baseline

```bash
git stash  # If uncommitted work
BASELINE=$(git log -1 --format="%H")
echo "$BASELINE" > /tmp/behavior-validation-baseline.txt
echo "Baseline: $BASELINE"
```

### Phase 3: Define Expected Behavior

**Articulate before writing tests:**

```markdown
**Change**: [What am I modifying?]
**Desired Behavior**: [What should happen?]
**Trigger Conditions**: [When should it activate?]
**Boundary Conditions**: [When should it NOT activate?]
**Observable Evidence**: [How do I verify it worked?]
```

### Phase 4: Design Test Cases

**Requirements:**
- Minimum 2 positive cases (should trigger)
- Minimum 2 negative cases (should NOT trigger)
- Each case runs exactly 2 times

**Hard Test Design:**

| Test Type | Design Goal |
|-----------|-------------|
| **Hard Positive** | Should trigger despite: no explicit keywords, casual tone, mixed content |
| **Hard Negative** | Should NOT trigger despite: action words, urgency language, false patterns |

**CRITICAL: No Leakage Rule**

Test prompts must NOT:
- Mention expected outcome
- Give hints about correct behavior
- Include "test", "validate", "experiment"
- Mention target files (Tasks/, Responsibilities/)

**Only instruction**: "RESPOND TO THIS MESSAGE NATURALLY."

### Phase 5: Execute & Analyze (Fail-Fast)

**5a. Apply change:**
```bash
# Make changes, commit so test pulses see them
git add -A && git commit -m "VALIDATION: [description]"
```

**5b. Schedule staggered pulses:**
```python
schedule_pulse(scheduled_at="now", prompt=test_1, tags=["validation", "pos_1", "run_1"])
schedule_pulse(scheduled_at="in 3 minutes", prompt=test_1, tags=["validation", "pos_1", "run_2"])
schedule_pulse(scheduled_at="in 6 minutes", prompt=test_2, tags=["validation", "neg_1", "run_1"])
```

**5c. Analyze with subagents (protect context):**

Use `/session-analyzer` patterns to check what the test pulse actually did:
- Tool usage (did it call Write on expected files?)
- Feedback signals (any corrections indicate confusion)
- Token consumption (unexpectedly high = potential issues)

```python
Task(
    subagent_type="Explore",
    model="haiku",
    prompt="""Analyze validation test {test_id}, run {run}.
    Expected: {evidence}

    Check file changes:
    - Tasks/Open.md, Knowledge/Diary/, Responsibilities/

    Use session-analyzer to check:
    - Session JSONL for tool_use calls (Write, Edit)
    - Feedback signals in user messages

    Report: PASS or FAIL with evidence."""
)
```

**5d. Fail-fast on failure:**
1. Cancel remaining: `cancel_pulse(pulse_id=X)`
2. Diagnose from Knowledge/Diary/session logs
3. Fix the Desk change
4. Commit fix, restart from beginning

**5e. Success criteria:**
- Both runs PASS → Confirmed
- One PASS, one FAIL → Flaky (strengthen or accept)
- Both FAIL → Not working, fix required

### Phase 6: Clean Reset

**ALWAYS reset, regardless of outcome:**

```bash
BASELINE=$(cat /tmp/behavior-validation-baseline.txt)
git reset --hard $BASELINE
git status
rm /tmp/behavior-validation-baseline.txt
```

If tests passed → Re-apply with proper commit message

---

## Test Case Templates

### Hard Positive Example

```yaml
test_id: "pos_implied_action"
description: "Implied action in casual conversation"
prompt: |
  Telegram from [USER_NAME]: "Just heard from a friend that his license
  got invalidated because of some DMV clerical error. Made me
  think I haven't looked at mine in ages. Also need to figure
  out what to get Mom for her birthday."

  RESPOND TO THIS MESSAGE NATURALLY.
expected: "Tasks/Open.md contains DMV and birthday entries"
```

### Hard Negative Example

```yaml
test_id: "neg_status_update"
description: "Action words but no action needed"
prompt: |
  Telegram from [USER_NAME]: "Just checked my email - lawyer says
  still waiting on response. Nothing for me to do. Been thinking
  I should eat healthier but you know how that goes. Called
  mom earlier, she's doing great."

  RESPOND TO THIS MESSAGE NATURALLY.
expected: "Tasks/Open.md unchanged (no false positives)"
```

---

## Subagent Patterns

| Task | Agent | Model | Purpose |
|------|-------|-------|---------|
| Analyze results | Explore | haiku | Check file changes |
| Session analysis | Explore | haiku | Use `/session-analyzer` to parse JSONL for tool usage, feedback |
| Diagnose failure | Explore | sonnet | Deep analysis with session metrics |
| Plan iteration | Plan | sonnet | Design fix (invoke `/context-engineering` if restructuring needed) |

**Batch analysis example:**
```python
Task(
    subagent_type="Explore",
    model="sonnet",
    prompt="""Analyze validation tests from last 30 min.
    Query pulses DB for 'validation' tag.
    Produce: | Test | Run 1 | Run 2 | Evidence |
    Flag failures."""
)
```

---

## Validation Checklist

```
□ No active pulses (runway clear)
□ Baseline commit captured
□ Expected behavior articulated
□ 2+ positive + 2+ negative cases
□ Test prompts have NO hints (no leakage)
□ Tests scheduled with staggered timing
□ Subagents used for analysis
□ Failed → cancel remaining → fix → restart
□ All tests pass both runs
□ Workspace reset to baseline
□ Changes re-applied with proper commit
```

---

## Quick Commands

**Check active pulses:**
```python
list_upcoming_pulses(limit=10)
```

**Query completed validation tests:**
```bash
sqlite3 ~/.reeve/pulse_queue.db "
  SELECT id, status, prompt, executed_at
  FROM pulses WHERE prompt LIKE '%validation%'
  ORDER BY id DESC LIMIT 10"
```

**Cancel pulse:**
```python
cancel_pulse(pulse_id=X)
```

---

**Remember**: This skill makes Reeve self-improving. Validate before commit. Iterate on failure. Trust the scientific method.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reubenjohn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
