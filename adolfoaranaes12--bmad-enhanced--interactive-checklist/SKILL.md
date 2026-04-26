---
name: interactive-checklist
description: Number of decision/branch points in workflow Use when this capability is needed.
metadata:
  author: adolfoaranaes12
---

# Interactive Checklist Skill

## Purpose

Create guided, interactive checklists for complex workflows, validation processes, onboarding sequences, and multi-step procedures. Checklists provide step-by-step instructions, validation criteria, decision trees, and progress tracking.

**Core Principles:**
- Step-by-step clarity (each step is clear and actionable)
- Progressive disclosure (details revealed as needed)
- Validation gates (ensure quality at each step)
- Decision support (guide users through choices)
- Progress visibility (track completion status)

---

## Prerequisites

- Workflow/process defined (either high-level description or detailed steps)
- Understanding of workflow branching (if any)
- Validation criteria identified
- workspace/ directory exists for checklist storage

---

## Workflow

### Step 1: Workflow Analysis

**Action:** Understand workflow structure, identify steps, decision points, and validation requirements.

**Key Activities:**

1. **Identify Workflow Type**

   **Linear Workflow:**
   ```
   Step 1 → Step 2 → Step 3 → Step 4 → Complete

   Example: Software deployment
   1. Run tests
   2. Build application
   3. Deploy to staging
   4. Verify staging
   5. Deploy to production
   ```

   **Branching Workflow:**
   ```
   Step 1 → Step 2 → Decision
                      ├─ Path A → Step 3A → Step 4
                      └─ Path B → Step 3B → Step 4

   Example: Code review process
   1. Review code
   2. Decision: Approve or Request Changes?
      A. If approved → Merge PR → Complete
      B. If changes needed → Developer fixes → Re-review
   ```

   **Cyclic Workflow:**
   ```
   Step 1 → Step 2 → Validation
                      ├─ Pass → Step 3 → Complete
                      └─ Fail → Return to Step 1

   Example: Quality assurance
   1. Implement feature
   2. Run tests
   3. If tests pass → Deploy
   4. If tests fail → Fix bugs → Return to step 2
   ```

   **Validation Checklist:**
   ```
   Independent items (order doesn't matter):
   - [ ] Item 1 validated
   - [ ] Item 2 validated
   - [ ] Item 3 validated

   Example: Pre-launch checklist
   - [ ] Security audit complete
   - [ ] Performance tests passed
   - [ ] Documentation updated
   - [ ] Backup verified
   ```

2. **Break Down into Steps**
   ```
   For each step, define:
   - Step number/ID
   - Step name (brief, actionable)
   - Description (what to do)
   - Validation criteria (how to know it's done)
   - Prerequisites (what must be done first)
   - Estimated time (how long it takes)
   - Resources needed (tools, access, knowledge)
   - Common issues (what might go wrong)
   ```

3. **Identify Decision Points**
   ```
   Decision point structure:
   - Condition: What question needs answered?
   - Options: What are the choices?
   - Outcomes: Where does each choice lead?
   - Criteria: How to decide?

   Example:
   Decision: "Do tests pass?"
   - Option A: YES → Continue to deployment
   - Option B: NO → Return to development
   - Criteria: 100% test pass rate, no failures
   ```

4. **Map Dependencies**
   ```
   Identify:
   - Sequential dependencies (Step 2 requires Step 1 complete)
   - Parallel steps (Steps can be done simultaneously)
   - Blocking dependencies (Cannot proceed until X is done)
   - Optional steps (Can be skipped in some cases)
   ```

**Output:** Workflow structure with steps, decision points, and dependencies

**See:** `references/workflow-patterns.md` for workflow type identification

---

### Step 2: Checklist Generation

**Action:** Create structured checklist document with steps, instructions, and tracking.

**Key Activities:**

1. **Create Checklist Header**
   ```markdown
   # Workflow Name: [Name]

   **Type:** Linear | Branching | Cyclic | Validation
   **Total Steps:** [N]
   **Estimated Time:** [X hours/days]
   **Last Updated:** [Date]

   ## Overview
   [Brief description of workflow purpose and outcome]

   ## Prerequisites
   - [ ] Prerequisite 1
   - [ ] Prerequisite 2
   - [ ] Prerequisite 3

   **Ready to proceed?** All prerequisites must be checked before starting.
   ```

2. **Generate Step Templates**

   **Linear Step:**
   ```markdown
   ## Step [N]: [Step Name]

   **Status:** ⬜ Not Started | 🔄 In Progress | ✅ Complete

   **Estimated Time:** [X minutes/hours]

   **Description:**
   [Detailed instructions on what to do]

   **How to Execute:**
   1. [Action 1]
   2. [Action 2]
   3. [Action 3]

   **Validation Criteria:**
   - [ ] Criterion 1 met
   - [ ] Criterion 2 met
   - [ ] Criterion 3 met

   **Resources:**
   - Tool/Link 1
   - Documentation reference
   - Access required

   **Common Issues:**
   - Issue 1: [Description] → Solution: [Fix]
   - Issue 2: [Description] → Solution: [Fix]

   **Next Step:** Proceed to Step [N+1]
   ```

   **Decision Step:**
   ```markdown
   ## Step [N]: [Decision Point Name]

   **Status:** ⬜ Awaiting Decision

   **Decision Question:** [What needs to be decided?]

   **Options:**

   ### Option A: [Choice 1]
   **When to choose:** [Criteria for this option]
   **Next steps:** [Where this leads]

   ### Option B: [Choice 2]
   **When to choose:** [Criteria for this option]
   **Next steps:** [Where this leads]

   **Decision Made:** [ ] Option A | [ ] Option B

   **Proceed to:**
   - If Option A → Step [X]
   - If Option B → Step [Y]
   ```

   **Validation Gate:**
   ```markdown
   ## Validation Checkpoint: [Name]

   **Purpose:** Ensure quality/readiness before proceeding

   **Validation Checklist:**
   - [ ] Check 1: [Description] - Status: ⬜ Pass / ❌ Fail
   - [ ] Check 2: [Description] - Status: ⬜ Pass / ❌ Fail
   - [ ] Check 3: [Description] - Status: ⬜ Pass / ❌ Fail

   **Pass Criteria:** All checks must pass

   **Result:** ⬜ PASS (proceed) | ❌ FAIL (address issues)

   **If FAIL:**
   - Identify which checks failed
   - Address issues
   - Re-run validation

   **If PASS:** Proceed to Step [N+1]
   ```

3. **Add Progress Tracking**
   ```markdown
   ## Progress Tracker

   **Overall Progress:** [█████░░░░░] 50% (5/10 steps complete)

   **Completed Steps:**
   - [✅] Step 1: [Name]
   - [✅] Step 2: [Name]
   - [✅] Step 3: [Name]
   - [✅] Step 4: [Name]
   - [✅] Step 5: [Name]

   **Current Step:**
   - [🔄] Step 6: [Name] (In Progress)

   **Remaining Steps:**
   - [⬜] Step 7: [Name]
   - [⬜] Step 8: [Name]
   - [⬜] Step 9: [Name]
   - [⬜] Step 10: [Name]

   **Time Tracking:**
   - Estimated Total: [X hours]
   - Time Spent: [Y hours]
   - Remaining: [Z hours]
   ```

4. **Include Decision Tree (for Branching Workflows)**
   ```markdown
   ## Workflow Decision Tree

   ```
   START
     │
     ├─ Step 1: Setup
     │
     ├─ Step 2: Execute
     │
     ├─ DECISION: Success?
     │    ├─ YES → Step 3: Deploy → Step 4: Verify → END
     │    └─ NO → Step 2b: Debug → Return to Step 2
   ```
   ```

**Output:** Complete interactive checklist document

**See:** `references/checklist-templates.md` for template examples

---

### Step 3: Add Interactive Elements

**Action:** Enhance checklist with interactive features for better usability.

**Key Activities:**

1. **Add Collapsible Sections** (for detailed steps)
   ```markdown
   ## Step 3: Database Migration

   **Status:** ⬜ Not Started

   <details>
   <summary>📖 Click to expand step details</summary>

   **Description:**
   Run database migration scripts to update schema.

   **Commands:**
   ```bash
   npm run migrate
   npm run seed
   ```

   **Validation:**
   - [ ] Migration ran without errors
   - [ ] All tables created
   - [ ] Seed data loaded

   </details>
   ```

2. **Add Quick Reference Tables**
   ```markdown
   ## Quick Reference: Status Codes

   | Code | Meaning | Action |
   |------|---------|--------|
   | 200 | Success | Continue |
   | 400 | Bad Request | Check input |
   | 401 | Unauthorized | Check credentials |
   | 500 | Server Error | Check logs |
   ```

3. **Add Helpful Links**
   ```markdown
   ## Resources

   **Documentation:**
   - [Setup Guide](docs/setup.md)
   - [Troubleshooting](docs/troubleshooting.md)

   **Tools:**
   - [Dashboard](https://app.example.com/dashboard)
   - [Logs](https://logs.example.com)

   **Support:**
   - Slack: #engineering-help
   - Email: support@example.com
   ```

4. **Add Time Estimates and Tracking**
   ```markdown
   ## Time Tracking

   | Step | Estimated | Actual | Notes |
   |------|-----------|--------|-------|
   | 1 | 15 min | ___ min | _____ |
   | 2 | 30 min | ___ min | _____ |
   | 3 | 45 min | ___ min | _____ |
   | 4 | 20 min | ___ min | _____ |

   **Total Estimated:** 1h 50m
   **Total Actual:** ____
   ```

5. **Add Sign-Off Section**
   ```markdown
   ## Workflow Completion

   **All steps complete?**
   - [ ] All steps checked off
   - [ ] All validations passed
   - [ ] No blockers remaining

   **Sign-Off:**
   - **Completed By:** __________________
   - **Date:** __________________
   - **Verified By:** __________________ (if applicable)
   - **Notes:** __________________

   **Status:** ⬜ INCOMPLETE | ✅ COMPLETE
   ```

**Output:** Enhanced interactive checklist

**See:** `references/interactive-elements.md` for enhancement techniques

---

### Step 4: Validation and Testing

**Action:** Verify checklist is complete, accurate, and usable.

**Key Activities:**

1. **Completeness Check**
   ```
   - [ ] All steps documented
   - [ ] All decision points identified
   - [ ] All validation criteria defined
   - [ ] All resources linked
   - [ ] All prerequisites listed
   - [ ] Progress tracking included
   ```

2. **Clarity Check**
   ```
   For each step:
   - [ ] Instructions are clear and actionable
   - [ ] Technical terms explained
   - [ ] Examples provided (where needed)
   - [ ] Expected outcomes stated
   ```

3. **Usability Test**
   ```
   - [ ] Test checklist on sample workflow
   - [ ] Verify all steps can be completed
   - [ ] Verify decision logic works
   - [ ] Verify validation gates catch issues
   - [ ] Time estimates are reasonable
   ```

4. **Update and Iterate**
   ```
   Based on testing:
   - Add missing steps
   - Clarify unclear instructions
   - Fix broken links
   - Adjust time estimates
   - Add troubleshooting tips
   ```

**Output:** Validated, tested checklist ready for use

**See:** `references/checklist-validation.md` for testing procedures

---

## Common Scenarios

### Scenario 1: Software Deployment Checklist

**Context:** Step-by-step deployment process

**Workflow Type:** Linear with validation gates

**Checklist Structure:**
```markdown
1. Pre-Deployment Checklist (Validation)
2. Build Application
3. Run Tests (Validation Gate)
4. Deploy to Staging
5. Verify Staging (Validation Gate)
6. Deploy to Production
7. Verify Production (Validation Gate)
8. Post-Deployment Steps
```

---

### Scenario 2: Code Review Workflow

**Context:** Branching workflow with approval/rejection

**Workflow Type:** Branching

**Checklist Structure:**
```markdown
1. Review Code Changes
2. Run Automated Checks
3. Decision: Approve or Request Changes?
   A. Approve → Merge PR → Complete
   B. Request Changes → Developer addresses feedback → Return to Step 1
```

---

### Scenario 3: New Employee Onboarding

**Context:** Multi-step onboarding with many tasks

**Workflow Type:** Validation (order-independent tasks)

**Checklist Structure:**
```markdown
Week 1:
- [ ] IT setup (laptop, accounts, access)
- [ ] HR paperwork
- [ ] Team introductions
- [ ] Codebase orientation

Week 2:
- [ ] First code contribution
- [ ] Attend team meetings
- [ ] Shadow senior engineer

Week 3:
- [ ] Own first ticket
- [ ] Complete training modules
```

---

### Scenario 4: Security Audit

**Context:** Comprehensive security validation

**Workflow Type:** Validation with severity levels

**Checklist Structure:**
```markdown
Critical:
- [ ] Authentication security verified
- [ ] Data encryption enabled
- [ ] Access controls configured

High Priority:
- [ ] Rate limiting implemented
- [ ] Input validation present
- [ ] CORS configured properly

Medium Priority:
- [ ] Logging enabled
- [ ] Error handling comprehensive
- [ ] Dependencies up to date
```

---

## Best Practices

1. **Keep Steps Atomic** - One clear action per step
2. **Make Steps Actionable** - Use verbs (Run, Deploy, Verify, Check)
3. **Define Success Criteria** - How to know step is complete
4. **Estimate Time Realistically** - Help users plan
5. **Provide Context** - Why this step matters
6. **Anticipate Issues** - Include troubleshooting tips
7. **Link Resources** - Provide tools, docs, support
8. **Use Visual Indicators** - Status icons, progress bars
9. **Test with Users** - Validate checklist with actual workflow
10. **Iterate and Improve** - Update based on feedback

---

## When to Escalate

**Escalate to stakeholders when:**
- Workflow steps unclear or ambiguous
- Decision criteria not defined
- Validation standards not established
- Approval process needed

**Escalate to subject matter experts when:**
- Technical details uncertain
- Best practices unknown
- Tools or access requirements unclear

**Use alternative skill when:**
- Need detailed documentation → Use `create-prd` or `shard-document`
- Need planning breakdown → Use `create-task-spec`
- Need validation framework → Use `review-task`

---

## Reference Files

- `references/workflow-patterns.md` - Workflow type identification and patterns
- `references/checklist-templates.md` - Templates for different workflow types
- `references/interactive-elements.md` - Enhancement techniques for interactivity
- `references/checklist-validation.md` - Testing and validation procedures

---

*Part of BMAD Enhanced Planning Suite*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adolfoaranaes12) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
