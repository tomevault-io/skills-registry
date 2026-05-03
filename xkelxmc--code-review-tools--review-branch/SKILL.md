---
name: review-branch
description: Comprehensive code review using all available review agents in parallel, with verification and false-positive filtering Use when this capability is needed.
metadata:
  author: xkelxmc
---

# Full Code Review

Run a comprehensive code review of a branch using every available review agent.

## Step 1: Determine what to review

- Figure out the default/base branch of the repository (main, master, develop, etc.)
- Review the current branch against the base branch
- Get the diff between them — this is the scope of the review

## Step 2: Enumerate review agents

Before launching anything, you MUST build a complete list of every agent you will launch.

**How to identify review agents:** Go through ALL available agent types from your system prompt. An agent qualifies if its description mentions ANY of these: review, analyze, audit, check code, find issues, error handling, silent failures, test coverage, type design, simplify, comments. Include agents from ALL plugins/namespaces — `pr-review-toolkit:*`, `xkelxmc-plugin:*`, standalone agents like `ui-testid-auditor`, etc.

**Key rules:**
- Agents from different plugins are DIFFERENT agents even if names overlap (e.g. `xkelxmc-plugin:code-reviewer` ≠ `pr-review-toolkit:code-reviewer` — launch BOTH)
- Include ALL review-capable agents, not just the obvious ones. Code simplifiers, comment analyzers, test analyzers, type design analyzers, test-id auditors — they ALL count
- Do NOT substitute with generic agents (Explore, general-purpose, Plan)
- Do NOT split one agent into multiple tasks by topic

**Output your enumeration as a numbered list before launching.** Example format:
> Launching N review agents:
> 1. `plugin:agent-name` — what it reviews
> 2. `plugin:agent-name` — what it reviews
> ...

If your list has fewer agents than the total number of review-capable agents available to you, STOP and re-check. You are missing some.

## Step 3: Parallel review

Launch ALL agents from Step 2 in a SINGLE message (one Task tool call per agent, all in parallel). Each agent should receive:
- A summary of what the branch does (from the diff/commit analysis)
- The git command to get the diff: `git diff <base>..HEAD`
- Clear instruction on what to focus on based on the agent's specialty
- **Explicit instruction: do NOT create any files in the repository. This is a read-only review. No reports, no analysis files, no artifacts. All findings must be returned as text output only.**

After all agents finish, check if any of them created files (like .md reports or analysis files) in the repository. If so, delete those specific files manually.

## Step 4: Verification

After all agents finish, go through EVERY finding yourself:

- Is this a real issue or a false positive?
- Is this pattern used elsewhere in the codebase? If the same approach exists in other places, it's an established project convention — not a bug in this branch
- Does the finding actually apply to the changed code, or is the agent confused?
- Would fixing this actually improve the code, or is it subjective nitpicking?

Be thorough but fair. The goal is to filter out noise and surface what truly matters.

## Step 5: Output

Present findings grouped by severity using these levels:

- 🔴 **Blocker** — real problem, must fix before merge
- 🟠 **High** — significant issue, strongly recommended to fix
- 🟡 **Medium** — valid concern, should fix but not critical
- 🔵 **Low** — minor issue, nice to improve
- 💡 **Nice-to-have** — suggestion for improvement, no action required

For each finding include:
- File and line reference
- What the issue is (concise)
- Why it matters
- If the same pattern exists elsewhere in the codebase, note it explicitly

## Step 6: Summary

IMPORTANT: Output the summary FIRST, before the detailed findings. The user should see the big picture immediately.

### Verdict

Overall recommendation: ✅ Approve / ⚠️ Approve with comments / 🚫 Request changes

### Findings table

A markdown table with ALL findings as a quick overview:

| # | Severity | File | Issue | Codebase pattern? |
|---|----------|------|-------|-------------------|
| 1 | 🔴 Blocker | `path/file.ts:42` | Short description | No |
| 2 | 🟠 High | `path/file.ts:15` | Short description | Yes — same in 3 files |
| ... | ... | ... | ... | ... |

### Stats
- Total findings from agents: N
- After verification: N blocker, N high, N medium, N low, N nice-to-have
- Dismissed as false positives: N

### Dismissed findings
Briefly list what was dismissed and why (e.g. "established project pattern", "false positive", "not applicable to changed code")

### Recommendations for follow-up tasks
If you found issues that exist across the codebase (not just in this branch), suggest creating tasks to address them project-wide. Format each as a short task description that could become a ticket.

## Step 7: Detailed findings

After the summary, output the detailed findings from Step 5 (grouped by severity with full descriptions).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xkelxmc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
