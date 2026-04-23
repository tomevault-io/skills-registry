---
name: tdd-security
description: Execute SECURITY phase of TDD for interactive development. OWASP Top 10 audit with MEDIUM+ threshold enforced (80% resolution rate). Called by /security-tdd command. Use when this capability is needed.
metadata:
  author: toonvos
---

# TDD SECURITY Phase Skill

Execute the complete SECURITY phase (OWASP audit & remediation) for interactive TDD development workflow.

## Invocation

```typescript
Skill(skill: "tdd-security", args: "[sprintdag-path]")
```

**Examples:**

```typescript
Skill(skill: "tdd-security", args: "tasks/sprints/sprint-7/day-05")
Skill(skill: "tdd-security", args: "tasks/sprints/sprint-7/day-02")
```

## Input Parsing

| Arg         | Format                         | Example                         |
| ----------- | ------------------------------ | ------------------------------- |
| `sprintdag` | `tasks/sprints/sprint-X/day-Y` | `tasks/sprints/sprint-7/day-05` |

**Note:** Feature and operations are read from `[sprintdag]/gates.json`.

## Output

- **Promise:** `<promise>TDD_SECURITY_COMPLETE</promise>`
- **Artifact:** `[sprintdag]/gates.json` updated with SECURITY phase gates
- **Progress:** `[sprintdag]/security/PROGRESS.md` with step-by-step completion log
- **Security report:** `[sprintdag]/security/security-audit-[date].md`

---

## MEDIUM+ Threshold (CRITICAL)

**This phase enforces MEDIUM+ security issue resolution:**

- **CRITICAL (P0)** → BLOCK, fix immediately
- **HIGH (P1)** → FIX before complete
- **MEDIUM (P2)** → FIX before complete
- **LOW (P3)** → SKIP, document in risk register

**Gate `GATE_SECURITY_MEDIUM_PLUS` stays FALSE until ≥80% MEDIUM+ issues resolved (with approved exceptions).**

---

## 80% Resolution Rate Requirement (NEW in v2.0) {#resolution-rate}

**Minimum 80% of detected MEDIUM+ issues must be RESOLVED (not skipped).**

```
resolution_rate = resolved / detected_medium_plus * 100

IF resolution_rate >= 80% → GATE_SECURITY_MEDIUM_PLUS = true
ELSE IF all OUT-OF-SCOPE approved by second agent → GATE_SECURITY_MEDIUM_PLUS = true
ELSE → GATE_SECURITY_MEDIUM_PLUS = false (BLOCKED)
```

**Example:**

- Detected: 10 MEDIUM+ issues
- Resolved: 8 (actually fixed)
- Out-of-scope: 2 (approved by second agent)
- Resolution rate: 80% ✅ PASS

**Why this matters:** Prevents "out of scope" becoming a catch-all excuse to skip work.

---

## OUT-OF-SCOPE Categories (STRICT) {#out-of-scope-categories}

**OUT-OF-SCOPE is NOT a free pass.** Only these categories are valid:

| Category                  | When Allowed                                     | Required Evidence                          |
| ------------------------- | ------------------------------------------------ | ------------------------------------------ |
| `TRANSITIVE_DEPENDENCY`   | npm audit finding in node_modules (not our code) | npm audit output showing transitive path   |
| `BLOCKED_BREAKING_CHANGE` | Fix requires breaking change to external API     | Impact analysis + ticket for future fix    |
| `AUDITED_ELSEWHERE`       | Component was audited in a DIFFERENT sprintdag   | **Link to specific audit report required** |
| `INFRASTRUCTURE_ONLY`     | Issue requires infrastructure change (not code)  | Infrastructure ticket number               |

**INVALID Out-of-Scope Justifications (REJECTED):**

- ❌ "Predates this sprintdag" - If issue is NEW, it must be fixed regardless of when code was written
- ❌ "Shared component" - Shared components belong to SOME sprintdag, identify which
- ❌ "Low impact" - All MEDIUM+ have impact by definition
- ❌ "UI only" - UI security issues (XSS, etc.) are real vulnerabilities
- ❌ "Following previous sprintdag pattern" - Each sprintdag judged independently
- ❌ "Not in target files" - If your code USES vulnerable component, you own the risk

**CRITICAL/HIGH have NO OUT-OF-SCOPE option** - must be fixed or blocked.

---

## Gate Variables

```
GATE_SECURITY_PREREQ_DONE    = false  # After Step 0
GATE_SECURITY_EXPLORE_DONE   = false  # After Step 1
GATE_SECURITY_PLAN_DONE      = false  # After Step 2
GATE_SECURITY_AUDIT_DONE     = false  # After Step 3
GATE_SECURITY_RISK_ASSESSED  = false  # After Step 4
GATE_SECURITY_SCOPE_APPROVED = false  # After Step 4a (NEW - second agent validates OUT-OF-SCOPE)
GATE_SECURITY_REMEDIATED     = false  # After Step 5 (or no MEDIUM+)
GATE_SECURITY_MEDIUM_PLUS    = false  # After ≥80% MEDIUM+ resolved
GATE_SECURITY_APPROVED       = false  # After final approval
```

---

## Step-by-Step Execution

### Step 0: PREREQUISITES VALIDATION [GATE: GATE_SECURITY_PREREQ_DONE]

**Task Tracking:**

```typescript
// Check if task already exists (from /security-tdd command)
TaskList(); // Find task with metadata.phase="SECURITY" and matching sprintdag

// If no task exists, create one with dependency on GREEN
const greenTask = TaskList().find(
  (t) =>
    t.metadata?.phase === "GREEN" && t.metadata?.sprintdag === "[sprintdag]",
);

TaskCreate({
  subject: "SECURITY: [feature]",
  description: "OWASP audit for [feature]",
  activeForm: "Executing SECURITY audit",
  metadata: {
    type: "phase",
    phase: "SECURITY",
    sprintdag: "[sprintdag]",
    feature: "[feature]",
    startedAt: "[ISO_TIMESTAMP]",
  },
});

// Set dependency and mark in_progress (REFACTOR is optional)
TaskUpdate(taskId, { addBlockedBy: [greenTask.id], status: "in_progress" });
```

**Read:** `[sprintdag]/gates.json`

- Verify: `green.GATE_GREEN_TESTS_PASS = true`
- Verify: `refactor.GATE_REFACTOR_MEDIUM_PLUS = true` (if REFACTOR phase done)

**Execute:**

```bash
# Verify implementation exists
ls app/src/server/**/*operations.ts

# Verify tests are GREEN (passing)
cd app && wasp test client run 2>&1 | grep -E "(FAIL|PASS)"
```

**After completion:**

- Update gates.json: `security.GATE_SECURITY_PREREQ_DONE = true`
- Initialize PROGRESS.md (see template below)

**Write:** `[sprintdag]/security/PROGRESS.md`

```markdown
# SECURITY Phase Progress: [FEATURE]

**Sprintdag:** [SPRINTDAG_PATH]
**Started:** [ISO_TIMESTAMP]
**Status:** IN_PROGRESS

## Step Completion Log

| Step | Description        | Status | Completed At | Notes                         |
| ---- | ------------------ | ------ | ------------ | ----------------------------- |
| 0    | Prerequisites      | ✅     | [TIMESTAMP]  | GREEN phase verified complete |
| 1    | Explore Security   | ⏳     | -            | -                             |
| 2    | Plan OWASP Audit   | ⏳     | -            | -                             |
| 3    | OWASP Top 10 Audit | ⏳     | -            | -                             |
| 4    | Risk Assessment    | ⏳     | -            | -                             |
| 4a   | Scope Validation   | ⏳     | -            | Second agent review           |
| 5    | Remediation Loop   | ⏳     | -            | -                             |
| 6    | Report Generation  | ⏳     | -            | -                             |
| 7    | Final Validation   | ⏳     | -            | -                             |
| 8    | Approval Decision  | ⏳     | -            | -                             |
| 9    | Git Commit         | ⏳     | -            | -                             |
| 10   | Output Promise     | ⏳     | -            | -                             |

## Gates Status

- [ ] GATE_SECURITY_PREREQ_DONE
- [ ] GATE_SECURITY_EXPLORE_DONE
- [ ] GATE_SECURITY_PLAN_DONE
- [ ] GATE_SECURITY_AUDIT_DONE
- [ ] GATE_SECURITY_RISK_ASSESSED
- [ ] GATE_SECURITY_SCOPE_APPROVED
- [ ] GATE_SECURITY_REMEDIATED
- [ ] GATE_SECURITY_MEDIUM_PLUS
- [ ] GATE_SECURITY_APPROVED

## Resolution Rate Tracking

- Detected MEDIUM+: [X]
- Resolved: [Y]
- Out-of-scope (approved): [Z]
- Resolution Rate: [Y/X * 100]% (≥80% required)
```

---

### Step 1: EXPLORE SECURITY CONTEXT [GATE: GATE_SECURITY_EXPLORE_DONE]

**Invoke:**

```
Task(
  subagent_type='Explore',
  model='haiku',
  prompt="""
  Analyze [FEATURE] implementation for security context:

  1. Identify all authentication checkpoints
  2. Find authorization/permission checks
  3. Check input validation patterns
  4. Review data flow (user input → database)
  5. Look for secrets/credentials handling
  6. Check multi-tenant data isolation
  """
)
```

**After completion:**

- Update gates.json: `security.GATE_SECURITY_EXPLORE_DONE = true`
- Update PROGRESS.md: Step 1 → ✅, note security checkpoints found

---

### Step 2: PLAN OWASP AUDIT STRATEGY [GATE: GATE_SECURITY_PLAN_DONE]

**Invoke:**

```
Task(
  subagent_type='Plan',
  model='sonnet',
  prompt="""
  Create OWASP Top 10 audit plan for [FEATURE]:

  For each OWASP category, identify:
  - Relevant code locations (files, functions)
  - Expected findings based on pattern analysis
  - Specific checks to perform
  - Test scenarios if issues found

  Priority:
  1. A01 Broken Access Control (most common)
  2. A03 Injection (high impact)
  3. A07 Auth Failures (critical)
  4. Others based on feature type

  OUTPUT: security-audit-plan.md
  """
)
```

**After completion:**

- Update gates.json: `security.GATE_SECURITY_PLAN_DONE = true`
- Update PROGRESS.md: Step 2 → ✅, note OWASP categories prioritized
- Write `[sprintdag]/security/security-audit-plan.md`

---

### Step 3: OWASP TOP 10 AUDIT [GATE: GATE_SECURITY_AUDIT_DONE]

**Invoke:**

```
Task(
  subagent_type='wasp-security-auditor',
  model='opus',
  prompt="""
  Comprehensive OWASP Top 10 compliance check for [FEATURE]:

  A01: Broken Access Control
  → Check: context.user validation, permission checks, multi-tenant isolation

  A02: Cryptographic Failures
  → Check: Passwords hashed (Wasp auth), secrets in .env.server, HTTPS

  A03: Injection
  → Check: Prisma ORM (no raw SQL), input validation (Zod schemas)

  A04: Insecure Design
  → Check: Permission design, secure defaults, rate limiting

  A05: Security Misconfiguration
  → Check: Debug mode off, error messages sanitized

  A06: Vulnerable Components
  → Check: npm audit, known CVEs

  A07: Authentication Failures
  → Check: Session management, password policy

  A08: Software Integrity
  → Check: CI/CD security, code signing

  A09: Security Logging
  → Check: Auth failures logged, security events

  A10: SSRF
  → Check: URL validation, whitelist

  OUTPUT per category:
  - PASS/FAIL status
  - Findings (if FAIL)
  - Severity: CRITICAL/HIGH/MEDIUM/LOW
  - Recommendation
  """
)
```

**After completion:**

- Update gates.json: `security.GATE_SECURITY_AUDIT_DONE = true`
- Update PROGRESS.md: Step 3 → ✅, note PASS/FAIL per category

---

### Step 4: RISK ASSESSMENT [GATE: GATE_SECURITY_RISK_ASSESSED]

**Categorize all findings:**

```
CRITICAL (P0): Block merge
- Unauthenticated access to sensitive data
- SQL injection
- Hardcoded credentials

HIGH (P1): Fix before merge
- Missing auth check on sensitive operation
- Cross-tenant data leakage
- Insecure direct object reference

MEDIUM (P2): Fix before merge
- Missing rate limiting
- Overly permissive CORS
- Missing input validation (non-critical)

LOW (P3): Document only
- Missing security headers
- Verbose error messages (dev mode)
- Missing audit logging
```

**Write:** `[sprintdag]/security/security-risks.json`

**After completion:**

- Update gates.json: `security.GATE_SECURITY_RISK_ASSESSED = true`
- Update PROGRESS.md: Step 4 → ✅, note CRITICAL/HIGH/MEDIUM/LOW counts

---

### Step 4a: SCOPE VALIDATION [GATE: GATE_SECURITY_SCOPE_APPROVED] (NEW in v2.0)

**Purpose:** Validate any OUT-OF-SCOPE decisions with a second agent to prevent unjustified skips.

**Blocked until:** GATE_SECURITY_RISK_ASSESSED = true

**IF any MEDIUM issues marked as OUT-OF-SCOPE:**

**Invoke:**

```
Task(
  subagent_type='wasp-security-auditor',
  model='sonnet',
  prompt="""
  VALIDATE OUT-OF-SCOPE decisions for [FEATURE]:

  Issues proposed for OUT-OF-SCOPE:
  [LIST FROM security-risks.json]

  FOR EACH issue, verify:
  1. Category is VALID (only 4 allowed: TRANSITIVE_DEPENDENCY, BLOCKED_BREAKING_CHANGE, AUDITED_ELSEWHERE, INFRASTRUCTURE_ONLY)
  2. Evidence is PROVIDED and VERIFIABLE
  3. Justification is NOT on invalid list:
     - ❌ "Predates this sprintdag"
     - ❌ "Shared component"
     - ❌ "Low impact"
     - ❌ "UI only"
     - ❌ "Following previous pattern"
     - ❌ "Not in target files"

  OUTPUT per issue:
  - "APPROVED: [category] - [evidence verified]"
  - "REJECTED: [reason] - Must be fixed in this sprintdag"

  CRITICAL/HIGH severity items CANNOT be out-of-scope.
  """
)
```

**Decision Matrix:**

| Agent 1 (Audit) | Agent 2 (Validation) | Result          |
| --------------- | -------------------- | --------------- |
| OUT-OF-SCOPE    | APPROVED             | ✅ Skip allowed |
| OUT-OF-SCOPE    | REJECTED             | ❌ Must fix     |
| FIX             | N/A                  | Must fix        |

**After completion:**

- Update gates.json: `security.GATE_SECURITY_SCOPE_APPROVED = true`
- Write `[sprintdag]/security/scope-approvals.md` with validation results
- Update PROGRESS.md: Step 4a → ✅, note approved/rejected counts

**IF no OUT-OF-SCOPE decisions:**

- Auto-approve: `security.GATE_SECURITY_SCOPE_APPROVED = true`
- Note in PROGRESS.md: "No OUT-OF-SCOPE items - gate auto-approved"

---

### Step 5: REMEDIATION LOOP [GATE: GATE_SECURITY_REMEDIATED]

**Blocked until:** GATE_SECURITY_SCOPE_APPROVED = true

**FOR EACH CRITICAL/HIGH/MEDIUM finding (NOT approved as OUT-OF-SCOPE):**

```
1. Generate fix:
   Task(
     subagent_type='wasp-code-generator',
     model='haiku',
     prompt="Generate security fix for: [finding]"
   )

2. Add security test:
   Task(
     subagent_type='wasp-test-automator',
     model='haiku',
     prompt="Add security test for: [fix]"
   )

3. Verify fix:
   → Run tests: cd app && wasp test client run
   → Expected: All GREEN

4. Commit:
   → git commit -m "fix(security): [description]"

5. Update metrics:
   → Increment resolved_count
```

**Track metrics during loop:**

```
detected_medium_plus = [total MEDIUM+ from Step 4]
resolved = [count of actually fixed issues]
out_of_scope_approved = [count from Step 4a]
```

**After all CRITICAL/HIGH/MEDIUM processed:**

- Calculate resolution rate: `resolved / detected_medium_plus * 100`
- Update gates.json: `security.GATE_SECURITY_REMEDIATED = true`
- Update PROGRESS.md: Step 5 → ✅, note fixes applied and resolution rate

---

### Step 6: REPORT GENERATION

**Write to `[sprintdag]/security/`:**

- `security-audit-[YYYY-MM-DD].md` - Full OWASP audit results
- `owasp-compliance.md` - Compliance matrix
- `security-risks.json` - Risk register
- `remediation-log.md` - All fixes applied
- `scope-approvals.md` - OUT-OF-SCOPE validation results (if any)

**Report format:**

```markdown
# Security Audit Report: [FEATURE]

**Date:** [YYYY-MM-DD]
**Auditor:** wasp-security-auditor (Claude)

## Executive Summary

- CRITICAL: 0 (must be 0)
- HIGH: 0 (must be 0)
- MEDIUM: 0 (must be 0)
- LOW: X (documented, accepted)

## OWASP Compliance Matrix

| Category | Status | Findings | Remediation |
| -------- | ------ | -------- | ----------- |
| A01      | PASS   | -        | -           |
| ...      | ...    | ...      | ...         |
```

**After completion:**

- Update PROGRESS.md: Step 6 → ✅, note reports written

---

### Step 7: FINAL VALIDATION

**Execute:**

```bash
# Verify ALL tests still GREEN (security tests added)
cd app && wasp test client run

# Run npm audit for dependency vulnerabilities
cd app && npm audit --audit-level=high
```

**After completion:**

- Update PROGRESS.md: Step 7 → ✅, note test and audit status

---

### Step 8: APPROVAL DECISION [GATE: GATE_SECURITY_APPROVED]

**Calculate final resolution rate:**

```
resolution_rate = resolved / detected_medium_plus * 100

SECURITY APPROVAL MATRIX:
- CRITICAL remaining: 0 (MUST be 0 - no exceptions)
- HIGH remaining: 0 (MUST be 0 - no exceptions)
- Resolution rate: ≥80% (MEDIUM+ issues)
- LOW remaining: X (OK, documented)

IF CRITICAL > 0 → REJECTED (no exceptions)
IF HIGH > 0 → REJECTED (no exceptions)
IF resolution_rate < 80% AND unresolved not approved → REJECTED
IF resolution_rate >= 80% OR all unresolved approved → APPROVED
```

**After approval:**

- Update gates.json: `security.GATE_SECURITY_MEDIUM_PLUS = true`
- Update gates.json: `security.GATE_SECURITY_APPROVED = true`
- Update PROGRESS.md: Step 8 → ✅, note APPROVED status and resolution rate

---

### Step 9: GIT COMMIT

**Execute:**

```bash
git add [sprintdag]/security/ [sprintdag]/gates.json
git commit -m "docs: Add security audit [FEATURE]

Security audit complete:
- OWASP Top 10 compliance verified
- Resolution rate: [X]% (≥80% required)
- MEDIUM+ resolved: [Y]/[Z]
- Security tests added

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

**After completion:**

- Update PROGRESS.md: Step 9 → ✅, note commit hash

---

### Step 10: OUTPUT PROMISE

**Update `[sprintdag]/gates.json`:**

```json
{
  "security": {
    "GATE_SECURITY_PREREQ_DONE": true,
    "GATE_SECURITY_EXPLORE_DONE": true,
    "GATE_SECURITY_PLAN_DONE": true,
    "GATE_SECURITY_AUDIT_DONE": true,
    "GATE_SECURITY_RISK_ASSESSED": true,
    "GATE_SECURITY_SCOPE_APPROVED": true,
    "GATE_SECURITY_REMEDIATED": true,
    "GATE_SECURITY_MEDIUM_PLUS": true,
    "GATE_SECURITY_APPROVED": true,
    "metrics": {
      "detected_medium_plus": "[X]",
      "resolved": "[Y]",
      "out_of_scope_approved": "[Z]",
      "resolution_rate": "[Y/X * 100]%"
    },
    "out_of_scope": [
      {
        "id": "[ID]",
        "category": "[VALID_CATEGORY]",
        "title": "[ISSUE]",
        "approved_by": "wasp-security-auditor (sonnet)",
        "evidence": "[LINK/TICKET]"
      }
    ],
    "started_at": "[ISO_TIMESTAMP]",
    "completed_at": "[ISO_TIMESTAMP]"
  }
}
```

**Output:**

```
TDD SECURITY PHASE COMPLETE: [FEATURE]

✅ OWASP Top 10: All categories audited
✅ Resolution rate: [X]% (≥80% required)
✅ MEDIUM+ resolved: [Y]/[Z]
✅ Out-of-scope approved: [W] (validated by second agent)
✅ CRITICAL: 0, HIGH: 0
✅ LOW: X (documented, accepted)
✅ Security tests added
✅ All 9 gates: ✅✅✅✅✅✅✅✅✅
✅ Artifacts: [sprintdag]/security/

<promise>TDD_SECURITY_COMPLETE</promise>
```

**Task Tracking (on completion):**

```typescript
TaskUpdate(taskId, {
  status: "completed",
  metadata: {
    completedAt: "[ISO_TIMESTAMP]",
    owaspCompliance: "PASS",
    criticalIssues: 0,
    highIssues: 0,
    mediumIssues: 0,
    lowIssues: "[X]",
    securityTestsAdded: "[Y]",
    reportPath: "[sprintdag]/security/security-audit-[date].md",
    gatesPath: "[sprintdag]/gates.json",
  },
});
```

**After completion:**

- Update PROGRESS.md: Step 10 → ✅, Status → COMPLETE
- Update PROGRESS.md Gates Status: All checkboxes → [x]

---

## Error Recovery

**If resolution rate < 80% AND unresolved not approved:**

```
⚠️ MEDIUM+ resolution rate below threshold:
- Detected: [X]
- Resolved: [Y]
- Resolution rate: [Y/X]% (80% required)

Unresolved issues without approved OUT-OF-SCOPE:
[list]

VALID OUT-OF-SCOPE categories (require evidence):
- TRANSITIVE_DEPENDENCY (npm audit path)
- BLOCKED_BREAKING_CHANGE (impact analysis + ticket)
- AUDITED_ELSEWHERE (link to audit report)
- INFRASTRUCTURE_ONLY (infra ticket)

INVALID justifications (will be rejected):
- "Predates this sprintdag"
- "Shared component"
- "Low impact"
- "UI only"

Cannot mark feature complete.
Resolve issues or provide valid OUT-OF-SCOPE evidence.

<promise>TDD_SECURITY_BLOCKED</promise>
```

**If CRITICAL/HIGH issues remain:**

```
⚠️ CRITICAL/HIGH security issues cannot be skipped:
[list]

These severity levels have NO OUT-OF-SCOPE option.
Must fix before proceeding.

<promise>TDD_SECURITY_BLOCKED</promise>
```

---

## Resume from Crash

**If skill was interrupted, check `[sprintdag]/gates.json`:**

1. Read gates.json to find last completed gate
2. Read PROGRESS.md to understand state
3. Resume from next incomplete step

---

## Agent Reference

| Step | Agent                 | Model  | Purpose                     | Gate                         |
| ---- | --------------------- | ------ | --------------------------- | ---------------------------- |
| 1    | Explore (built-in)    | haiku  | Gather security context     | GATE_SECURITY_EXPLORE_DONE   |
| 2    | Plan (built-in)       | sonnet | OWASP checklist design      | GATE_SECURITY_PLAN_DONE      |
| 3    | wasp-security-auditor | opus   | OWASP Top 10 audit          | GATE_SECURITY_AUDIT_DONE     |
| 4a   | wasp-security-auditor | sonnet | Validate OUT-OF-SCOPE (NEW) | GATE_SECURITY_SCOPE_APPROVED |
| 5    | wasp-code-generator   | haiku  | Generate security fixes     | GATE_SECURITY_REMEDIATED     |
| 5    | wasp-test-automator   | haiku  | Add security tests          | GATE_SECURITY_REMEDIATED     |

---

## Cross-Phase Integration

**Reads from prior phases:**

- `[sprintdag]/gates.json` - Feature, phase status
- `[sprintdag]/tests/test-plan.md` - Expected security scenarios
- `[sprintdag]/implementation/implementation-notes.md` - Implementation details
- `[sprintdag]/refactor/refactor-report.md` - Final code state (if REFACTOR done)

**Creates for completion:**

- `[sprintdag]/security/security-audit-[date].md` - Complete audit
- `[sprintdag]/security/security-risks.json` - Risk register with metrics
- `[sprintdag]/security/scope-approvals.md` - OUT-OF-SCOPE validation (if any)
- `[sprintdag]/gates.json` - SECURITY phase completion with resolution rate

---

## Precedent Chain Prevention (CRITICAL)

**Each sprintdag is judged INDEPENDENTLY.** Do NOT use previous sprintdag decisions as precedent.

**Invalid reasoning patterns:**

- "Sprintdag-03 skipped this, so sprintdag-04 can skip it too" ❌
- "We've been marking this as out-of-scope" ❌
- "Following the pattern from previous sprintdags" ❌

**Valid reasoning:**

- "This issue is AUDITED_ELSEWHERE in sprintdag-02 security report (link)" ✅
- "npm audit shows this is TRANSITIVE_DEPENDENCY: lodash→glob→minimatch" ✅

**Why:** Prevents accumulating tech debt through cascading skips.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
