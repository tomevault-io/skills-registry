---
name: proactive-review-workflow
description: Automate multi-pass code review across quality, security, and performance dimensions Use when this capability is needed.
metadata:
  author: cdalsoniii
---

# Proactive Review Workflow Skill

Automate multi-pass code review across quality, security, and performance dimensions.

## Trigger Conditions
- PR opened or updated
- Merge event
- User invokes with "proactive review" or "full review"

## Input Contract
- **Required:** PR diff or file changes
- **Optional:** Review focus areas, prior review comments

## Output Contract
- Multi-dimensional review report (quality, security, performance)
- Categorized findings (blocking, suggestion, nit)
- Post-merge validation results

## Tool Permissions
- **Read:** PR diffs, source code, test files, configs
- **Write:** Review comments, review reports
- **Search:** Related code and patterns

## Execution Steps
1. Analyze PR diff for scope and affected components
2. Run quality pass (complexity, naming, error handling, dead code)
3. Run security pass (injection, auth, secrets, dependencies)
4. Run performance pass (N+1 queries, unbounded collections, missing indexes)
5. Categorize findings: blocking (must fix), suggestion (consider), nit (style)
6. Run post-merge validation on merged result
7. Generate review report

## Success Criteria
- All three dimensions reviewed (quality, security, performance)
- Findings categorized by severity
- No blocking findings remain unresolved
- Post-merge validation passes

## Escalation Rules
- Escalate if PR is >400 lines (diminishing review quality)
- Escalate if Critical security finding detected
- Escalate if architectural drift detected

## Example Invocations

**Input:** "Run proactive review on PR #42"

**Output:** Review: 3 files, 89 lines changed. Quality: 1 suggestion (extract helper function). Security: 1 blocking (SQL injection in search query — use parameterized query). Performance: 1 suggestion (add index for new WHERE clause). Post-merge: clean, no composition errors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cdalsoniii) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
