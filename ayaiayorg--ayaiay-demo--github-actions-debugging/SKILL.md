---
name: github-actions-debugging
description: Guide for debugging failing GitHub Actions workflows. Use this when asked to debug failing GitHub Actions workflows. Use when this capability is needed.
metadata:
  author: ayaiayorg
---

# GitHub Actions Debugging Skill

This skill provides a systematic approach to debugging failing GitHub Actions workflows in pull requests.

## When to Use This Skill

- When a GitHub Actions workflow fails in a pull request
- When investigating CI/CD pipeline failures
- When asked to analyze workflow run errors
- When troubleshooting build, test, or deployment issues

## Debugging Process

### 1. Identify Failed Workflows

Use the `list_workflow_runs` tool to look up recent workflow runs for the pull request and their status:

```
list_workflow_runs(owner="owner", repo="repo", resource_id="workflow_file.yml")
```

This will show:
- Workflow run status (success, failure, cancelled)
- Trigger event and branch
- Run timestamps
- Run IDs for further investigation

### 2. Get AI-Powered Failure Summary

Use the `summarize_job_log_failures` tool to get an AI summary of the logs for failed jobs:

```
summarize_job_log_failures(owner="owner", repo="repo", run_id=12345)
```

This provides:
- Concise summary of what went wrong
- Key error messages
- Affected jobs and steps
- No context window bloat from thousands of log lines

### 3. Deep Dive Into Logs (If Needed)

If you need more detailed information, use these tools:

**For specific job logs:**
```
get_job_logs(owner="owner", repo="repo", job_id=67890)
```

**For complete workflow run logs:**
```
get_workflow_run_logs(owner="owner", repo="repo", run_id=12345)
```

### 4. Reproduce Locally

Try to reproduce the failure in your own environment:
- Check out the failing branch
- Run the same commands locally
- Compare environment differences
- Look for platform-specific issues

### 5. Fix and Verify

Once you understand the issue:
1. Make the necessary code changes
2. If you reproduced locally, verify the fix works
3. Commit your changes
4. Monitor the workflow run to ensure it passes

## Common Failure Patterns

### Build Failures
- Missing dependencies
- Compilation errors
- Configuration issues
- Environment variable problems

### Test Failures
- Flaky tests
- Test environment setup issues
- Race conditions
- Missing test data

### Deployment Failures
- Authentication/permission issues
- Resource unavailability
- Configuration mismatches
- Network connectivity problems

## Best Practices

1. **Start with the summary** - Don't load full logs until necessary
2. **Check recent changes** - Compare with previous successful runs
3. **Look for patterns** - Similar failures across multiple jobs
4. **Check workflow file** - YAML syntax and configuration errors
5. **Verify secrets** - Ensure all required secrets are configured
6. **Test locally first** - Validate fixes before pushing

## Tools Reference

- `list_workflow_runs` - List recent workflow runs and their status
- `summarize_job_log_failures` - Get AI summary of failed job logs
- `get_job_logs` - Get detailed logs for a specific job
- `get_workflow_run_logs` - Get complete workflow run logs
- `list_workflow_jobs` - List all jobs in a workflow run
- `get_workflow_run_usage` - Get timing and billable minutes

## Example Workflow

```
1. User reports: "The CI is failing on PR #123"
2. List workflows: list_workflow_runs to find failed runs
3. Summarize failures: summarize_job_log_failures for quick diagnosis
4. If needed, get detailed logs: get_job_logs for specific failures
5. Reproduce issue locally and test fix
6. Commit fix with clear message explaining the issue
7. Monitor new workflow run to confirm fix
```

Remember: Always start with high-level summaries before diving into detailed logs to keep your context efficient.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayaiayorg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
