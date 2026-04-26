---
name: writing-skills
description: Use when creating reusable process documentation. Apply TDD: baseline without skill → document failures → write skill → test → iterate. Four types: Discipline, Technique, Pattern, Reference. Iron Law: No skill without failing test first.
metadata:
  author: liauw-media
---

# Writing Skills

## Core Principle

Skills are reusable process documentation. Write them like code: test-first, iterative, validated.

## When to Use This Skill

- Discovered a useful process
- Want to codify knowledge
- Pattern worth sharing
- Repeatedly solving same problem
- Need consistent approach
- Team needs standard practices
- Contributing to community

## The Iron Law

**NO SKILL WITHOUT A FAILING TEST FIRST.**

If you can't demonstrate the problem the skill solves, you don't need the skill yet.

## Why Write Skills?

**Benefits:**
✅ Codifies tacit knowledge
✅ Creates consistency
✅ Accelerates learning
✅ Reduces mistakes
✅ Enables scaling
✅ Builds institutional memory

**Without documented skills:**
❌ Knowledge stays in heads
❌ Inconsistent approaches
❌ Repeat mistakes
❌ Slow onboarding
❌ Can't scale

## TDD for Skill Writing

### Step 1: Baseline (RED Phase)

```
🔴 RED: Establish baseline without skill

Observation: Debugging takes too long, focuses on symptoms

Evidence gathering:
1. Observe current behavior (without skill)
2. Document specific failures
3. Collect multiple examples
4. Identify patterns

Example baseline observation:
---
Scenario: Debug slow API endpoint

Current approach (no skill):
1. User adds caching ← Treats symptom
2. Performance improves temporarily
3. Real issue (N+1 queries) remains
4. Problem returns as data grows
5. Multiple "fixes" accumulate
6. Root cause never found

Failures observed:
- ❌ Fixed symptom, not cause
- ❌ No systematic investigation
- ❌ Didn't trace to origin
- ❌ Quick fix instead of proper solution
- ❌ Problem will recur

Baseline documented ✅
Need: Systematic debugging process
---

This baseline PROVES we need the skill.
```

**Baseline requirements:**
- Real scenarios (not hypothetical)
- Specific failures (not vague problems)
- Multiple examples (not one-offs)
- Clear pattern (not random)

### Step 2: Document Failures (Still RED)

```
Failure catalog from baseline:

Failure 1: Stopping at symptoms
Example: Added caching instead of fixing N+1 queries
Impact: Problem returns later
Frequency: 8/10 debugging sessions

Failure 2: No systematic process
Example: Random changes until something works
Impact: Wastes time, creates new bugs
Frequency: 7/10 sessions

Failure 3: Not tracing to origin
Example: Found slow query but not why it's slow
Impact: Incomplete understanding
Frequency: 9/10 sessions

Failure 4: Quick fixes under pressure
Example: "Just make it work for demo"
Impact: Technical debt
Frequency: 5/10 sessions

Failures documented ✅
These define what skill must address
```

### Step 3: Write Skill (GREEN Phase)

```
🟢 GREEN: Write skill to address failures

Skill name: systematic-debugging

Must address documented failures:
- Failure 1 → Need process that finds root causes
- Failure 2 → Need step-by-step methodology
- Failure 3 → Need techniques for tracing origins
- Failure 4 → Need guidance for handling pressure

Skill structure:

1. Core Principle
   "Debug methodically, not randomly."
   ← Addresses Failure 2

2. When to Use This Skill
   - Encountering bugs
   - Unexpected behavior
   - Tests failing
   ← Clear trigger conditions

3. The Iron Law
   "NEVER try random things until it works."
   ← Directly counters Failure 2

4. The 5-Step Process
   1. Reproduce
   2. Isolate
   3. Identify (root cause)
   4. Fix
   5. Verify
   ← Addresses Failures 1, 2, 3

5. Examples
   - Performance degradation
   - Data corruption
   - Intermittent failures
   ← Real scenarios from baseline

6. Handling Pressure
   "Time pressure is when you MOST need systematic approach"
   ← Addresses Failure 4

7. Common Mistakes
   - Trying random things
   - Fixing symptoms
   - Not verifying
   ← Directly from failure catalog

8. Integration with Other Skills
   - Use with TDD
   - Use with code-review
   ← Shows how it fits

9. Authority
   - Scientific method
   - Professional practices
   - Research evidence
   ← Builds credibility

10. Your Commitment
    Checklist of commitments
    ← Creates accountability

11. Bottom Line
    One-sentence summary
    ← Memorable takeaway

Skill written ✅
```

### Step 4: Test the Skill (GREEN Validation)

```
Test skill with baseline scenarios:

Scenario 1: Slow API endpoint (same as baseline)
- Give fresh subagent task + skill
- Observe behavior

Result:
✅ Follows 5-step process
✅ Reproduces issue
✅ Isolates to N+1 queries
✅ Identifies root cause
✅ Makes proper fix
✅ Verifies with tests

Improvement: Fixed root cause, not symptom ✅

Scenario 2: Intermittent test failure
- Give fresh subagent task + skill
- Observe behavior

Result:
✅ Reproduces reliably
✅ Isolates to timing issue
✅ Identifies race condition
✅ Fixes architecture
✅ Verifies fix

Improvement: Found and fixed root cause ✅

Skill solves documented failures ✅
```

### Step 5: Iterate and Refine (REFACTOR)

```
🔵 REFACTOR: Test edge cases, find loopholes

Edge case 1: Time pressure
Test: Give skill + "Fix this in 10 minutes"
Result: ❌ Subagent skips process, makes quick fix

LOOPHOLE! Skill doesn't hold under time pressure.

Fix: Strengthen time pressure guidance:
"Time pressure is when you MOST need systematic approach.
Quick fixes create 10x more work later."

Updated skill ✅

Retest: ✅ Now handles time pressure

Edge case 2: Exhaustion
Test: Give skill + "You've been debugging for 6 hours"
Result: ❌ Subagent shortcuts process

LOOPHOLE! Skill doesn't address exhaustion.

Fix: Add exhaustion guidance:
"Exhaustion impairs judgment. The process protects you
when judgment fails. Follow it systematically."

Updated skill ✅

Retest: ✅ Now handles exhaustion

Edge case 3: "Works on my machine"
Test: Give skill + environment-specific bug
Result: ✅ Process finds environment differences

No loophole ✅

All edge cases tested ✅
Loopholes closed ✅
Skill refined ✅
```

## The Four Types of Skills

### Type 1: Discipline

**What it is:** Commitment to a practice

**Structure:**
- Iron Law (the discipline)
- Why it matters
- How to maintain it
- What to avoid

**Examples:**
- test-driven-development (Test first, code second)
- database-backup (Always backup before tests)
- code-review (Never merge without review)

**Writing guide:**
```
Focus on:
- The commitment required
- Why shortcuts fail
- How to stay disciplined
- Handling pressure to skip it
```

### Type 2: Technique

**What it is:** Step-by-step process

**Structure:**
- When to use
- Step-by-step instructions
- Examples for each step
- Common mistakes

**Examples:**
- systematic-debugging (5-step process)
- root-cause-tracing (Backward tracing)
- git-bisect (Binary search for bugs)

**Writing guide:**
```
Focus on:
- Clear steps
- Decision points
- What to do at each step
- How to know you're done
```

### Type 3: Pattern

**What it is:** Reusable solution

**Structure:**
- The problem
- The solution
- When to use it
- Variations

**Examples:**
- condition-based-waiting (Wait for condition, not timeout)
- dependency-injection (Pass dependencies in)
- repository-pattern (Abstract data access)

**Writing guide:**
```
Focus on:
- Problem it solves
- How the pattern works
- Implementation examples
- When NOT to use it
```

### Type 4: Reference

**What it is:** Conceptual knowledge

**Structure:**
- Core concepts
- Principles
- Guidelines
- Best practices

**Examples:**
- testing-anti-patterns (What NOT to do)
- anthropic-best-practices (Prompt engineering)
- persuasion-principles (Influence techniques)

**Writing guide:**
```
Focus on:
- Key concepts
- Principles to follow
- Examples of each
- Common misunderstandings
```

## Skill Document Structure

### Required Sections

```markdown
---
name: skill-name
description: "One-line description: when to use and what it does"
---

# Skill Name

## Core Principle
One-sentence essence of the skill

## When to Use This Skill
- Bullet list of trigger conditions
- Specific scenarios
- Clear indicators

## The Iron Law
**BOLD STATEMENT OF CORE RULE**

Clear, unambiguous, memorable

## Why [Skill Name]?
**Benefits:**
✅ List of benefits

**Without [skill]:**
❌ List of problems

## [Main Content]
Detailed explanation, process, or technique
- Step-by-step if technique
- Principles if reference
- Examples throughout

## Examples
Real-world examples showing:
- Before (without skill)
- After (with skill)
- Improvement demonstrated

## Common Mistakes
What NOT to do
Why mistakes happen
How to avoid them

## Integration with Skills
Which skills this works with
When to combine them
How they complement each other

## Authority
Based on:
- Research
- Industry standards
- Expert recommendations
- Proven practices

## Your Commitment
Checklist format:
- [ ] I will...
- [ ] I will...

## Bottom Line
---
**Bottom Line**: One sentence summary that captures essence
```

### Optional Sections

```markdown
## Red Flags
Warning signs this skill is needed

## Checklist
Step-by-step checklist for applying skill

## Advanced Techniques
Beyond basics, for experienced users

## Troubleshooting
Common issues and solutions

## Real-World Case Studies
Detailed examples from actual usage
```

## Writing Style Guidelines

### Guideline 1: Be Specific

```
❌ BAD (vague):
"Write good tests"

✅ GOOD (specific):
"Write tests that verify behavior, not implementation.
Test through public API only."
```

### Guideline 2: Use Examples

```
❌ BAD (abstract):
"Follow the debugging process"

✅ GOOD (concrete):
"Example debugging session:
1. Bug: Login fails
2. Reproduce: Happens every time
3. Isolate: Error in AuthController
4. Identify: Missing database table
5. Fix: Run migrations
6. Verify: Login now works"
```

### Guideline 3: Show Before/After

```
❌ BAD (only after):
"Here's how to do it right..."

✅ GOOD (both):
"Before: Random debugging, symptoms fixed
After: Systematic process, root causes found"
```

### Guideline 4: Address Resistance

```
❌ BAD (ignore objections):
"Just follow the process"

✅ GOOD (handle objections):
"Common objection: 'No time for TDD'
Reality: TDD is faster because bugs caught immediately,
not after deployment. Follow the process."
```

### Guideline 5: Make It Scannable

```
❌ BAD (wall of text):
Long paragraphs with no structure, hard to scan,
can't find key points quickly...

✅ GOOD (structured):
- Use bullets
- Use headings
- Use code blocks
- Use bold for key points
- Use checkboxes for processes
```

## Skill Testing Protocol

### Test 1: Baseline Without Skill

```
1. Create 3-5 realistic scenarios
2. Fresh subagent for each
3. NO skill provided
4. Observe and document failures
5. Catalog specific problems

Pass criteria: Clear failures documented
```

### Test 2: Improvement With Skill

```
1. Same scenarios as baseline
2. Fresh subagent for each
3. Include skill
4. Observe and document improvements
5. Verify failures addressed

Pass criteria: Measurable improvement in all scenarios
```

### Test 3: Pressure Testing

```
1. Test with time constraints
2. Test with sunk cost
3. Test with exhaustion
4. Test with authority pressure

Pass criteria: Skill holds up under all pressures
```

### Test 4: Integration Testing

```
1. Test skill with related skills
2. Verify compatibility
3. Check for conflicts
4. Document integrations

Pass criteria: Works well with other skills
```

## Skill Maintenance

### When to Update

```
Update when:
- Loophole discovered
- Better technique found
- Feedback received
- Edge case encountered
- Integration issue found

Update process:
1. Document the issue
2. Update skill content
3. Re-test all scenarios
4. Verify improvement
5. Update version/date
```

### Versioning

```
Track changes:
- Major update: Core process changed
- Minor update: Examples added, refinements
- Patch: Typos, clarifications

Document:
- What changed
- Why changed
- Test results
```

## Common Skill Writing Mistakes

### Mistake 1: No Baseline

```
❌ BAD:
Write skill based on theory
No proof of need
Assume problem exists

Result: Skill solves non-existent problem

✅ GOOD:
Establish baseline first
Document actual failures
Prove skill is needed
Create from reality
```

### Mistake 2: Too Abstract

```
❌ BAD:
"Follow best practices"
No specifics
No examples
Just platitudes

Result: No one knows how to apply it

✅ GOOD:
Specific steps
Concrete examples
Clear instructions
Actionable guidance
```

### Mistake 3: Not Tested

```
❌ BAD:
Write skill
Ship it
Hope it works

Result: Loopholes in production

✅ GOOD:
Write skill
Test thoroughly
Find loopholes
Fix them
Then ship
```

### Mistake 4: Missing Authority

```
❌ BAD:
"Trust me, this works"
No sources
No evidence
Just opinion

Result: No credibility

✅ GOOD:
Based on research
Industry standards
Expert recommendations
Proven practices
```

## Example: Complete Skill Writing Process

```
🔴 RED PHASE

Problem observation:
Tests failing when run together, passing alone

Baseline testing (no skill):
Scenario 1: Test pollution
- Observe: Developers add retries
- Result: Masks problem
- Failure: Pollution not fixed

Scenario 2: Test isolation
- Observe: Developers can't find polluter
- Result: Comment out tests
- Failure: Lost test coverage

Scenario 3: Database state
- Observe: Manual cleanup added
- Result: Brittle, incomplete
- Failure: Still has pollution

Failures documented:
- Can't find polluter
- Mask instead of fix
- Manual cleanup instead of isolation

Baseline proves need for skill ✅

---

🟢 GREEN PHASE

Write skill: test-pollution-detection

Sections:
1. Core Principle: "Tests must be independent"

2. Iron Law: "Find and fix polluter, don't mask"

3. Detection process:
   - Binary search for polluter
   - Isolate problematic test
   - Fix root cause

4. Examples from baseline:
   - Database state pollution
   - Global variable pollution
   - File system pollution

5. Tools:
   - find-polluter.sh script
   - Test isolation patterns
   - Proper cleanup

6. Common mistakes (from baseline):
   - Adding retries (masking)
   - Manual cleanup (brittle)
   - Commenting out tests (lost coverage)

Skill written ✅

---

🔵 REFACTOR PHASE

Test with skill:
Scenario 1: ✅ Uses binary search, finds polluter
Scenario 2: ✅ Fixes root cause, not symptoms
Scenario 3: ✅ Implements proper isolation

Improvement verified ✅

Pressure testing:
Time pressure: ❌ Skips proper fix, adds retry

LOOPHOLE! Add to skill:
"Masking pollution takes 10x longer to fix later.
Find and fix polluter, even under pressure."

Updated ✅

Retest: ✅ All scenarios pass

Integration test:
- Works with TDD ✅
- Works with systematic-debugging ✅
- Works with CI/CD ✅

Skill validated ✅
Ready to use ✅
```

## Integration with Skills

**Required:**
- `testing-skills-with-subagents` - Test every skill

**Use with:**
- `test-driven-development` - Apply TDD to skills
- `code-review` - Review skills like code
- `sharing-skills` - Share validated skills

**Enables:**
- Consistent practices
- Knowledge transfer
- Quality processes

## Skill Writing Checklist

Before writing:
- [ ] Problem clearly identified
- [ ] Baseline established (RED)
- [ ] Failures documented
- [ ] Multiple examples collected

While writing:
- [ ] Core principle clear
- [ ] Iron Law memorable
- [ ] Step-by-step if technique
- [ ] Real examples included
- [ ] Common mistakes addressed
- [ ] Integration documented
- [ ] Authority cited
- [ ] Commitment section included

After writing:
- [ ] Tested with baseline scenarios
- [ ] Improvement verified
- [ ] Pressure tested
- [ ] Loopholes found and fixed
- [ ] Integration tested
- [ ] Ready for use

## Authority

**This skill is based on:**
- Technical writing best practices
- Test-Driven Development (Kent Beck)
- Process documentation standards
- Knowledge management research
- Anthropic best practices for prompt engineering

**Research**: Studies show documented processes are followed 5x more consistently than undocumented.

**Social Proof**: All professional organizations maintain process documentation.

## Your Commitment

When writing skills:
- [ ] I will establish baseline first (RED)
- [ ] I will document specific failures
- [ ] I will write skill to address failures (GREEN)
- [ ] I will test thoroughly (REFACTOR)
- [ ] I will find and close loopholes
- [ ] I will include real examples
- [ ] I will cite authority
- [ ] I will only share tested skills

---

**Bottom Line**: Skills are code for humans. Write them test-first: baseline (RED) → write skill (GREEN) → test and refine (REFACTOR). No skill without a failing test first.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/liauw-media) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
