---
name: ccpm-code-review
description: Enforces quality verification gates with four-step validation (tests pass, build succeeds, checklist complete, no blockers) before task completion, PR creation, or status updates. Auto-activates when user says "done", "complete", "finished", "ready to merge", or runs /ccpm:verify or /ccpm:done commands. Provides systematic verification workflow that prevents false completion claims and ensures production readiness. Blocks external system writes (Jira, Slack) until evidence collected. Integrates with external-system-safety for confirmation workflow. When verification fails, suggests /ccpm:verify to debug issues systematically. Use when this capability is needed.
metadata:
  author: neversight
---

# CCPM Code Review

Structured code review workflow integrated with CCPM's Linear-based project management system. Enforces "no completion claims without verification evidence" principle.

## When to Use

This skill auto-activates when:

- User says **"done"**, **"complete"**, **"finished"**, **"ready to merge"**
- Running **`/ccpm:verify`** command (natural workflow - recommended)
- Running **`/ccpm:done`** command (includes pre-flight verification)
- Running **`/ccpm:verify`** command (advanced)
- Before updating Linear task status to "Done"
- Before syncing Jira status
- Before creating GitHub/BitBucket PR
- Before sending Slack completion notifications

## CCPM Verification Workflow

CCPM provides a streamlined 3-step verification process:

### Step 1: Quality Checks (Automated)
Run linting, tests, and build checks to ensure technical correctness:

```bash
/ccpm:verify [issue-id]  # Auto-detects issue from git branch
```

**What it checks:**
- ✅ Linting passes (no style errors)
- ✅ Tests pass (all unit and integration tests)
- ✅ Build succeeds (no compilation errors)
- ✅ Checklist complete (100% of implementation items)

**If checks fail:** Command automatically suggests `/ccpm:verify` to debug systematically.

### Step 2: Agent Code Review
After quality checks pass, agent review analyzes:

- ✅ Code quality and best practices
- ✅ Security vulnerabilities
- ✅ Performance implications
- ✅ Requirement fulfillment
- ✅ Regression risks

### Step 3: Final Confirmation
Four verification gates must pass:

1. **Tests Pass** ✅ - Zero failures
2. **Build Succeeds** ✅ - Exit status 0
3. **Checklist Complete** ✅ - 100% checked
4. **No Blockers** ✅ - No unresolved blockers in Linear

**Only after all gates pass** can task proceed to finalization with `/ccpm:done`.

## Core Principles

### 1. Technical Correctness Over Social Comfort

**Forbidden performative agreement:**
- ❌ "Great point!"
- ❌ "You're absolutely right!"
- ❌ "That makes total sense!"

**Required rigorous verification:**
- ✅ "Let me verify that assumption"
- ✅ "I'll test this approach first"
- ✅ "Here's the evidence: [test output]"

### 2. No Implementation Before Verification

When receiving feedback:
1. **First**: Verify the feedback is technically correct
2. **Then**: Implement the change
3. **Never**: Blindly implement without understanding

### 3. NO COMPLETION CLAIMS WITHOUT EVIDENCE

**Required evidence before any "done" claim:**
- ✅ Tests: All passing (screenshot or CI link)
- ✅ Build: Exit status 0 (no errors)
- ✅ Linear checklist: 100% complete
- ✅ No unresolved blockers in Linear comments

## Integration with CCPM Commands

### With `/ccpm:verify` (Natural Workflow - Recommended)

**This skill enforces verification during the streamlined verify command:**

```bash
# User runs natural verification command
/ccpm:verify

# Or with explicit issue ID
/ccpm:verify AUTH-123
```

**Flow:**
```
User: "I'm done, let me verify"

Claude: [ccpm-code-review activates]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 Smart Verify Command
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📋 Issue: AUTH-123 - Add user authentication
📊 Status: In Progress
📋 Checklist: 100% (5/5 items)

Verification Flow:
──────────────────
1. Quality Checks (linting, tests, build)
2. Final Verification (code review, security)

═══════════════════════════════════════
Step 1/2: Running Quality Checks
═══════════════════════════════════════

🔍 Running linting...
✅ All files pass linting

🧪 Running tests...
✅ All tests passed (28/28)

🏗️  Running build...
✅ Build successful

📊 Quality Check Results:
  ✅ Linting
  ✅ Tests
  ✅ Build

═══════════════════════════════════════
Step 2/2: Running Final Verification
═══════════════════════════════════════

[Code reviewer agent analyzes changes...]

✅ All requirements met
✅ Code quality standards met
✅ Security best practices followed
✅ Performance acceptable

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ All Verification Complete!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

All verifications passed! Ready to finalize.

💡 What's Next?
⭐ Recommended: /ccpm:done AUTH-123
```

**If verification fails:**
```
❌ Quality Checks Failed

📊 Quality Check Results:
  ✅ Linting
  ❌ Tests (3 failures)
  ✅ Build

To debug and fix issues:
  /ccpm:verify AUTH-123

Then run verification again:
  /ccpm:verify AUTH-123
```

### With `/ccpm:done` (Pre-Flight Verification)

**This skill ensures quality before finalization:**

```bash
# User attempts to finalize task
/ccpm:done

# Or with explicit issue ID
/ccpm:done AUTH-123
```

**Pre-flight checks enforced by ccpm-code-review:**

```
User: "/ccpm:done AUTH-123"

Claude: [ccpm-code-review activates for pre-flight]

✅ All pre-flight checks passed!

Checking completion readiness:

□ Tests passing?
  → ✅ All 52 tests passed

□ Build successful?
  → ✅ Build completed (exit 0)

□ Linear checklist complete?
  → ✅ 7/7 items checked

□ No blockers?
  → ✅ No blockers found

VERIFICATION COMPLETE ✅

📝 Creating GitHub Pull Request...
✅ Pull Request created

[Prompts for Jira/Slack updates with confirmation]

✅ Linear issue updated to Done

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎉 Task Finalized: AUTH-123
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**If pre-flight fails:**
```
⛔ Cannot Finalize: Checklist Incomplete

Progress: 80% (4/5 completed)

❌ Remaining Items:
  - [ ] Write integration tests

🔧 Actions Required:
1. Complete remaining checklist items
2. Update checklist: /ccpm:sync AUTH-123
3. Then run: /ccpm:done AUTH-123
```

### Advanced: With `/ccpm:verify`

**For manual verification step (advanced users):**

```
Claude runs /ccpm:verify AUTH-123

[ccpm-code-review activates during verification]

Checking verification evidence:

□ Tests passing?
  → ✅ All 47 tests passed

□ Build successful?
  → ✅ Build completed with exit code 0

□ Linear checklist complete?
  → ❌ 3 of 5 items checked

□ No blockers?
  → ⚠️ 1 blocker found: "Redis connection failing in staging"

VERIFICATION FAILED:
- Linear checklist incomplete (3/5)
- Blocker unresolved: Redis connection issue

Cannot proceed with completion until:
1. Complete remaining checklist items
2. Resolve Redis blocker or document as out-of-scope

Would you like to:
A. Update checklist now
B. Investigate Redis blocker
C. Update task scope to exclude Redis
```

## Receiving Feedback Protocol

### When PR Reviewer Comments

**Scenario**: Reviewer says "Add error handling to login function"

**❌ Wrong approach** (performative):
```
"Great point! I'll add error handling right away."
[Immediately adds try-catch without understanding]
```

**✅ Correct approach** (verification-first):
```
Claude: [ccpm-code-review activates]

Let me verify this feedback:

1. Checking current error handling...
   ```typescript
   // login.ts
   async function login(email, password) {
     const user = await db.findUser(email);  // ← No error handling
     return generateToken(user);             // ← No error handling
   }
   ```

2. Potential issues:
   - Database query could fail (network, timeout)
   - user might be null (not found)
   - generateToken could throw (invalid data)

3. Verification: Feedback is VALID
   - Missing error handling confirmed
   - Could cause uncaught exceptions

4. Implementation approach:
   ```typescript
   async function login(email, password) {
     try {
       const user = await db.findUser(email);
       if (!user) {
         throw new UnauthorizedError('Invalid credentials');
       }
       return generateToken(user);
     } catch (error) {
       if (error instanceof DatabaseError) {
         logger.error('Database error during login', error);
         throw new ServiceUnavailableError();
       }
       throw error;
     }
   }
   ```

5. Testing the fix:
   - Add test: login with network error → returns 503
   - Add test: login with invalid user → returns 401
   - Verify existing tests still pass

6. Update Linear:
   - Add comment: "PR feedback: Added error handling"
   - Link to new commit

Implementing fix now...
```

### When Feedback Seems Wrong

**Scenario**: Reviewer says "Use var instead of const"

**❌ Wrong approach**:
```
"Okay, I'll change it!"
[Blindly makes change]
```

**✅ Correct approach**:
```
Claude: [ccpm-code-review activates]

Let me verify this feedback:

1. Current code uses `const` (ES6+)
2. Feedback suggests `var` (ES5)

3. Technical analysis:
   - `const`: Block-scoped, immutable binding, prevents reassignment bugs
   - `var`: Function-scoped, allows reassignment, hoisting issues

4. Project standards check:
   - .eslintrc: Enforces `const`/`let`, disallows `var`
   - Existing codebase: 100% uses `const`/`let`

5. Verification: Feedback appears INCORRECT
   - Contradicts project standards
   - Would cause linting failures
   - Downgrades code quality

6. Response to reviewer:
   "I checked our ESLint config and existing codebase. We enforce `const`/`let`
   and disallow `var` for block-scoping safety. Changing to `var` would:
   - Fail CI linting
   - Contradict project standards
   - Introduce potential hoisting bugs

   Did you mean something else, or is there a specific reason for `var` here?"

Waiting for reviewer clarification before making changes...
```

## Verification Gates

### Gate 1: Tests Must Pass

**Requirement**: Zero test failures

```
✅ PASS: 52 tests, 0 failures
❌ FAIL: 48 tests passed, 4 failures

If failures exist:
1. Run /ccpm:verify to debug
2. Update Linear with findings
3. Re-run verification after fixes
4. Only proceed when 0 failures
```

### Gate 2: Build Must Succeed

**Requirement**: Exit status 0

```
✅ PASS: Build completed successfully (exit 0)
❌ FAIL: Build failed with 3 TypeScript errors (exit 1)

If build fails:
1. Review error messages
2. Fix compilation errors
3. Re-run build
4. Only proceed when exit 0
```

### Gate 3: Linear Checklist Must Be Complete

**Requirement**: 100% of checklist items checked

```
✅ PASS: 7/7 checklist items complete
❌ FAIL: 5/7 checklist items complete

Incomplete items:
- [ ] Add integration tests
- [ ] Update API documentation

Action required:
1. Complete remaining items, OR
2. Update task scope (with /ccpm:plan)
3. Mark items as out-of-scope explicitly
4. Only proceed when justified
```

### Gate 4: No Unresolved Blockers

**Requirement**: Zero blocker comments in Linear

```
✅ PASS: No blockers found
❌ FAIL: 2 blockers in Linear comments

Blockers:
1. "Database migration failing in staging"
2. "Awaiting security team approval"

Action required:
1. Resolve each blocker, OR
2. Document why blocker doesn't apply
3. Get explicit approval to proceed despite blocker
4. Only proceed when all blockers addressed
```

## Integration with CCPM Safety Rules

### Works with `external-system-safety` Skill

This skill focuses on **verification**, `external-system-safety` focuses on **confirmation**.

**Division of responsibility:**

| Concern | Skill | Check |
|---------|-------|-------|
| Are tests passing? | ccpm-code-review | ✓ |
| Is build successful? | ccpm-code-review | ✓ |
| Is checklist complete? | ccpm-code-review | ✓ |
| User confirms Jira update? | external-system-safety | ✓ |
| User confirms Slack post? | external-system-safety | ✓ |

**Together they ensure**:
1. Quality standards met (ccpm-code-review)
2. User approves external writes (external-system-safety)

### References SAFETY_RULES.md

All external writes follow `../commands/SAFETY_RULES.md`:

**⛔ Require confirmation**:
- Jira status updates
- Confluence page edits
- BitBucket PR creation
- Slack notifications

**✅ Always allowed**:
- Linear status updates (internal tracking)
- Local file modifications
- Read operations

## Examples

### Example 1: Successful Completion Flow (Natural Workflow)

```
User: "Done with AUTH-123, ready to ship!"

Claude: [ccpm-code-review activates]

Let me run verification first:

Running /ccpm:verify AUTH-123...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 Smart Verify Command
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📋 Checklist: 100% (7/7 items)

═══════════════════════════════════════
Step 1/2: Running Quality Checks
═══════════════════════════════════════

✅ Tests: 52/52 passing
✅ Build: Successful (exit 0)
✅ Linting: No errors

═══════════════════════════════════════
Step 2/2: Running Final Verification
═══════════════════════════════════════

✅ Code review: Passed
✅ Security audit: Passed
✅ Requirements met: Passed

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ All Verification Complete!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

💡 What's Next?
⭐ Recommended: /ccpm:done AUTH-123

Ready to finalize! Shall I run /ccpm:done now?
```

### Example 2: Failed Verification (Natural Workflow)

```
User: "I'm done, let's merge"

Claude: [ccpm-code-review activates]

Running /ccpm:verify...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔍 Smart Verify Command
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

═══════════════════════════════════════
Step 1/2: Running Quality Checks
═══════════════════════════════════════

🧪 Running tests...
❌ 3 tests failing:
   - test/auth/login.test.ts: "should handle invalid credentials"
   - test/auth/jwt.test.ts: "should refresh expired tokens"
   - test/api/protected.test.ts: "should reject missing tokens"

🏗️  Running build...
❌ TypeScript error:
   src/auth/jwt.ts:45:12 - error TS2345

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
❌ Verification Failed
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

To debug and fix issues:
  /ccpm:verify AUTH-123

Then run verification again:
  /ccpm:verify AUTH-123

Would you like me to start debugging these issues?
```

### Example 3: Incomplete Checklist (Pre-Flight with /ccpm:done)

```
User: "/ccpm:done AUTH-456"

Claude: [ccpm-code-review activates for pre-flight]

✅ All git pre-flight checks passed!

Checking completion readiness:

✅ Tests passing (41/41)
✅ Build successful
❌ Linear checklist incomplete (4/6):
   ✓ Implement JWT generation
   ✓ Add login endpoint
   ✓ Add logout endpoint
   ✓ Write unit tests
   ✗ Add integration tests
   ✗ Update API documentation

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⛔ Cannot Finalize: Checklist Incomplete
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Progress: 67% (4/6 completed)

🔧 Actions Required:
1. Complete remaining checklist items
2. Update checklist: /ccpm:sync AUTH-456
3. Then run: /ccpm:done AUTH-456

Or update scope with /ccpm:plan AUTH-456 "mark items as Phase 2"

Which would you like to do?
```

### Example 4: Complete Natural Workflow (End-to-End)

```
User: "I finished the authentication feature"

Claude: [ccpm-code-review guides through workflow]

Great! Let's verify your work before finalizing.

Step 1: Verification
────────────────────
/ccpm:verify

✅ All quality checks passed
✅ Code review complete
✅ Security validated

Step 2: Finalization
────────────────────
/ccpm:done

✅ Git pre-flight checks passed
✅ Checklist complete (5/5)
✅ No blockers
📝 GitHub PR created
✅ Linear updated to Done

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎉 Task Finalized: AUTH-456
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

All done! Task completed successfully. 🎊
```

## Verification Checklist Template

Before claiming "done":

- [ ] Tests executed
  - [ ] All tests passing (0 failures)
  - [ ] Coverage meets requirements
  - [ ] New tests added for new code

- [ ] Build verified
  - [ ] Build successful (exit 0)
  - [ ] No compilation errors
  - [ ] No linting errors

- [ ] Linear task complete
  - [ ] All checklist items checked
  - [ ] No unresolved blockers
  - [ ] Work summary added

- [ ] Code quality
  - [ ] Code reviewed (by human or code-reviewer agent)
  - [ ] Security checked (if applicable)
  - [ ] Performance acceptable

- [ ] Documentation
  - [ ] Code comments added where needed
  - [ ] API docs updated (if API changed)
  - [ ] README updated (if user-facing)

Only after ALL checked:
- [ ] Ready for /ccpm:done

## Integration with Other CCPM Skills

**Works alongside**:

- **external-system-safety**: Enforces confirmation for external writes (Jira/Slack)
- **pm-workflow-guide**: Suggests `/ccpm:verify` and `/ccpm:done` at right time
- **ccpm-debugging**: Invoked via `/ccpm:verify` when checks fail
- **sequential-thinking**: For complex verification scenarios

**Example combined activation**:
```
User: "Ready to merge AUTH-123"
       ↓
ccpm-code-review → Suggests /ccpm:verify first
       ↓
/ccpm:verify → Runs quality checks + agent review
       ↓
[If gates pass]
       ↓
Suggests /ccpm:done
       ↓
/ccpm:done → Pre-flight checks + PR creation
       ↓
external-system-safety → Confirms Jira/Slack writes
       ↓
[If user confirms]
       ↓
Complete! ✅
```

## Natural Workflow Commands

CCPM provides streamlined commands for the complete verification and finalization workflow:

| Command | Purpose | Auto-detects Issue |
|---------|---------|-------------------|
| `/ccpm:verify` | Quality checks + agent review | ✅ From git branch |
| `/ccpm:done` | Pre-flight + PR + finalize | ✅ From git branch |
| `/ccpm:verify` | Debug failed checks | ❌ Explicit ID required |

**Recommended workflow:**
```bash
# 1. Complete implementation
/ccpm:work

# 2. Commit changes
/ccpm:commit

# 3. Verify quality (this skill activates)
/ccpm:verify

# 4. Finalize task (this skill activates for pre-flight)
/ccpm:done
```

## Summary

This skill ensures:

- ✅ No false completion claims
- ✅ Evidence required before "done"
- ✅ Quality gates enforced (4-step validation)
- ✅ Technical rigor over social comfort
- ✅ Integration with CCPM natural workflow
- ✅ Systematic debugging when failures occur

**Philosophy**: Verification before completion, evidence over claims, quality over speed.

**Key Features**:
- Auto-activates on completion attempts
- Enforces 4 verification gates
- Integrates with `/ccpm:verify` and `/ccpm:done`
- Suggests `/ccpm:verify` for failures
- Works with external-system-safety for confirmations

---

**Source**: Adapted from [claudekit-skills/code-review](https://github.com/mrgoonie/claudekit-skills)
**License**: MIT
**CCPM Integration**: `/ccpm:verify`, `/ccpm:done`, `/ccpm:verify`, quality-gate hook

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
