---
name: super-dev
description: This skill should be used when the user asks to "plan a feature", "evaluate a feature", "review a feature proposal", "analyze a feature before implementation", "plan a feature", "review this feature", "assess this feature", or uses the /super-dev command for multi-agent feature planning with 5-perspective judgment system. Use when this capability is needed.
metadata:
  author: juniyadi
---

# Super-Dev: Multi-Agent Feature Planning

Multi-agent feature planning with 5-perspective judgement system for validating new features before implementation.

## ⚠️ CRITICAL: Role Definition

**This skill is PLANNING and INSPECTION ONLY:**
- ✅ Analyze proposals
- ✅ Evaluate with 5 judges
- ✅ Provide recommendations
- ❌ Do NOT implement features
- ❌ Do NOT make code changes
- ❌ Do NOT create files

**Output Requirement:**
When running as a sub-agent, you MUST output the complete formatted report with ALL judge verdicts. Do not summarize. Do not internalize. OUTPUT the full report so the user can see it.

**Stop After Planning:**
After presenting the final report, STOP. Do not ask if the user wants to implement. Do not offer to implement. Your job is done after evaluation.

## Purpose

Transform vague feature requests into structured proposals, then evaluate them through 5 independent judge agents (Pragmatist, Purist, Innovator, Skeptic, Optimizer) to produce actionable go/no-go decisions.

## Usage

```bash
# With vague request (triggers interactive proposal builder)
/super-dev add notifications to Laravel app

# With file (structured proposal)
/super-dev --file docs/proposals/notification-system.md
```

## High-Level Flow

```
User Request
    ↓
Phase 0: Proposal Building (if needed)
    ↓
Phase 1: Parse & Analyze
    ↓
Phase 2: 5 Judges in PARALLEL
    ↓
Phase 3: Vote Aggregation
    ↓
Phase 4: Present Final Report
```

## Implementation

### Phase 0: Input Analysis & Proposal Building

```markdown
Analyze user input to determine if proposal builder is needed.

**Check for completeness:**
- Does input have structured sections (Goals, Requirements, Constraints)?
- Is it just a vague request like "add notifications"?
- Is it a file path with --file flag?

**Decision flow:**

if args starts with "--file":
    proposal_file = args after "--file"
    if file exists:
        proposal_text = read file
        → Skip to Phase 1
    else:
        ERROR: "File not found: {proposal_file}"
        EXIT

elif input looks structured (has "## Goals" or "## Requirements"):
    proposal_text = input
    → Skip to Phase 1

else:
    # Vague request, needs proposal building
    Use @skills/proposal-builder to:
    - Ask clarifying questions (5-7 questions)
    - Research framework best practices
    - Analyze existing codebase
    - Generate structured proposal
    - Get user confirmation

    if user approves proposal:
        proposal_text = generated proposal
        → Continue to Phase 1
    elif user wants to edit:
        Save proposal to file
        Tell user path and how to rerun
        EXIT
    else:
        EXIT (user declined)
```

### Phase 1: Parse & Analyze Codebase

```markdown
Use @skills/feature-parser to extract structured data:

Input: proposal_text (markdown)
Output: ParsedProposal object with:
- title
- goals
- requirements (mustHave + niceToHave)
- constraints
- successCriteria
- keywords

Use @skills/codebase-analyzer to understand context:

Input: ParsedProposal (especially keywords)
Output: CodebaseContext object with:
- similarFeatures (existing related code)
- techStack (languages, frameworks, libraries)
- patterns (architecture style, testing strategy)
- integrationPoints (where to integrate)
- potentialConflicts (files that might need changes)

These two operations can run in parallel for speed.
```

### Phase 2: Launch 5 Judge Agents in PARALLEL

**CRITICAL: All 5 judges MUST be launched in a single message with multiple Task tool calls.**

```markdown
Prepare input for judges:
judge_input = {
    parsedProposal: ParsedProposal,
    codebaseContext: CodebaseContext
}

Launch 5 Task agents simultaneously in ONE message:

Task 1: @skills/judge-pragmatist
  - Focus: Practical feasibility, user value, MVP scope
  - Input: judge_input
  - Output: JudgeVerdict (APPROVE/REJECT + reasoning + concerns + suggestions)

Task 2: @skills/judge-purist
  - Focus: Code quality, architecture, best practices
  - Input: judge_input
  - Output: JudgeVerdict

Task 3: @skills/judge-innovator
  - Focus: Modern solutions, creative approaches, UX
  - Input: judge_input
  - Output: JudgeVerdict

Task 4: @skills/judge-skeptic
  - Focus: Risks, security, failure modes, edge cases
  - Input: judge_input
  - Output: JudgeVerdict

Task 5: @skills/judge-optimizer
  - Focus: Performance, efficiency, scalability, cost
  - Input: judge_input
  - Output: JudgeVerdict

Wait for all 5 agents to complete.

Collect results and OUTPUT them immediately:
verdicts = [
    {judgeName: "Pragmatist", verdict: "APPROVE", ...},
    {judgeName: "Purist", verdict: "REJECT", ...},
    {judgeName: "Innovator", verdict: "APPROVE", ...},
    {judgeName: "Skeptic", verdict: "REJECT", ...},
    {judgeName: "Optimizer", verdict: "APPROVE", ...}
]

IMPORTANT: After collecting verdicts, output a summary:
"✅ All 5 judges completed:
- Pragmatist: APPROVE
- Purist: REJECT
- Innovator: APPROVE
- Skeptic: REJECT
- Optimizer: APPROVE"

This ensures visibility when running in sub-agent.
```

**Error Handling:**
```markdown
if 1-2 judges fail:
    Continue with remaining verdicts
    Note in final report: "[Judge] verdict unavailable"
    Adjust decision matrix accordingly (e.g., 3/4 approve = APPROVED)

if 3+ judges fail:
    ERROR: "Multi-agent review failed. Please retry."
    EXIT
```

### Phase 3: Aggregate Votes & Generate Report

```markdown
Use @skills/vote-aggregator to synthesize results:

Input:
- verdicts (array of 5 JudgeVerdicts)
- parsedProposal (for context in report)

Processing:
1. Count votes (approve vs reject)
2. Determine final decision (APPROVED/NEEDS_REVISION/REJECTED)
3. Extract common themes (strengths, concerns)
4. Synthesize recommendations (must-fix vs nice-to-have)
5. Generate next steps

Output: FinalRecommendation object with formatted report
```

### Phase 4: Present Final Report to User

**CRITICAL: This skill is for PLANNING and INSPECTION ONLY. Do NOT implement the feature.**

```markdown
Output the complete report from vote-aggregator:

## 🎯 Feature Review: [Title]

**Final Decision: [EMOJI] [DECISION]**
**Vote Breakdown:** X Approve, Y Reject

### 📊 Judge Verdicts
[Summary from each judge with full reasoning]

<details>
<summary>📋 Detailed Analysis</summary>
[Strengths, concerns, must-fix items, suggestions]
</details>

**Next Steps:**
[Clear guidance based on decision]

---

IMPORTANT OUTPUT REQUIREMENTS:
1. **You MUST output the COMPLETE formatted report above** - do not summarize
2. **Include ALL judge verdicts** with their reasoning (Pragmatist, Purist, Innovator, Skeptic, Optimizer)
3. **Show the detailed analysis section** with strengths, concerns, and recommendations
4. **Display clear next steps** so user knows what to do
5. **STOP after outputting the report** - do NOT ask if user wants to implement
6. **STOP after outputting the report** - do NOT start implementing the feature

Your role is ADVISORY ONLY:
- Analyze and evaluate the proposal ✅
- Provide multi-perspective feedback ✅
- Recommend next steps ✅
- Implement the feature ❌
- Make code changes ❌
- Create files ❌

User can now:
- Proceed with implementation (if APPROVED) - on their own or by asking you separately
- Revise proposal (if NEEDS_REVISION) - and rerun super-dev
- Redesign approach (if REJECTED) - and rerun super-dev
```

## Implementation Notes

### Parallel Execution

**MUST launch all 5 judges in single message:**

```markdown
✅ CORRECT:
I'm launching all 5 judge agents in parallel to evaluate this feature.
<uses Task tool with @skills/judge-pragmatist>
<uses Task tool with @skills/judge-purist>
<uses Task tool with @skills/judge-innovator>
<uses Task tool with @skills/judge-skeptic>
<uses Task tool with @skills/judge-optimizer>

❌ WRONG:
Launching Pragmatist judge...
[wait for response]
Launching Purist judge...
[wait for response]
...
```

Sequential execution would take 5x longer and bias later judges.

### User Communication

**Be transparent about process:**

```markdown
At each phase, inform user:

Phase 0: "🧠 Analyzing request... needs clarification."
Phase 1: "📝 Parsing proposal... ✅ Parsed: [title]"
         "🔍 Analyzing codebase... ✅ Found [N] similar features"
Phase 2: "👥 Launching 5 judge agents in parallel..."
         "⏳ Pragmatist analyzing..."
         "⏳ Purist analyzing..."
         ...
         "✅ All 5 judges completed:
            - Pragmatist: APPROVE
            - Purist: REJECT
            - Innovator: APPROVE
            - Skeptic: REJECT
            - Optimizer: APPROVE"
Phase 3: "📊 Aggregating verdicts..."
Phase 4: "✅ Final report ready"

         [OUTPUT THE COMPLETE FORMATTED REPORT - DO NOT SUMMARIZE]

         Then STOP. Do not continue to implementation.
```

**CRITICAL for Sub-Agent Execution:**
When running as a sub-agent via Task tool, the final report MUST be outputted in full, not just processed internally. The user cannot see internal processing - only explicit output messages are visible.
```

### Performance Expectations

```markdown
Phase 0: 1-3 minutes (if needed, includes user Q&A time)
Phase 1: 40 seconds - 2 minutes (parsing + codebase analysis)
Phase 2: 1-3 minutes (parallel, NOT 5-15 minutes)
Phase 3: 20-30 seconds (aggregation)
Total: 2-6 minutes (excluding user interaction time)
```

### Error Messages

```markdown
If proposal file not found:
❌ Error: File not found: {path}

Usage: /super-dev --file <path-to-proposal>
Example: /super-dev --file docs/proposals/notification-system.md

Or use inline request:
Example: /super-dev add notifications to Laravel app


If proposal builder user declines:
⚠️ Proposal building cancelled.

To start over: /super-dev [your request]
Or create proposal manually using template: super-dev/templates/proposal-template.md


If judges fail:
❌ Error: Multi-agent review failed (3+ judges unavailable)

Please try again. If issue persists, check:
- Network connectivity
- System resources
- Retry after a moment


If empty proposal:
❌ Error: No feature proposal provided.

Usage:
  /super-dev add user authentication    (vague request - will ask questions)
  /super-dev --file docs/proposals/auth.md    (structured proposal)

See template: super-dev/templates/proposal-template.md
```

## Full Example Execution

**User Input:**
```
/super-dev add notification feature to Laravel 12
```

**System Output:**
```
🚀 Super-Dev: Multi-Agent Feature Review
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🧠 Analyzing request...
⚠️  Request needs clarification. Let me ask a few questions to build
a complete proposal.

Q1: What triggers the notifications?
[Multiple choice options via AskUserQuestion]

[User answers 5 questions...]

✅ Proposal generated!

[Shows generated proposal]

Does this capture what you need?
✅ Yes, proceed with review (Recommended)
[User selects "Yes"]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📝 Parsing proposal...
✅ Parsed: "User Mention Notifications"
   - 4 must-have requirements
   - 2 nice-to-have features
   - 3 constraints identified

🔍 Analyzing codebase...
   - Found: app/Notifications/WelcomeEmail.php (existing example)
   - Found: app/Services/EmailService.php (can reuse)
   - Tech stack: Laravel 12.2, PHP 8.3
   - Architecture: MVC with Service Layer
✅ Analysis complete (confidence: High)

👥 Launching 5 judge agents in parallel...
   ⏳ Pragmatist analyzing...
   ⏳ Purist analyzing...
   ⏳ Innovator analyzing...
   ⏳ Skeptic analyzing...
   ⏳ Optimizer analyzing...

✅ All 5 judges completed:
   - Pragmatist: APPROVE
   - Purist: REJECT
   - Innovator: APPROVE
   - Skeptic: REJECT
   - Optimizer: APPROVE

📊 Aggregating verdicts...
✅ Final report ready

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 🎯 Feature Review: User Mention Notifications

**Final Decision: ⚠️ NEEDS REVISION**

**Vote Breakdown:** 3 Approve, 2 Reject

### 📊 Judge Verdicts

**✅ Pragmatist:** APPROVE
> Good user value with realistic scope...

**❌ Purist:** REJECT
> Missing architectural clarity...

[... full report ...]

**Next Steps:**

⚠️ **Revise proposal and resubmit:**
1. Add interface design (INotificationChannel)
2. Add security section (rate limiting, validation)
3. Add performance section (caching strategy)

Once revised: `/super-dev --file proposal-v2.md`

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Tips for Effective Use

**For Users:**
- Start with vague ideas - the system will guide you
- Be honest in Q&A - better input = better feedback
- Take revision feedback seriously - judges catch real issues
- Iterate based on concerns - revise and resubmit

**For Developers:**
- Read all judge feedback, not just final decision
- Minority opinions (1 reject in 4 approves) often highlight real risks
- Use suggestions during implementation
- Consider phased approach if scope concerns raised

**For Teams:**
- Use for major features before starting implementation
- Share reports in team discussions
- Build consensus around judge feedback
- Track which concerns proved valid in retrospect

## Success Criteria

**Good Review Session:**
- Clear actionable decision
- Specific feedback on issues
- Concrete suggestions for improvement
- Multiple perspectives considered
- User knows exactly what to do next

**Poor Review Session:**
- Vague "fix everything" feedback
- No clear decision
- Contradictory suggestions
- Missing critical concerns
- User confused about next steps

The super-dev system should make feature planning faster and more thorough by combining multiple expert perspectives into a single automated review process.

---

*Super-Dev v1.0 - Multi-Agent Feature Planning System*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/juniyadi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
