---
name: diff-review
description: Run a multi-agent diff review with debate on PR or branch changes. Use when users say "diff-review", "/diff-review", "diff review", "PR review", or want multiple AI reviewers to analyze diff changes with cross-review debate. Six specialized reviewers (Security, Performance, Architecture, Logic & Correctness, Test & Quality, Agent-Friendliness) independently analyze changes, then debate each other's findings. Use when this capability is needed.
metadata:
  author: ccf05017
---

# Multi-Agent Diff Review with Debate

## User-invocable
- name: diff-review
- description: Run a multi-agent diff review with debate on PR or branch changes
- args: [mode] [target] - mode: "default", "intense", or custom preset name. target: PR number (e.g. "#123") or branch comparison (e.g. "main...HEAD"). Defaults to interactive mode with main...HEAD.

## Prerequisites & Usage

### Requirements
- **Git repository**: Must run inside a git repository
- **GitHub CLI (`gh`)**: Required for GitHub PR reviews (`#123`). Install: https://cli.github.com/ — Authenticate: `gh auth login`
- **GitLab CLI (`glab`)**: Required for GitLab MR reviews (`!456`). Install: https://gitlab.com/gitlab-org/cli — Authenticate: `glab auth login`
- **Git**: Required for branch diff reviews (`main...HEAD`)

### Quick Start
```
/diff-review                    # Interactive mode, diff main...HEAD
/diff-review #183               # Interactive mode, review GitHub PR #183
/diff-review !456               # Interactive mode, review GitLab MR !456
/diff-review default #183       # Default preset (all 6 reviewers, sonnet)
/diff-review intense #183       # Intense preset (all 6 reviewers, opus + plan approval)
/diff-review my-preset #183     # Custom preset from references/presets/my-preset.md
```

### If prerequisites are missing
When a user asks about this skill or attempts to run it, check prerequisites first:
1. Run `git rev-parse --git-dir` — if it fails, tell the user they must be in a git repository
2. If target is a GitHub PR (`#123`): run `gh --version` — if it fails, tell the user to install and authenticate `gh` CLI
3. If target is a GitLab MR (`!456`): run `glab --version` — if it fails, tell the user to install and authenticate `glab` CLI
4. If any check fails, list what's missing and how to fix it. Do NOT proceed with the review.

## Prompt

You are the **Team Lead** for a multi-perspective code review with debate.

### Step 0: Parse Args & Mode

Parse the user's arguments: `/diff-review [mode] [target]`

**Parsing priority:**
1. First token is `default` or `intense` → built-in preset, second token is target
2. First token exists as `references/presets/{token}.md` in the skill directory → custom preset, second token is target
3. Otherwise (e.g. `#123`, `main...HEAD`, or empty) → interactive mode, first token is target

**Target resolution** (same for all modes):
- GitHub PR number (e.g. `#123`): use `gh pr diff <number>`
- GitLab MR number (e.g. `!456`): use `glab mr diff <number>`
- Branch comparison (e.g. `main...feature`): use `git diff <comparison>`
- No target: use `git diff main...HEAD`

### Step 0a: Validate Prerequisites

Before proceeding, validate:
1. `git rev-parse --git-dir` succeeds (working directory is a git repo). If not → inform user and stop.
2. If target is a GitHub PR (`#123`): `gh --version` succeeds (gh CLI installed). If not → tell user to install `gh` from https://cli.github.com/ and run `gh auth login`.
3. If target is a GitHub PR (`#123`): `gh auth status` succeeds (authenticated). If not → tell user to run `gh auth login`.
4. If target is a GitLab MR (`!456`): `glab --version` succeeds (glab CLI installed). If not → tell user to install `glab` from https://gitlab.com/gitlab-org/cli and run `glab auth login`.
5. If target is a GitLab MR (`!456`): `glab auth status` succeeds (authenticated). If not → tell user to run `glab auth login`.

If any check fails, clearly list what's missing and how to fix it. Do NOT proceed.

**IMPORTANT: Do NOT change the current branch.** `gh pr diff`, `glab mr diff`, and `git diff` all output diffs without switching branches. Never run `gh pr checkout`, `glab mr checkout`, `git checkout`, or `git switch` during the review. The user's working branch must remain unchanged.

### Step 1: Configure Reviewers

Based on the mode determined in Step 0, configure the reviewer settings.

#### Built-in Presets

**`default`** - Fast full review:

| Reviewer | Enabled | Model | Plan Approval |
|----------|---------|-------|---------------|
| security-reviewer | yes | sonnet | off |
| performance-reviewer | yes | sonnet | off |
| architecture-reviewer | yes | sonnet | off |
| logic-reviewer | yes | sonnet | off |
| test-quality-reviewer | yes | sonnet | off |
| agent-friendliness-reviewer | yes | sonnet | off |

**`intense`** - Thorough review:

| Reviewer | Enabled | Model | Plan Approval |
|----------|---------|-------|---------------|
| security-reviewer | yes | opus | on |
| performance-reviewer | yes | opus | on |
| architecture-reviewer | yes | opus | on |
| logic-reviewer | yes | opus | on |
| test-quality-reviewer | yes | opus | on |
| agent-friendliness-reviewer | yes | opus | on |

#### Custom Preset

Read the preset file from `references/presets/{name}.md` and parse the table. See [references/preset-format.md](references/preset-format.md) for format details.

#### Interactive Mode

Ask the user to configure each reviewer using AskUserQuestion in **two rounds** (AskUserQuestion supports max 4 questions per call). Each question combines model and plan approval into a single choice.

**Round 1** (AskUserQuestion with 3 questions):

**Security Reviewer:**
```
question: "Security Reviewer - Checks injection, auth/authz, secret exposure, OWASP Top 10. How should it run?"
header: "Security"
options:
  - label: "Sonnet (Recommended)"
    description: "Fast and cost-efficient. No plan approval."
  - label: "Opus"
    description: "More thorough review. No plan approval."
  - label: "Opus + Plan"
    description: "Most thorough. Review plan requires lead approval before execution."
  - label: "Skip"
    description: "Do not run this reviewer."
```

**Performance Reviewer:**
```
question: "Performance Reviewer - Checks N+1 queries, memory leaks, blocking I/O, inefficient algorithms. How should it run?"
header: "Performance"
options:
  - label: "Sonnet (Recommended)"
    description: "Fast and cost-efficient. No plan approval."
  - label: "Opus"
    description: "More thorough review. No plan approval."
  - label: "Opus + Plan"
    description: "Most thorough. Review plan requires lead approval before execution."
  - label: "Skip"
    description: "Do not run this reviewer."
```

**Architecture Reviewer:**
```
question: "Architecture Reviewer - Checks SOLID violations, separation of concerns, dependency direction, API design. How should it run?"
header: "Architecture"
options:
  - label: "Sonnet (Recommended)"
    description: "Fast and cost-efficient. No plan approval."
  - label: "Opus"
    description: "More thorough review. No plan approval."
  - label: "Opus + Plan"
    description: "Most thorough. Review plan requires lead approval before execution."
  - label: "Skip"
    description: "Do not run this reviewer."
```

**Round 2** (AskUserQuestion with 3 questions):

**Logic & Correctness Reviewer:**
```
question: "Logic & Correctness Reviewer - Checks off-by-one errors, race conditions, null handling, conditional logic errors. How should it run?"
header: "Logic"
options:
  - label: "Sonnet (Recommended)"
    description: "Fast and cost-efficient. No plan approval."
  - label: "Opus"
    description: "More thorough review. No plan approval."
  - label: "Opus + Plan"
    description: "Most thorough. Review plan requires lead approval before execution."
  - label: "Skip"
    description: "Do not run this reviewer."
```

**Test & Quality Reviewer:**
```
question: "Test & Quality Reviewer - Checks test coverage, edge cases, code conventions, error handling. How should it run?"
header: "Test"
options:
  - label: "Sonnet (Recommended)"
    description: "Fast and cost-efficient. No plan approval."
  - label: "Opus"
    description: "More thorough review. No plan approval."
  - label: "Opus + Plan"
    description: "Most thorough. Review plan requires lead approval before execution."
  - label: "Skip"
    description: "Do not run this reviewer."
```

**Agent-Friendliness Reviewer:**
```
question: "Agent-Friendliness Reviewer - Checks execution path traceability, abstraction depth, explicit wiring, contract-first design, code navigability. How should it run?"
header: "Agent"
options:
  - label: "Sonnet (Recommended)"
    description: "Fast and cost-efficient. No plan approval."
  - label: "Opus"
    description: "More thorough review. No plan approval."
  - label: "Opus + Plan"
    description: "Most thorough. Review plan requires lead approval before execution."
  - label: "Skip"
    description: "Do not run this reviewer."
```

If the user selects "Other", parse their input for model (`sonnet`/`opus`) and plan (`plan`/`no plan`) keywords. Default to sonnet without plan if unclear.

**Edge case: 0 reviewers enabled** → Inform the user "At least 1 reviewer is required" and re-ask the questions.

### Step 2: Determine Review Target

Collect the diff using the target resolved in Step 0. Identify all changed files. If the diff is empty, inform the user and stop.

### Step 3: Create Team & Tasks

Create team `code-review`. Create tasks only for **enabled** reviewers:

For each enabled reviewer, create an "Independent {Name} Review" task. Then:
- If 2+ reviewers are enabled: create "Cross-Review & Debate" task (blocked by all review tasks) and "Synthesis & Final Report" task (blocked by debate task)
- If exactly 1 reviewer: create only "Synthesis & Final Report" task (blocked by the single review task). Skip the debate step.

### Step 4: Spawn Reviewers

Read [references/reviewer-prompts.md](references/reviewer-prompts.md) for each reviewer's focus areas and common task workflow.

Spawn only **enabled** reviewers using the Task tool with `team_name="code-review"`. Apply per-reviewer settings:
- **subagent_type**: `"Explore"` — reviewers only need to read files and communicate. Explore agents cannot Edit/Write files, providing a safety guardrail.
- **model**: Use the configured model (`"sonnet"` or `"opus"`)
- **mode**: If plan approval is `on`, set `mode: "plan"`. Otherwise, set `mode: "bypassPermissions"` to eliminate repeated permission prompts. This is safe because Explore agents cannot modify files (Edit/Write/NotebookEdit are excluded from their tool set).

Each reviewer's prompt MUST include:
- **Instruction to use English for all output** (findings, messages, debate)
- The full diff or changed file paths
- Their focus areas (from reviewer-prompts.md)
- The common task workflow (from reviewer-prompts.md)

### Step 5: Monitor & Facilitate Debate

**Skip this step if only 1 reviewer is enabled.**

After all independent reviews complete:

1. Unblock the "Cross-Review & Debate" task
2. Broadcast a summary of all findings to all reviewers
3. Instruct reviewers to debate each other's findings following the **Argument Quality Rules** in reviewer-prompts.md. Remind them: bare "agree/disagree" without code-level evidence will be discarded.
4. Allow 2-3 rounds of debate to converge
5. **Quality gate**: When synthesizing debate results, discard any severity change that lacks a concrete technical justification. A vote count ("3 reviewers agreed") is not a justification. The team lead must independently verify that the winning argument cites specific code, behavior, or scenario.

### Step 6: Synthesize Final Report

Use the template in [assets/report-template.md](assets/report-template.md) to produce the final report. Severity levels reflect the strength of technical arguments, not vote counts. If debate was skipped (1 reviewer), note this in the report. Write the Debate Log section using the "Thesis → Antithesis → Synthesis" structure from the template — strip reviewer names and focus on the arguments themselves.

**Save report to file:**
1. Output directory: `.claude/docs/review-results/` (create with `mkdir -p` if it doesn't exist)
2. File naming:
   - PR reviews (`#123`): `{pr_number}-{index}.md` (e.g., `183-1.md`)
   - Branch diff reviews (`main...feature`): `{target}-{compare}-{index}.md` (e.g., `main-feature-1.md`)
   - Default diff (`main...HEAD`): `main-HEAD-{index}.md`
3. Auto-increment index: Glob existing files matching the prefix in the output directory, count matches, use `count + 1`
4. Write the final report using the Write tool
5. Display the saved file path to the user

### Step 7: Save Preset (Interactive Mode Only)

**Skip this step if a built-in or custom preset was used.**

Ask the user whether to save the current configuration as a preset:
```
question: "Save this configuration as a preset for reuse?"
header: "Save"
options:
  - label: "Save"
    description: "Name it and save to references/presets/. Reuse with /diff-review <name>."
  - label: "Don't save"
    description: "Run with this configuration this time only."
```

If the user chooses "Save", ask for a preset name, then write the configuration table to `references/presets/{name}.md` following the format in [references/preset-format.md](references/preset-format.md).

### Step 8: Cleanup

Send shutdown requests to all reviewers, then delete the team.

## Notes
- Token cost: ~6-7x a single review (6 reviewers + lead), less if reviewers are skipped
- Best for: large PRs, critical changes, pre-release reviews
- For small changes: use a single-agent review instead
- `default` preset: fastest, good for routine reviews
- `intense` preset: most thorough, good for critical/security-sensitive changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ccf05017) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
