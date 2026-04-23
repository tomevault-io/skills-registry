---
name: ralph-security
description: Execute SECURITY phase of TDD for Ralph automation. OWASP Top 10 audit with MEDIUM+ threshold enforced (80% resolution rate). Use via Skill tool in Ralph loops. Use when this capability is needed.
metadata:
  author: toonvos
---

# Ralph SECURITY Phase Skill

Execute the complete SECURITY phase (OWASP audit & remediation) as part of autonomous Ralph TDD workflow.

## Invocation

```typescript
Skill(skill: "ralph-security", args: "[fase-dir]")
```

**Examples:**

```typescript
Skill(skill: "ralph-security", args: "fase-01")
Skill(skill: "ralph-security", args: "fase-02")
```

## Input Parsing

| Arg        | Format    | Example   |
| ---------- | --------- | --------- |
| `fase-dir` | `fase-XX` | `fase-01` |

**Note:** Feature and operations are read from `[fase-dir]/gates.json`.

## Output

- **Promise:** `<promise>SECURITY_PHASE_COMPLETE</promise>`
- **Artifact:** `[fase-dir]/gates.json` updated with SECURITY phase gates
- **Progress:** `[fase-dir]/security/PROGRESS.md` with step-by-step completion log
- **Security report:** `[fase-dir]/security/security-audit-[date].md`

---

## MEDIUM+ Threshold (CRITICAL)

**This phase enforces MEDIUM+ security issue resolution:**

- **CRITICAL (P0)** → BLOCK, fix immediately
- **HIGH (P1)** → FIX before fase complete
- **MEDIUM (P2)** → FIX before fase complete
- **LOW (P3)** → SKIP, document in risk register

**Gate `GATE_SECURITY_MEDIUM_PLUS` requires:**

1. **Resolution rate ≥ 80%** of detected MEDIUM+ issues actually resolved
2. **OR** All remaining OUT-OF-SCOPE items approved via Step 4a validation

---

## OUT-OF-SCOPE Categories (STRICT) {#out-of-scope-categories}

**OUT-OF-SCOPE is NOT a free pass.** Only these categories are valid:

| Category                  | When Allowed                                     | Required Evidence                          |
| ------------------------- | ------------------------------------------------ | ------------------------------------------ |
| `TRANSITIVE_DEPENDENCY`   | npm audit finding in node_modules (not our code) | npm audit output showing transitive path   |
| `BLOCKED_BREAKING_CHANGE` | Fix requires breaking change to external API     | Impact analysis + ticket for future fix    |
| `AUDITED_ELSEWHERE`       | Component was audited in a DIFFERENT fase        | **Link to specific audit report required** |
| `INFRASTRUCTURE_ONLY`     | Issue requires infrastructure change (not code)  | Infrastructure ticket number               |

**INVALID Out-of-Scope Justifications (REJECTED):**

- ❌ "Predates this fase" - If issue is NEW, it must be fixed regardless of when code was written
- ❌ "Shared component" - Shared components belong to SOME fase, identify which
- ❌ "Low impact" - All MEDIUM+ have impact by definition
- ❌ "UI only" - UI security issues (XSS, etc.) are real vulnerabilities
- ❌ "Following previous fase pattern" - Each fase judged independently
- ❌ "Not in target files" - If your code USES vulnerable component, you own the risk

**CRITICAL: Precedent Chain Prevention**

```
⚠️ EACH FASE IS JUDGED INDEPENDENTLY

"Following Fase-X pattern" is NOT a valid justification.
"Previous fase marked as out-of-scope" is NOT a valid justification.
Evaluate each finding on its own merit for THIS fase.

If a MEDIUM+ issue is found in code YOUR fase touches, YOU must either:
1. FIX it, or
2. Provide valid OUT-OF-SCOPE category with evidence
```

---

## Gate Variables

```
GATE_SECURITY_PREREQ_DONE    = false  # After Step 0
GATE_SECURITY_EXPLORE_DONE   = false  # After Step 1
GATE_SECURITY_PLAN_DONE      = false  # After Step 2
GATE_SECURITY_AUDIT_DONE     = false  # After Step 3
GATE_SECURITY_RISK_ASSESSED  = false  # After Step 4
GATE_SECURITY_SCOPE_APPROVED = false  # After Step 4a (if OUT-OF-SCOPE items exist)
GATE_SECURITY_REMEDIATED     = false  # After Step 5 (or no MEDIUM+)
GATE_SECURITY_MEDIUM_PLUS    = false  # After 80% resolution OR all OUT-OF-SCOPE approved
GATE_SECURITY_APPROVED       = false  # After final approval
```

---

## Step-by-Step Execution

### Step 0: PREREQUISITES VALIDATION [GATE: GATE_SECURITY_PREREQ_DONE]

**Read:** `[fase-dir]/gates.json`

- Verify: `green.GATE_GREEN_TESTS_PASS = true`
- Verify: `refactor.GATE_REFACTOR_MEDIUM_PLUS = true`

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
- TodoWrite: Mark Step 0 completed

**Write:** `[fase-dir]/security/PROGRESS.md`

```markdown
# SECURITY Phase Progress - Fase [FASE_NR]: [FEATURE]

**Started:** [ISO_TIMESTAMP]
**Status:** IN_PROGRESS

## Step Completion Log

| Step | Description        | Status | Completed At | Notes                                     |
| ---- | ------------------ | ------ | ------------ | ----------------------------------------- |
| 0    | Prerequisites      | ✅     | [TIMESTAMP]  | GREEN + REFACTOR phases verified complete |
| 1    | Explore Security   | ⏳     | -            | -                                         |
| 2    | Plan OWASP Audit   | ⏳     | -            | -                                         |
| 3    | OWASP Top 10 Audit | ⏳     | -            | -                                         |
| 4    | Risk Assessment    | ⏳     | -            | -                                         |
| 4a   | Scope Validation   | ⏳     | -            | -                                         |
| 5    | Remediation Loop   | ⏳     | -            | -                                         |
| 6    | Report Generation  | ⏳     | -            | -                                         |
| 7    | Final Validation   | ⏳     | -            | -                                         |
| 8    | Approval Decision  | ⏳     | -            | -                                         |
| 9    | Git Commit         | ⏳     | -            | -                                         |
| 10   | Output Promise     | ⏳     | -            | -                                         |

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
```

---

### Step 1: EXPLORE SECURITY CONTEXT [GATE: GATE_SECURITY_EXPLORE_DONE]

**Invoke:**

```
Task(
  subagent_type='Explore',
  model='haiku',
  thoroughness='medium',
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
- TodoWrite: Mark Step 1 completed

---

### Step 2: PLAN OWASP AUDIT STRATEGY [GATE: GATE_SECURITY_PLAN_DONE]

**Invoke:**

```
Task(
  subagent_type='Plan',
  model='sonnet',
  prompt="""
  Create OWASP Top 10 audit plan for [FEATURE] Fase [FASE_NR]:

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
- Write `[fase-dir]/security/security-audit-plan.md`
- TodoWrite: Mark Step 2 completed

---

### Step 3: OWASP TOP 10 AUDIT [GATE: GATE_SECURITY_AUDIT_DONE]

**Invoke:**

```
Task(
  subagent_type='wasp-security-auditor',
  model='opus',
  prompt="""
  Comprehensive OWASP Top 10 compliance check for [FEATURE] Fase [FASE_NR]:

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
- TodoWrite: Mark Step 3 completed

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

**Write:** `[fase-dir]/security/security-risks.json`

**Track metrics:**

```json
{
  "detected_medium_plus": 0,
  "in_scope": 0,
  "out_of_scope": []
}
```

**After completion:**

- Update gates.json: `security.GATE_SECURITY_RISK_ASSESSED = true`
- Update gates.json: `security.metrics.detected_medium_plus = [count]`
- Update PROGRESS.md: Step 4 → ✅, note CRITICAL/HIGH/MEDIUM/LOW counts
- TodoWrite: Mark Step 4 completed

---

### Step 4a: SCOPE VALIDATION [GATE: GATE_SECURITY_SCOPE_APPROVED] (NEW)

**ONLY if any MEDIUM+ findings were marked as OUT-OF-SCOPE:**

**Validate each OUT-OF-SCOPE has valid category:**

```
FOR EACH out-of-scope finding:
  IF category NOT IN [TRANSITIVE_DEPENDENCY, BLOCKED_BREAKING_CHANGE, AUDITED_ELSEWHERE, INFRASTRUCTURE_ONLY]:
    → REJECT scope exclusion
    → Must fix or provide valid category

  IF category == AUDITED_ELSEWHERE:
    → Verify link to audit report exists
    → If no link → REJECT scope exclusion

  IF category == BLOCKED_BREAKING_CHANGE:
    → Verify ticket number exists
    → If no ticket → REJECT scope exclusion
```

**Invoke second agent review:**

```
Task(
  subagent_type='wasp-security-auditor',
  model='sonnet',
  prompt="""
  REVIEW OUT-OF-SCOPE JUSTIFICATIONS for [FEATURE] Fase [FASE_NR]:

  OUT-OF-SCOPE FINDINGS:
  [list from Step 4]

  VALIDATION CHECKLIST (for each item):
  □ Category is valid (TRANSITIVE_DEPENDENCY, BLOCKED_BREAKING_CHANGE, AUDITED_ELSEWHERE, INFRASTRUCTURE_ONLY)
  □ Evidence provided matches category requirements
  □ Item is NOT using invalid justification (predates fase, shared component, low impact, etc.)
  □ Item is NOT referencing "previous fase pattern"
  □ If code YOUR fase touches uses vulnerable component, the risk is YOURS

  CRITICAL QUESTION for each "predates this fase" claim:
  - Was this issue NEWLY DISCOVERED in this audit?
  - If YES → It must be fixed, regardless of when code was written
  - If NO → Provide link to prior audit where it was documented

  OUTPUT one of:
  - "ALL SCOPE EXCLUSIONS APPROVED: [brief rationale per item]"
  - "SCOPE EXCLUSIONS REJECTED: [list of rejected items with reason]. Must fix or provide valid category."
  """
)
```

**If REJECTED:**

- Return to Step 5 to fix rejected items
- Max 2 iterations, then escalate

**If APPROVED (or no OUT-OF-SCOPE):**

- Update gates.json: `security.GATE_SECURITY_SCOPE_APPROVED = true`
- Update PROGRESS.md: Step 4a → ✅, note scope review status
- TodoWrite: Mark Step 4a completed

---

### Step 5: REMEDIATION LOOP [GATE: GATE_SECURITY_REMEDIATED]

**FOR EACH CRITICAL/HIGH/MEDIUM finding:**

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
```

**Track metrics during execution:**

```json
{
  "detected_medium_plus": 6,
  "resolved": 0,
  "out_of_scope_approved": []
}
```

**After all CRITICAL/HIGH/MEDIUM resolved (or approved out-of-scope):**

- Update gates.json: `security.GATE_SECURITY_REMEDIATED = true`
- Update gates.json: `security.metrics.resolved = [count]`
- Update PROGRESS.md: Step 5 → ✅, note fixes applied
- TodoWrite: Mark Step 5 completed

---

### Step 6: REPORT GENERATION

**Write to `[fase-dir]/security/`:**

- `security-audit-[YYYY-MM-DD].md` - Full OWASP audit results
- `owasp-compliance.md` - Compliance matrix
- `security-risks.json` - Risk register with metrics
- `remediation-log.md` - All fixes applied
- `scope-approvals.md` - Approved OUT-OF-SCOPE items with categories and evidence (if any)

**Report format:**

```markdown
# Security Audit Report - [FEATURE] Fase [FASE_NR]

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
- TodoWrite: Mark Step 6 completed

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
- TodoWrite: Mark Step 7 completed

---

### Step 8: APPROVAL DECISION [GATE: GATE_SECURITY_APPROVED]

**Calculate resolution rate:**

```
resolution_rate = resolved / detected_medium_plus * 100

IF resolution_rate >= 80%:
  → GATE_SECURITY_MEDIUM_PLUS = true

ELSE IF resolution_rate < 80% AND all OUT-OF-SCOPE approved:
  → GATE_SECURITY_MEDIUM_PLUS = true (via approved exceptions)

ELSE:
  → GATE_SECURITY_MEDIUM_PLUS = false
  → REJECTED - Cannot proceed
```

```
SECURITY APPROVAL MATRIX:
- CRITICAL remaining: 0 (MUST be 0)
- HIGH remaining: 0 (MUST be 0)
- MEDIUM remaining: 0 (MUST be 0, or approved OUT-OF-SCOPE)
- LOW remaining: X (OK, documented)
- Resolution rate: ≥ 80% or all exceptions approved

IF CRITICAL/HIGH > 0 → REJECTED (no exceptions)
IF MEDIUM > 0 AND not approved OUT-OF-SCOPE → REJECTED
IF resolution_rate < 80% AND OUT-OF-SCOPE not approved → REJECTED
OTHERWISE → APPROVED
```

**After approval:**

- Update gates.json: `security.GATE_SECURITY_APPROVED = true`
- Update gates.json: `security.GATE_SECURITY_MEDIUM_PLUS = true`
- Update gates.json: `security.metrics.resolution_rate = "[X]%"`
- Update PROGRESS.md: Step 8 → ✅, note APPROVED status and resolution rate
- TodoWrite: Mark Step 8 completed

---

### Step 9: GIT COMMIT

**Execute:**

```bash
git add [fase-dir]/security/ [fase-dir]/gates.json
git commit -m "docs: Add security audit [FEATURE] - Fase [FASE_NR]

Security audit complete:
- OWASP Top 10 compliance verified
- Resolution rate: [X]% ([resolved]/[detected] MEDIUM+ issues)
- Security tests added

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>"
```

**After completion:**

- Update PROGRESS.md: Step 9 → ✅, note commit hash
- TodoWrite: Mark Step 9 completed

---

### Step 10: OUTPUT PROMISE

**Update `[fase-dir]/gates.json`:**

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
    "started_at": "[ISO_TIMESTAMP]",
    "completed_at": "[ISO_TIMESTAMP]",
    "metrics": {
      "detected_medium_plus": 3,
      "resolved": 2,
      "out_of_scope_approved": 1,
      "resolution_rate": "67%"
    },
    "out_of_scope": [
      {
        "finding_id": "SEC-001",
        "category": "TRANSITIVE_DEPENDENCY",
        "evidence": "npm audit path: axios → @sendgrid/client",
        "approved_by": "wasp-security-auditor"
      }
    ]
  }
}
```

**Task Tracking:**

```typescript
// Mark fase task as COMPLETED - full TDD cycle done!
TaskUpdate(faseTaskId, {
  status: "completed",
  metadata: {
    ...existing,
    currentPhase: "COMPLETE",
    completedAt: "[ISO_TIMESTAMP]",
  },
});
// Note: Next fase automatically unblocked via blockedBy dependency chain
```

**Output:**

```
SECURITY PHASE COMPLETE - Fase [FASE_NR]: [FEATURE]

✅ OWASP Top 10: All categories audited
✅ CRITICAL: 0, HIGH: 0, MEDIUM: 0 (or approved OUT-OF-SCOPE)
✅ LOW: X (documented, accepted)
✅ Resolution rate: [X]% ([resolved]/[detected])
✅ OUT-OF-SCOPE approved: [count] (with valid categories)
✅ Security tests added
✅ All 9 gates: ✅✅✅✅✅✅✅✅✅
✅ Artifacts: [fase-dir]/security/

<promise>SECURITY_PHASE_COMPLETE</promise>
```

**After completion:**

- Update PROGRESS.md: Step 10 → ✅, Status → COMPLETE
- Update PROGRESS.md Gates Status: All checkboxes → [x]

---

### Step 11: AUTO-CONTINUE TO NEXT FASE (CRITICAL)

**ONLY if `<promise>SECURITY_PHASE_COMPLETE</promise>` was output (NOT BLOCKED):**

#### Step 11.1: Output fase completion

```
<promise>FASE_[N]_COMPLETE</promise>
```

#### Step 11.2: Detect next fase

**Execute:**

```bash
# Get current fase number from gates.json
CURRENT_FASE=$(cat [fase-dir]/gates.json | grep '"fase"' | grep -oE '[0-9]+')

# Calculate next fase
NEXT_FASE=$(printf "%02d" $((10#$CURRENT_FASE + 1)))

# Check if next fase exists
ls -d [parent-dir]/fase-$NEXT_FASE 2>/dev/null
```

#### Step 11.3: Continue or Complete

**If next fase EXISTS:**

1. Read `[parent-dir]/fase-$NEXT_FASE/README.md` for feature name and operations
2. Invoke:
   ```
   Skill(skill: "ralph-red", args: "[parent-dir]/fase-$NEXT_FASE '[Feature from README]' ops:[ops from README]")
   ```

**If NO more fases:**

1. Output workflow completion:

   ```
   ALL FASES COMPLETE

   Summary:
   - Fase 01: ✅ RED → GREEN → REFACTOR → SECURITY
   - Fase 02: ✅ RED → GREEN → REFACTOR → SECURITY
   - ...

   <promise>ALL_FASES_COMPLETE</promise>
   ```

**If `<promise>SECURITY_PHASE_BLOCKED</promise>` was output:**

- Do NOT auto-continue
- Wait for user to resolve the blocker
- User can manually resume with `/ralph-security [fase-dir]` or proceed to next fase manually

---

## Error Recovery

**If resolution rate < 80% AND OUT-OF-SCOPE not approved:**

```
⚠️ SECURITY PHASE BLOCKED

Resolution rate: [X]% (below 80% threshold)
Detected: [N] MEDIUM+ issues
Resolved: [M]
Out-of-scope (unapproved): [K]

REQUIRED ACTIONS:
1. Resolve more MEDIUM+ issues to reach 80%, OR
2. Provide valid OUT-OF-SCOPE categories for remaining items

Invalid out-of-scope justifications:
- "Predates this fase" ❌
- "Shared component" ❌
- "Low impact" ❌
- "UI only" ❌
- "Following previous fase" ❌
- "Not in target files" ❌

Valid out-of-scope categories:
- TRANSITIVE_DEPENDENCY (with npm audit output)
- BLOCKED_BREAKING_CHANGE (with ticket number)
- AUDITED_ELSEWHERE (with link to audit report)
- INFRASTRUCTURE_ONLY (with infrastructure ticket)

<promise>SECURITY_PHASE_BLOCKED</promise>
```

**If CRITICAL or HIGH issues remain (no exceptions allowed):**

```
⚠️ SECURITY PHASE BLOCKED - CRITICAL/HIGH ISSUES

CRITICAL: [N] remaining (MUST be 0)
HIGH: [M] remaining (MUST be 0)

These severity levels have NO OUT-OF-SCOPE option.
Must fix before fase can complete.

<promise>SECURITY_PHASE_BLOCKED</promise>
```

---

## Agent Reference

| Step | Agent                 | Model  | Purpose                 | Gate                         |
| ---- | --------------------- | ------ | ----------------------- | ---------------------------- |
| 1    | Explore (built-in)    | haiku  | Gather security context | GATE_SECURITY_EXPLORE_DONE   |
| 2    | Plan (built-in)       | sonnet | OWASP checklist design  | GATE_SECURITY_PLAN_DONE      |
| 3    | wasp-security-auditor | opus   | OWASP Top 10 audit      | GATE_SECURITY_AUDIT_DONE     |
| 4a   | wasp-security-auditor | sonnet | Validate OUT-OF-SCOPE   | GATE_SECURITY_SCOPE_APPROVED |
| 5    | wasp-code-generator   | haiku  | Generate security fixes | GATE_SECURITY_REMEDIATED     |
| 5    | wasp-test-automator   | haiku  | Add security tests      | GATE_SECURITY_REMEDIATED     |

---

## Cross-Phase Integration

**Reads from prior phases:**

- `[fase-dir]/gates.json` - Feature, phase status
- `[fase-dir]/tests/test-plan.md` - Expected security scenarios
- `[fase-dir]/implementation/implementation-notes.md` - Implementation details
- `[fase-dir]/refactor/refactor-report.md` - Final code state

**Creates for fase completion:**

- `[fase-dir]/security/security-audit-[date].md` - Complete audit
- `[fase-dir]/security/scope-approvals.md` - OUT-OF-SCOPE approvals (if any)
- `[fase-dir]/gates.json` - SECURITY phase completion with metrics → `FASE_X_COMPLETE`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toonvos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
