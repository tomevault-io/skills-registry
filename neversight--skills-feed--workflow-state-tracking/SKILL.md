---
name: workflow-state-tracking
description: Tracks and visualizes CCPM workflow state transitions (IDEA → PLANNED → IMPLEMENTING → VERIFYING → VERIFIED → COMPLETE). Prevents invalid state transitions and suggests appropriate next actions. Auto-activates when users ask about task status, "where am I in the workflow", "what should I do next", or "can I do this now". Use when this capability is needed.
metadata:
  author: neversight
---

# Workflow State Tracking

This skill provides real-time visibility into your CCPM workflow progress, prevents invalid state transitions, and guides you toward task completion through intelligent next action suggestions.

## State Machine Overview

CCPM uses a structured 8-state workflow that progresses from concept to completion. Understanding these states helps you know exactly where you are and what to do next.

### The 8 Workflow States

```
IDEA (💡)
  ↓
PLANNED (📋)
  ↓
IMPLEMENTING (🚀) ←→ BLOCKED (🚫)
  ↓
VERIFYING (🔍)
  ↓
VERIFIED (✅)
  ↓
COMPLETE (🎉)

CANCELLED (❌) - Terminal, can branch from any state
```

### State Definitions

| State | Phase | Description | Linear Status | Progress |
|-------|-------|-------------|---------------|----------|
| **IDEA** | Ideation | Initial concept, not yet planned | Backlog | 0% |
| **PLANNED** | Planning | Requirements gathered, plan created | Planned, Todo | 25% |
| **IMPLEMENTING** | Implementation | Active development in progress | In Progress, In Development | 50% |
| **BLOCKED** | Implementation | Cannot proceed due to blocker | Blocked | 50% |
| **VERIFYING** | Verification | Quality checks and review in progress | In Review, Testing | 75% |
| **VERIFIED** | Verification | Verified and ready to finalize | Verified, Approved | 90% |
| **COMPLETE** | Completion | Task finalized and closed | Done, Completed | 100% |
| **CANCELLED** | Cancelled | Task cancelled or abandoned | Cancelled, Archived | 0% |

### State Properties

- **Terminal States**: COMPLETE and CANCELLED (no transitions out)
- **Blocking States**: BLOCKED (prevents progression to VERIFYING)
- **Gating States**: VERIFIED (required before COMPLETE)
- **Reversible States**: IMPLEMENTING can return to PLANNED if re-planning needed

## State Detection

When you ask "Where am I?", this skill detects your current state by checking:

### 1. Linear Custom Fields
Most accurate: Reads CCPM custom fields directly from Linear issue
```
- ccpmPhase: Current state (IDEA, PLANNED, etc.)
- ccpmLastCommand: Last CCPM command executed
- ccpmLastUpdate: Timestamp of last update
- ccpmAutoTransitions: Whether state auto-updates
```

### 2. Linear Status Inference (Fallback)
If custom fields missing, infers from Linear status:
- "Backlog" → IDEA
- "Planned", "Todo", "Ready" → PLANNED
- "In Progress", "In Development" → IMPLEMENTING
- "Blocked" → BLOCKED
- "In Review", "Testing" → VERIFYING
- "Verified", "Approved" → VERIFIED
- "Done", "Completed" → COMPLETE
- "Cancelled", "Archived" → CANCELLED

### 3. Checklist Completion Analysis
Analyzes implementation checklist progress:
- 0-25% complete → Early IMPLEMENTING
- 50-75% complete → Mid IMPLEMENTING
- 80-99% complete → Near VERIFYING
- 100% complete → Ready to VERIFY

### 4. Git State Detection
Checks for uncommitted changes:
- Changes detected → Still IMPLEMENTING
- No changes → Ready for VERIFYING

## Valid Transitions

Each state has specific allowed transitions. Attempting an invalid transition will be prevented with helpful suggestions.

### Transition Matrix

```javascript
IDEA        → PLANNED, CANCELLED
PLANNED     → IMPLEMENTING, IDEA, CANCELLED
IMPLEMENTING → VERIFYING, PLANNED, BLOCKED
BLOCKED     → IMPLEMENTING, CANCELLED
VERIFYING   → VERIFIED, IMPLEMENTING
VERIFIED    → COMPLETE, IMPLEMENTING
COMPLETE    → (terminal - no transitions)
CANCELLED   → (terminal - no transitions)
```

### Confidence Levels

Each transition has a confidence score (0-100) indicating how certain the system is:
- **95%**: High confidence (plan ready to implement)
- **85%**: Good confidence (checks passing)
- **70%**: Medium confidence (re-planning during implementation)
- **50%**: Low confidence (requires explicit user confirmation)

## Invalid Transition Prevention

The skill prevents invalid workflows and provides helpful corrections:

### Common Invalid Transitions

```
❌ IDEA → IMPLEMENTING (skip planning)
   Suggestion: Run /ccpm:plan first

❌ IDEA → VERIFYING (skip planning + implementation)
   Suggestion: Follow workflow: IDEA → PLANNED → IMPLEMENTING → VERIFYING

❌ PLANNED → VERIFYING (skip implementation)
   Suggestion: Run /ccpm:work to start implementation

❌ IMPLEMENTING → COMPLETE (skip verification)
   Suggestion: Run /ccpm:verify first

❌ VERIFYING → COMPLETE (skip verified state)
   Suggestion: Fix issues and re-verify, then transition to VERIFIED

❌ COMPLETE → IMPLEMENTING (reopen completed task)
   Suggestion: Create new issue for follow-up work
```

### Pre-Condition Checks

Before allowing transitions, the skill validates pre-conditions:

**Transition to PLANNED requires**:
- ✓ Implementation checklist exists in issue description
- ✓ Basic requirements documented

**Transition to IMPLEMENTING requires**:
- ✓ Plan exists (checklist section)
- ✓ Linear status allows (not in terminal state)

**Transition to VERIFYING requires**:
- ✓ Checklist 100% complete (or bypassed)
- ✓ No uncommitted git changes
- ✓ Code review initiated

**Transition to VERIFIED requires**:
- ✓ All quality checks passing
- ✓ Code review approved
- ✓ No security issues

**Transition to COMPLETE requires**:
- ✓ In VERIFIED state
- ✓ PR merged
- ✓ All checks passed

## State Visualization

### Progress Indicator

Visual representation of task completion:
```
IDEA:         [░░░░░░░░░░] 0%
PLANNED:      [██░░░░░░░░] 25%
IMPLEMENTING: [████████░░] 50%
VERIFYING:    [██████████░] 75%
VERIFIED:     [███████████░] 90%
COMPLETE:     [████████████] 100%
```

### Current State Display

When checking status, you see:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🎯 Workflow State
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🚀 Phase: IMPLEMENTING
📋 Status: In Progress
⚙️  Last Command: /ccpm:work
🕐 Last Update: Nov 21, 2025 2:30 PM

📍 Next Actions:
  • Transition to VERIFYING
  • Transition to PLANNED (re-plan)
  • Transition to BLOCKED (if blocker found)

💡 Suggested: /ccpm:sync
   Save progress to Linear
   Confidence: 70%

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### State Flow Diagram

```
START
  ↓
💡 IDEA (Plan your task)
  └─→ /ccpm:plan
      ↓
📋 PLANNED (Ready to start)
  ├─→ /ccpm:work
  │   ↓
🚀 IMPLEMENTING (In progress)
  │   ├─→ /ccpm:sync (save progress)
  │   ├─→ /ccpm:commit (commit changes)
  │   ├─→ /ccpm:verify (check completion)
  │   │   ↓
  │   └─→ 🚫 BLOCKED (if blocker found)
  │       └─→ /ccpm:verify
  │           ↓ (issue resolved)
  │           └─→ 🚀 IMPLEMENTING
  │
  └─→ /ccpm:verify (when checklist complete)
      ↓
🔍 VERIFYING (Quality checks)
  ├─→ /ccpm:verify (continue checks)
  │   ↓ (all checks pass)
  │   ↓
✅ VERIFIED (Ready to finalize)
  └─→ /ccpm:done (create PR + complete)
      ↓
🎉 COMPLETE (Done!)
```

## Next Action Suggestions

Based on your current state, the skill suggests the best command to execute:

### IDEA State
```
Current: 💡 IDEA
Question: What should I do next?

Suggested: /ccpm:plan "Task title" <project>
Description: Create implementation plan
Confidence: 90%
Reasoning: Task needs planning before implementation
```

### PLANNED State
```
Current: 📋 PLANNED
Question: What should I do next?

Suggested: /ccpm:work
Description: Start implementation
Confidence: 90%
Reasoning: Plan is ready, begin development
```

### IMPLEMENTING State (Early)
```
Current: 🚀 IMPLEMENTING (25% complete)
Question: What should I do next?

Suggested: /ccpm:sync
Description: Save progress to Linear
Confidence: 70%
Reasoning: Document what you've done so far
```

### IMPLEMENTING State (Complete)
```
Current: 🚀 IMPLEMENTING (100% complete)
Question: What should I do next?

Suggested: /ccpm:verify
Description: Run quality checks
Confidence: 85%
Reasoning: Checklist complete, verify before completion
```

### BLOCKED State
```
Current: 🚫 BLOCKED
Question: What should I do next?

Suggested: /ccpm:verify
Description: Diagnose and fix blocker
Confidence: 80%
Reasoning: Address blocking issue to continue
```

### VERIFYING State
```
Current: 🔍 VERIFYING
Question: What should I do next?

Suggested: /ccpm:verify
Description: Continue verification process
Confidence: 80%
Reasoning: Complete all quality checks
```

### VERIFIED State
```
Current: ✅ VERIFIED
Question: What should I do next?

Suggested: /ccpm:done
Description: Finalize and create PR
Confidence: 95%
Reasoning: Verification passed, ready to complete
```

### COMPLETE State
```
Current: 🎉 COMPLETE
Status: Task is finished!
No further action needed.
```

## Command-State Mapping

Understanding which commands cause which state transitions:

| Command | From State | To State | Confidence |
|---------|-----------|---------|------------|
| `/ccpm:plan` | IDEA | PLANNED | 95% |
| `/ccpm:plan` | PLANNED | PLANNED | - (updates) |
| `/ccpm:work` | PLANNED | IMPLEMENTING | 95% |
| `/ccpm:sync` | IMPLEMENTING | IMPLEMENTING | - (progress update) |
| `/ccpm:commit` | IMPLEMENTING | IMPLEMENTING | - (git commit) |
| `/ccpm:verify` | IMPLEMENTING | VERIFYING | 85% |
| `/ccpm:verify` | VERIFYING | VERIFIED | 85% (if checks pass) |
| `/ccpm:verify` | VERIFYING | IMPLEMENTING | 100% (if checks fail) |
| `/ccpm:done` | VERIFIED | COMPLETE | 95% |
| `/ccpm:verify` | BLOCKED | IMPLEMENTING | 85% |

## Integration with Commands

### State Auto-Update

These commands automatically update your workflow state in Linear:

- **Planning commands**: `plan`, `planning:create`, `planning:plan`, `planning:update`
- **Implementation commands**: `work`, `implementation:start`, `implementation:sync`
- **Verification commands**: `verify`, `verification:check`, `verification:verify`
- **Completion commands**: `done`, `complete:finalize`

### Manual State Override

For edge cases, you can manually override state:
```
Task(linear-operations): `
operation: update_issue_state
params:
  issue_id: "PSN-29"
  phase: "IMPLEMENTING"
  reason: "Manual override due to external blocker"
context:
  command: "workflow-state-tracking:manual-override"
`
```

## Blocked State Handling

When a blocker is detected, workflow transitions to BLOCKED:

### Detecting Blockers
```
Blockers automatically detected by:
• Pre-condition validation failures
• Security audit failures
• Code review failures marked as blocking
• External dependency issues
• Permission/environment issues
```

### Resolving Blockers
```
When BLOCKED:
1. Suggested command: /ccpm:verify
2. Diagnose the specific issue
3. Take corrective action
4. Re-run verification
5. Transition back to IMPLEMENTING
```

Example:
```
🚫 BLOCKED: Security audit detected vulnerabilities

Blocker Details:
  • SQL injection risk in user input handler
  • Missing CSRF token validation
  • Hardcoded API key in config

Suggestions:
  1. Fix: Parameterize database queries
  2. Fix: Add CSRF token middleware
  3. Fix: Move API key to environment variable

Status: /ccpm:verify
```

## State Machine Queries

You can ask the skill for workflow information:

### "Where am I?"
Returns current state with progress indicators and suggestions

### "What can I do next?"
Shows valid transitions from current state

### "Can I run this command now?"
Checks if command is allowed in current state

### "What's blocking me?"
Shows any blockers preventing progression

### "How much progress?"
Displays checklist completion percentage and phase progress

### "Show me the workflow"
Visualizes the complete state machine and your position

## Integration Example

### Using State Tracking in a Workflow

```
Step 1: Check where you are
User: "Where am I in the workflow?"
Skill: "You're in PLANNING state. Your plan was created 2 hours ago."

Step 2: Get next suggestion
User: "What should I do next?"
Skill: "Suggested: /ccpm:work (90% confidence)"

Step 3: Start implementation
User: "/ccpm:work"
System: Transitions PLANNING → IMPLEMENTING

Step 4: Work on task
User: [Does actual implementation]

Step 5: Periodic sync
User: "/ccpm:sync"
System: Saves progress (stays in IMPLEMENTING)

Step 6: Check progress
User: "How much is done?"
Skill: "Checklist 85% complete. Nearly ready to verify."

Step 7: Finalize and verify
User: "/ccpm:verify"
System: Transitions IMPLEMENTING → VERIFYING

Step 8: All checks pass
System: Transitions VERIFYING → VERIFIED

Step 9: Finalize
User: "/ccpm:done"
System: Transitions VERIFIED → COMPLETE
```

## Best Practices

### Do's
- ✅ Check your state before starting work
- ✅ Follow suggested next actions
- ✅ Validate transitions before attempting them
- ✅ Sync progress regularly during implementation
- ✅ Ask for clarification if a transition is blocked

### Don'ts
- ❌ Skip verification (IMPLEMENTING → COMPLETE)
- ❌ Re-open completed tasks (manually revert COMPLETE)
- ❌ Ignore blockers (resolve before continuing)
- ❌ Work without a plan (plan first)
- ❌ Force invalid transitions (follow state machine rules)

## Troubleshooting

### State Misalignment
If displayed state doesn't match reality:
```
1. Check Linear custom fields: ccpmPhase field
2. Fall back to Linear status inference
3. Analyze checklist completion
4. Check git status for uncommitted changes

If still misaligned:
  /ccpm:work PSN-29 (detailed state diagnostics)
```

### Stuck in BLOCKED State
```
1. Identify blocker: /ccpm:work
2. Diagnose issue: /ccpm:verify
3. Fix root cause
4. Re-verify: /ccpm:verify
5. Return to IMPLEMENTING: Automatic on successful verification
```

### Premature State Transition
If transitioned too early (e.g., PLANNING → VERIFYING):
```
1. Use /ccpm:plan to re-plan if needed
2. Or transition back to IMPLEMENTING: /ccpm:work
3. Complete actual implementation
4. Then proceed with verification
```

### State Not Persisting
```
1. Check Linear write permissions
2. Verify custom fields exist: linear custom fields setup
3. Check Jira sync if using Jira integration
4. Review Linear comments for transition history
```

## Related Documentation

- [State Machine Definition](../../commands/_shared-state-machine.md)
- [Workflow Commands](../../commands/README.md)
- [CCPM Workflow Guide](./pm-workflow-guide)
- [Project Status Reports](../../commands/utils:report.md)

## Quick Reference

### Phase Emojis
- 💡 IDEA
- 📋 PLANNED
- 🚀 IMPLEMENTING
- 🚫 BLOCKED
- 🔍 VERIFYING
- ✅ VERIFIED
- 🎉 COMPLETE
- ❌ CANCELLED

### State Check Command
```bash
/ccpm:work <issue-id>
```

### Workflow Visualization
```bash
/ccpm:work <issue-id>
```

### Progress Report
```bash
/ccpm:sync <project>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
