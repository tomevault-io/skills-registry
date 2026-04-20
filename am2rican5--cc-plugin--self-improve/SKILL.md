---
name: am2rican5self-improve
description: Run a self-improvement cycle that analyzes Claude Code usage patterns and applies targeted improvements to CLAUDE.md, hooks, and skills. Audits current setup, identifies gaps from usage data, and proposes grouped changes with rationale. Use when user says "improve my setup", "self-improve", "optimize my Claude config", "fix my workflow", or after running /insights and wanting to act on the results. Do NOT use for general Claude Code help, configuration questions, or manual editing of individual files. Use when this capability is needed.
metadata:
  author: am2rican5
---

# Self-Improve Workflow

## Critical Rules

- NEVER apply changes without explicit user approval
- NEVER add language-specific rules to global CLAUDE.md unless the user confirms their stack
- NEVER remove existing rules without explaining why
- Prefer editing existing files over creating new ones
- Keep CLAUDE.md under 200 lines — link to separate files for detail
- Every rule must be trigger-action format: WHEN X, DO Y

## Instructions

### Step 1: Load Report

1. IF `$ARGUMENTS` contains a file path, read that report file
2. OTHERWISE, check for an existing report at `~/.claude/usage-data/report.html`
3. IF no report exists, tell the user: "No usage report found. Run `/insights` first to generate one, then re-run `/self-improve`." and **stop**.

Extract from the report:
- Friction points and failure patterns
- Interaction style analysis
- What's working vs what's hindering
- Specific suggestions from the report

### Step 2: Audit Current Setup

Read these files to understand the current configuration:
- `~/.claude/CLAUDE.md` (global rules)
- `~/.claude/settings.json` (hooks, permissions, plugins)
- Any project-level `.claude/CLAUDE.md` in the current working directory
- All files in `~/.claude/hooks/`
- All files in `~/.claude/skills/`

Compare the current setup against the report findings. Identify gaps: what problems from the report are NOT addressed by existing rules, hooks, or skills?

### Step 3: Propose Improvements

Present a numbered list of proposed changes, grouped by type:

**CLAUDE.md Rules:**
- New trigger-action rules that address identified friction
- Rules to remove or update if they're stale or counterproductive
- Keep rules language-agnostic unless working in a project with a specific stack

**Hooks:**
- New hooks to enforce behaviors that rules alone can't guarantee
- Changes to existing hooks that aren't working

**Skills:**
- New skills to encode repeated multi-step workflows
- Changes to existing skills

**Workflow Changes:**
- Prompt patterns or habits to adopt
- Session management improvements

For each proposed change, state:
- **WHAT**: The exact change
- **WHY**: Link to specific report finding
- **CONTENT**: The exact text/code to write

### Step 4: Apply with Confirmation

WAIT for user to approve, reject, or modify each proposed change.

- Present changes one group at a time (CLAUDE.md, then hooks, then skills)
- For each group, ask the user which items to apply
- Apply only approved changes
- After applying, summarize what was changed and what was skipped

## Examples

### Example 1: Post-insights improvement

User says: "I just ran /insights, now improve my setup"

Actions:
1. Read report from `~/.claude/usage-data/report.html`
2. Audit current CLAUDE.md, hooks, and skills
3. Present grouped proposals with rationale
4. Apply approved changes

Result: Targeted config updates linked to actual usage patterns.

### Example 2: Using a specific report

User says: "/self-improve ~/Downloads/report.html"

Actions:
1. Read the specified report file
2. Same audit and proposal flow

Result: Improvements based on the provided report.

## Troubleshooting

### No report found
**Cause:** User hasn't run `/insights` yet.
**Solution:** Tell the user to run `/insights` first, then re-run this skill.

### Report contains no actionable findings
**Cause:** Usage data is too sparse or setup is already well-optimized.
**Solution:** Inform the user that no changes are recommended and explain why.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/am2rican5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
