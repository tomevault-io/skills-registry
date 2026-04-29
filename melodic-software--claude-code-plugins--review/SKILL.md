---
name: review
description: Run comprehensive code review before commits, PRs, or releases. Uses code-reviewer agent with tiered security, performance, and quality checks. Use when this capability is needed.
metadata:
  author: melodic-software
---

# Code Review Command

Run comprehensive code review on specified files or staged changes.

## Instructions

### Pre-Flight: Research Phase + File Count (MANDATORY)

**CRITICAL: The code-reviewer agent runs a MANDATORY Research Phase (Step 0) before analysis:**

1. **Technology Detection** - Agent scans file extensions, imports, and package manifests
2. **MCP Research** - Agent queries MCP servers for current best practices
3. **Build Truth Context** - Agent builds validated context before reviewing code

This research phase ensures ALL findings are validated against current documentation, not stale training data.

**Before invoking agents, count files to ensure accurate reporting and determine review mode:**

```bash
# For staged changes
FILE_COUNT=$(git diff --staged --name-only | wc -l)

# For PR changes
FILE_COUNT=$(git diff --name-only main...HEAD | wc -l)

# For specific paths - use Glob and count results
```

**Report this count and apply thresholds:**

- **1-49 files**: Standard single-agent review (unless `--consensus` specified)
- **50+ files**: Auto-enable consensus mode (unless `--no-consensus` specified)
- **100+ files**: Warn user and suggest breaking into smaller chunks

**Use the code-reviewer agent to perform systematic code quality analysis.**

The code-reviewer agent provides:

- **Tiered progressive disclosure** for token optimization
- **Security analysis** (OWASP top 10, secrets detection)
- **Performance review** (N+1 queries, resource leaks)
- **Code quality** (SOLID, DRY, clean code principles)
- **CLAUDE.md compliance** for Claude Code ecosystem files
- **Severity levels** (CRITICAL/MAJOR/MINOR)
- **MCP validation** for current best practices and version accuracy (perplexity, context7, microsoft-learn)

**Invoke the agent based on the arguments provided:**

```text
$ARGUMENTS

Based on the arguments, determine the review scope:

If 'staged' or no arguments:
  - Review currently staged files (git diff --staged)
  - Good for pre-commit review

If 'pr' or 'pull-request':
  - Review all changes in the current PR/branch vs main
  - Good for PR review

If specific file paths provided:
  - Review those specific files
  - Can be globs like "src/**/*.ts"

If '--parallel':
  - Run multiple code-reviewer agents in parallel (one per file/module)
  - Good for large changesets
  - ⚠️ CAUTION: Limit to 2-3 Opus agents at a time to prevent context exhaustion

If '--sequential':
  - Run single code-reviewer agent reviewing all files
  - Good for smaller changesets or when context between files matters

If '--batched':
  - Run agents in batches of 2-3, waiting for completion between batches
  - Good for very large changesets (50+ files) or multi-type audits
  - Prevents context exhaustion on large workloads

If '--consensus':
  - Force multi-agent consensus mode (even for small changesets)
  - Launches 3 specialized agents: code-reviewer, security-reviewer, quality-reviewer
  - Consolidates findings based on agreement level
  - Good for critical code paths requiring extra validation

If '--no-consensus':
  - Disable auto-consensus mode (even for 50+ files)
  - Forces single code-reviewer agent
  - Good when you want faster review over higher accuracy

If '--show-rules':
  - Display active rules without running review
  - Shows: config source, tech stack, excluded rules, severity overrides, custom checks
  - Good for verifying configuration before review

If '--ignore-repo-config':
  - Skip .claude/code-review.md and CLAUDE.md configuration
  - Use default rules only (Tier 1 universal checks)
  - Good for comparing with/without repo-specific rules

If '--baseline <branch>' (e.g., '--baseline main', '--baseline develop'):
  - Compare changes against specified branch to identify NEW vs PRE-EXISTING issues
  - Run `git diff <baseline>...HEAD` to get changed line ranges
  - Tag each finding as "new" (line added/modified in this changeset) or "pre-existing"
  - Output separates new issues (prominent) from pre-existing debt (collapsed)
  - Default baseline: 'main' when 'pr' scope is used, none for 'staged'
  - GAME CHANGER for adoption: Teams only see issues THEY introduced, not inherited debt
  - Quality gates (--fail-on-new-critical) apply only to NEW issues

If '--profile <name>':
  - Run focused review using predefined check subsets
  - Profiles:
    - security: OWASP, secrets, auth checks only (fast security scan)
    - quick: Style, obvious issues only (pre-commit fast check)
    - thorough: All checks (default - full review)
    - performance: N+1, complexity, memory, concurrency checks
    - strict: thorough + pattern compliance, module boundaries, dependency migration
  - Good for focused reviews or faster pre-commit checks
  - See SKILL.md profiles section for tier mappings

If '--profile strict':
  - Runs all 'thorough' checks PLUS additional strictness:
    - Pattern compliance: architectural patterns, DI patterns, naming conventions
    - Import hygiene: module boundary violations, barrel file pollution
    - Test correlation: orphaned tests detection
    - Dependency safety: migration verification for major version bumps
  - Higher token cost (~17,000 tokens) but catches more subtle issues
  - Good for critical codebases, library releases, or comprehensive audits

If '--history':
  - Force git history analysis even for profiles that skip it (e.g., quick)
  - Analyzes:
    - Coupling: Files that typically change together
    - Hot spots: High-churn files that need extra scrutiny
    - Author context: Ownership patterns and bus factor
    - Recent patterns: Bug fix history indicating fragile areas
  - Good for investigating unfamiliar code areas or deep reviews
  - Adds ~1,500 tokens for history context analysis

If '--no-history':
  - Skip git history analysis even for profiles that include it (thorough, strict)
  - Saves time when history context isn't needed
  - Good for quick reviews of isolated, well-understood changes
  - Saves ~1,500 tokens

Default: staged changes, sequential mode, auto-consensus for 50+ files, history per profile
```

### Repository Configuration

The review command automatically loads repository-specific rules from:

1. **`.claude/code-review.md`** (primary) - Dedicated config file in .claude folder
2. **CLAUDE.md + @imports** (fallback) - Extracts rules from existing project instructions

**Configuration Priority Chain:**

```text
1. .claude/code-review.md exists?
   └── Yes: Parse config, apply rules
   └── No: Continue to fallback

2. CLAUDE.md exists?
   └── Yes: Read + follow @imports, extract rules
   └── No: Continue to defaults

3. No config found (interactive mode):
   └── AskUserQuestion: "No review configuration found. Review with default rules?"
   └── User can confirm or provide guidance
```

**What Gets Configured:**

| Section | Effect |
| --- | --- |
| `## Tech Stack` | Override auto-detected tech, improve MCP query accuracy |
| `## Exclude Rules` | Skip specific rules (e.g., `sql-injection` for non-DB projects) |
| `## Severity Overrides` | Change default severity (CRITICAL→MAJOR, MAJOR→MINOR) |
| `## Custom Checks` | Add project-specific validation (file patterns, content rules) |

See [repo-config.md](../skills/code-reviewing/references/tier-4/repo-config.md) for full schema documentation.

## Claude Code Component Validation (MANDATORY)

**CRITICAL: When Claude Code files are detected in the review scope, specialized validation via auditor agents is MANDATORY. This is NOT optional - if validation cannot run, the review FAILS.**

Claude Code components (skills, agents, commands, hooks, memory files, plugins, etc.) require validation against official documentation via the `docs-management` skill. The code-reviewer agent performs only basic syntax checks - comprehensive validation requires specialized auditor agents.

### Why Auditors Are Required

The code-reviewer agent:

- Lacks `Task` tool access (cannot spawn subagents)
- Can only perform basic syntax validation (YAML/JSON structure)
- Cannot validate frontmatter fields against official documentation
- Cannot validate tool configurations, hook events, or permission modes

The auditor agents:

- Delegate to development skills (`skill-development`, etc.)
- Development skills delegate to `docs-management` for official documentation
- Provide 100% docs-driven validation
- Catch invalid frontmatter properties, incorrect field values, etc.

### CC Detection (Step 0c - Before Code Reviewer)

Before invoking the code-reviewer agent, scan the review scope for Claude Code files:

**Detection Patterns:**

| File Pattern | Component Type |
| --- | --- |
| `.claude/agents/**/*.md`, `agents/*.md` | Agent |
| `.claude/skills/**`, `skills/*/SKILL.md` | Skill |
| `.claude/hooks/**`, `hooks.json` | Hook |
| `.claude/skills/**` | Skill |
| `CLAUDE.md`, `.claude/memory/**/*.md` | Memory |
| `output-styles/*.md` | Output Style |
| `.mcp.json`, `mcp.json` | MCP Config |
| `settings.json`, `.claude/settings.json`, `.claude/settings.local.json` | Settings |
| `plugin.json`, `.claude-plugin/**` | Plugin |
| Status line scripts | Status Line |

**Workflow:**

1. Get list of files in review scope (staged, PR, or specified paths)
2. Match against detection patterns using Glob
3. Group detected files by component type
4. Store list for post-code-reviewer auditor invocation

### CC Auditor Invocation (Step 8 - After Code Reviewer)

If CC files were detected in Step 0c, invoke specialized auditors:

#### Step 8a: Check Plugin Availability

Attempt to spawn one auditor agent (e.g., `claude-ecosystem:skill-auditor`):

- If spawn succeeds: `claude-ecosystem` plugin is installed, proceed to Step 8b
- If spawn fails: **FAIL THE REVIEW** with error message (see below)

#### Step 8b: Spawn Auditors in Priority-Ordered Batches

To prevent context exhaustion, spawn auditors in batches of 2-3, waiting between batches:

| Priority | Auditors | Rationale |
| --- | --- | --- |
| Round 1 | `memory-component-auditor`, `skill-auditor` | Memory affects all; skills define behavior |
| Round 2 | `agent-auditor`, `hook-auditor` | Agents use skills; hooks define runtime behavior |
| Round 3 | `mcp-auditor`, `settings-auditor` | Runtime configuration |
| Round 4 | `plugin-component-auditor`, `output-style-auditor`, `statusline-auditor` | Packaging and display |

Only spawn auditors for component types that were detected. For example, if no hooks were changed, skip `hook-auditor`.

#### Step 8c: Provide Auditor Context

For each auditor, provide:

- `project_root`: Absolute path to repository root
- `files`: List of files to audit (filtered to component type)
- `source`: "project" or "plugin:{plugin-name}"

Auditors write dual output:

- JSON file: `.claude/temp/audit-{source}-{component-name}.json`
- Markdown report: `.claude/temp/audit-{source}-{component-name}.md`

#### Step 8d: Integrate Findings

Map auditor scores to review severity:

| Auditor Score | Auditor Result | Review Severity |
| --- | --- | --- |
| < 70 | FAIL | CRITICAL |
| 70-84 | PASS WITH WARNINGS | MAJOR |
| 85+ with issues | PASS | MINOR |

Include auditor findings in the review report with source attribution:

```markdown
### [Finding Title]
**File**: `path/to/file.md:line`
**Severity**: CRITICAL
**Category**: Claude Code Compliance
**Source**: claude-ecosystem:skill-auditor

**Problem**: YAML frontmatter contains unsupported field 'color'

**Impact**: Invalid frontmatter may cause skill loading failures or undefined behavior

**Fix**: Remove the 'color' field - only 'name', 'description', and 'allowed-tools' are valid for skills
```

### Plugin Unavailable - REVIEW FAILS

If CC files are detected but the `claude-ecosystem` plugin is not installed:

```markdown
## REVIEW FAILED: Claude Code Validation Required

**Error:** Claude Code ecosystem files were detected in the review scope, but the `claude-ecosystem` plugin is not installed. CC validation is MANDATORY.

**Files Detected:**
| Component Type | Count | Files |
| --- | --- | --- |
| [type] | [count] | [file list] |

**Why This Is Required:**
Claude Code files require specialized validation against official documentation. The code-reviewer agent can only perform basic syntax checks (YAML/JSON structure), which is insufficient. Auditor agents validate:
- YAML frontmatter field names and values
- Tool configurations and restrictions
- Hook events and matcher patterns
- Permission modes and model selection
- Official documentation compliance

**Resolution:**
Install the claude-ecosystem plugin:

/plugin install claude-ecosystem@claude-code-plugins

Then re-run the review:

/code-quality:review [original-args]
```

### CC Validation Output Section

When CC files are reviewed, add to the review report:

```markdown
## Claude Code Validation Summary

**CC Files Detected**: [count]
**Auditors Invoked**: [list]
**Validation Status**: PASS | FAIL

### CC Findings by Component

#### Skills ([count] issues)
| File | Issue | Severity |
| --- | --- | --- |
| [file] | [issue] | [severity] |

#### Agents ([count] issues)
...

### Auditor Sources
- skill-auditor: 2 files audited, 1 issue
- agent-auditor: 3 files audited, 0 issues
```

## CRITICAL: Context Exhaustion Prevention

When reviewing large changesets or running multiple specialized agents (audit agents, code-reviewers, etc.), follow these rules to prevent context exhaustion:

### File Count Thresholds

| Files to Review | Recommended Mode | Consensus |
| --- | --- | --- |
| 1-20 files | Sequential or 2-3 parallel agents | Optional (use `--consensus` if needed) |
| 20-50 files | Batched mode (2-3 agents per batch) | Optional |
| 50-100 files | Sequential mode + consensus | Auto-enabled |
| 100+ files | Warn user, suggest smaller chunks | Auto-enabled if proceeding |

### Multi-Agent Audits (Plugin Auditing)

When invoking multiple specialized audit agents (plugin-component-auditor, skill-auditor, agent-auditor, output-style-auditor):

1. **NEVER launch 5+ Opus agents in parallel** - this exhausts context
2. **Batch into groups of 2-3 agents** with explicit waits between batches
3. **Consider Haiku for simpler audits** to reduce token overhead
4. **Use sequential mode** if unsure

### Example: Auditing 100+ plugin files across 5 component types

```text
Round 1: Launch plugin-component-auditor + skill-auditor (parallel, 2 agents)
         Wait for completion
Round 2: Launch agent-auditor + hook-auditor (parallel, 2 agents)
         Wait for completion
Round 3: Launch output-style-auditor (sequential, 1 agent)
         Wait for completion
Aggregate results and report
```

### Recovery from Context Exhaustion

If you encounter "Context low" + "/compact fails":

1. Stop launching new agents
2. Note what was completed vs pending
3. Suggest user runs /clear and resumes with remaining items
4. Do NOT keep retrying - context is exhausted

See `.claude/memory/agent-usage-patterns.md` for complete batching guidance.

## Examples

### Review Staged Changes

```text
/code-quality:review
/code-quality:review staged
```

### Review PR Changes

```text
/code-quality:review pr
```

### Review Specific Files

```text
/code-quality:review src/auth.ts src/config.ts
/code-quality:review "src/**/*.ts"
```

### Parallel Review (Large Changesets)

```text
/code-quality:review --parallel
/code-quality:review pr --parallel
```

### Batched Review (Very Large Changesets)

```text
/code-quality:review --batched
/code-quality:review staged --batched
```

### Consensus Review (Multiple Reviewers)

```text
/code-quality:review --consensus
/code-quality:review pr --consensus
```

### Skip Auto-Consensus (Large Changesets, Single Reviewer)

```text
/code-quality:review pr --no-consensus
```

### Baseline Mode (Show Only New Issues)

```text
/code-quality:review pr --baseline main
/code-quality:review staged --baseline develop
/code-quality:review --baseline main  # Compare all files vs main
```

### Profile-Based Reviews

```text
/code-quality:review --profile security    # Security checks only (fast)
/code-quality:review --profile quick       # Pre-commit fast check
/code-quality:review --profile performance # Performance-focused
/code-quality:review pr --profile thorough # Full review (default)
/code-quality:review pr --profile strict   # Comprehensive audit (pattern + module + dependency checks)
```

### Combined Flags

```text
/code-quality:review pr --baseline main --profile security  # Only new security issues
/code-quality:review staged --profile quick                 # Fast pre-commit check
/code-quality:review pr --baseline main --profile strict    # Comprehensive audit for new code only
```

## Consensus Mode

When consensus mode is active (auto-enabled for 50+ files or via `--consensus`), the command launches 3 specialized reviewers in parallel:

| Agent | Focus Areas |
| --- | --- |
| `code-reviewer` | Logic, design, maintainability, general quality |
| `security-reviewer` | OWASP top 10, secrets, auth, crypto, injection |
| `quality-reviewer` | SOLID, clean code, performance, resource leaks |

### Configuration in Consensus Mode

Each consensus agent independently loads repository configuration (Step 0b from the
code-reviewing skill). All 3 agents read the same `.claude/code-review.md` (or CLAUDE.md
fallback), and `.claude/rules/*.md` content is auto-loaded into all agent contexts by the
runtime. Excluded rules, severity overrides, and custom checks apply uniformly.

### Consensus Consolidation

Findings are consolidated based on agreement level:

| Agreement | Confidence | Severity Mapping |
| --- | --- | --- |
| 3 agents agree | HIGH | As flagged (CRITICAL/MAJOR/MINOR) |
| 2 agents agree | MEDIUM | Downgrade by one level if flagged as CRITICAL |
| 1 agent flags | LOW | Mark as "Needs Human Review" - may be false positive |

### Consensus Output Format

```markdown
## Consensus Review Summary
**Files Reviewed**: [Count]
**Review Mode**: Consensus (3 agents)
**Issues Found**: [CRITICAL: X | MAJOR: Y | MINOR: Z | NEEDS REVIEW: W]

## High Confidence Issues (3/3 agents agree)
[Details]

## Medium Confidence Issues (2/3 agents agree)
[Details]

## Low Confidence Issues (1/3 agents - may be false positive)
[Details]
```

## Output Format

The agent returns a structured review report:

```markdown
## Technology Research Summary (from Research Phase)
### Detected Stack
| Category | Technology | Version | Source |
| --- | --- | --- | --- |
| [Category] | [Tech] | [Version] | [Detected from] |

### Current Best Practices (MCP-Validated)
| Area | Current Recommendation | Source |
| --- | --- | --- |
| [Area] | [Recommendation] | [mcp-server] |

## Review Summary
**Files Reviewed**: [Count]
**Issues Found**: [CRITICAL: X | MAJOR: Y | MINOR: Z]
**Overall Assessment**: [PASS/CONCERNS/FAIL]

## Critical Issues
[Details with file:line, problem, impact, fix, **Validated**: status]

## Major Issues
[Details with validation status]

## Minor Issues
[Details with validation status]

## Positive Observations
[Good patterns noted]

## MCP Validation Summary
**Sources Consulted**: [count]
**Findings Validated**: [validated/total]
**Corrections Applied**: [count]
**Outdated Patterns Detected**: [count]

### Sources
- [perplexity]: [Query or source description]
- [microsoft-learn]: [Doc title and URL if available]
- [context7]: [Library name and version]
```

### Baseline Mode Output Format

When `--baseline` is specified, the output separates new issues from pre-existing debt:

```markdown
## Review Summary

**Baseline**: `main` (comparing HEAD against main)
**Files Changed**: [Count]

**New Issues (this changeset)**: 2 CRITICAL, 1 MAJOR, 3 MINOR ← FOCUS HERE
**Pre-existing Issues**: 47 (collapsed below)

**Overall Assessment**: [PASS/CONCERNS/FAIL] (based on NEW issues only)

## New Issues Introduced

These issues were introduced in this changeset. Address before merging.

### CRITICAL (New)

#### [Finding Title]
**File**: `src/api/users.ts:42` (line added in this changeset)
**Attribution**: NEW - line 42 added in commit abc123
[... full finding details ...]

### MAJOR (New)
[... findings ...]

### MINOR (New)
[... findings ...]

---

<details>
<summary>Pre-existing Issues (47) - Technical Debt (click to expand)</summary>

These issues existed before this changeset. Not blocking, but noted for awareness.

### CRITICAL (Pre-existing): 5
[... collapsed findings ...]

### MAJOR (Pre-existing): 20
[... collapsed findings ...]

### MINOR (Pre-existing): 22
[... collapsed findings ...]

</details>
```

### Quality Gate Support (CI/CD Integration)

When using `--baseline` with quality gates:

- `--fail-on-new-critical` - Exit code 1 if ANY new CRITICAL issues found
- `--fail-on-new-major` - Exit code 1 if ANY new MAJOR issues found

**Note:** Quality gates apply ONLY to new issues. Pre-existing debt does not fail the build.

## Command Design Notes

This command delegates to the code-reviewer agent, which uses the code-quality:code-reviewing skill as its authoritative source. The skill provides tiered checklists and repository-specific rules. This separation keeps the command simple while leveraging comprehensive review logic.

### Read-Only by Design

The code-reviewer agent operates in `plan` mode (read-only). It analyzes code and reports findings but does not apply fixes. This is intentional:

- **Review and fix are separate concerns**: Review identifies issues; fixing is a distinct action
- **User control**: Developers decide which fixes to apply and how
- **Safety**: No accidental modifications during review

### Future: Fix Mode

If a `--fix` flag is added in the future to auto-apply fixes:

1. Use a separate agent with Edit capability (not plan mode)
2. **Default: Leave fixes unstaged** for user review
3. Support `--auto-stage` flag to stage fixes after verification
4. Follow the pattern in [fix-workflow.md](../skills/code-reviewing/references/fix-workflow.md)

Example future usage:

```bash
# Review and apply fixes, leave unstaged (default)
/code-quality:review staged --fix

# Review, apply fixes, and stage (for automation)
/code-quality:review staged --fix --auto-stage
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
