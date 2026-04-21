---
name: 17th-make-triager
description: | Use when this capability is needed.
metadata:
  author: seventeenthearth
---

# Make Triager

Delegate make commands to GLM-5 worker for execution and intelligent failure triage.

## Prerequisites

Configure `glm-worker` MCP in Claude Code's mcp.json:

```json
{
  "mcpServers": {
    "glm-worker": {
      "command": "uv",
      "args": ["run", "--directory", "/path/to/glm-worker-mcp", "python", "server.py"],
      "env": {
        "ZHIPU_API_KEY": "your-api-key"
      }
    }
  }
}
```

## Workflow

1. Receive make command request from user
2. Get current git branch name
3. Call `delegate_to_glm` with branch name and triager prompt template
4. GLM executes command and analyzes failures if any
5. GLM writes failure report to `.kkachi/{branch_name}/completed/`
6. Return report path to main agent for detailed analysis

## Prompt Template

Use `delegate_to_glm` with this template. Replace `{branch_name}` with current git branch:

```
delegate_to_glm(
    task="""
Execute: {make_command}

Always write report to: .kkachi/{branch_name}/completed/make-triager-{epoch_timestamp}.md
- Use current Unix epoch timestamp in seconds for {epoch_timestamp}

On success:
- Write success report
- Return "✅ {make_command} passed. Report: {report_path}"

On failure:
- Extract ALL failed items (DO NOT truncate - list every single failure)
- Group failures by root cause/error pattern
- Identify if multiple failures share the same underlying issue
- Write comprehensive failure report
- Return "❌ {make_command} failed. {N} failures. Report: {report_path}"

Success report format:
# Success: {command}

**Timestamp**: {iso_timestamp}
**Exit Code**: 0

✅ All checks passed.

Failure report format:
# Failure Report: {command}

**Timestamp**: {iso_timestamp}
**Exit Code**: {exit_code}
**Total Failures**: {count}

## Summary
(1-2 sentence overview: what failed, common patterns if any)

## Failures by Root Cause

### Root Cause 1: {description}
Affected: {N} items
- item1 (file:line)
- item2 (file:line)

Error pattern:
```
{common error message}
```

### Root Cause 2: {description}
(repeat for each distinct root cause)

## All Failed Items (Complete List)
1. item1 (file:line) - [Root Cause 1]
2. item2 (file:line) - [Root Cause 1]
3. item3 (file:line) - [Root Cause 2]
(list ALL failures, no truncation)

## Suggested Actions
1. Fix for Root Cause 1: {specific action}
2. Fix for Root Cause 2: {specific action}
""",
    working_dir="{project_root}"
)
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{make_command}` | Full make command | `make test-unit` |
| `{branch_name}` | Current git branch | `feature/task-177` |
| `{epoch_timestamp}` | Unix epoch seconds | `1737612345` |
| `{project_root}` | Project root path | `/home/user/myproject` |

## Examples

### Single Command

```
delegate_to_glm(
    task="""
Execute: make lint

Always write report to .kkachi/feature/task-177/completed/make-triager-{epoch}.md

On success: Return "✅ make lint passed. Report: <path>"
On failure: Extract errors with file:line, return "❌ make lint failed. Report: <path>"
""",
    working_dir="/home/user/project"
)
```

### Multiple Commands

```
delegate_to_glm(
    task="""
Execute in order, stop on first failure:
1. make lint
2. make test-unit
3. make test-integration

Write report to .kkachi/feature/task-177/completed/make-triager-{epoch}.md

Return:
- All passed: "✅ All checks passed. Report: {path}"
- Any failed: "❌ {failed_cmd} failed. Report: {path}"
""",
    working_dir="/home/user/project"
)
```

## Post-Triage Actions

After receiving a report path from GLM:

1. Read the report file
2. If success: acknowledge and proceed
3. If failure:
   - Analyze failures in context of the codebase
   - Propose specific fixes with code changes
   - Optionally re-run failed tests after fixes

## Output Directory

Reports saved to `.kkachi/{branch_name}/completed/` in project root. Add to `.gitignore`:

```
.kkachi/
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seventeenthearth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
