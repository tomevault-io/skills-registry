---
name: gh-actions-debug
description: Debug and investigate GitHub Actions workflow failures using gh CLI. Use when workflows are failing or when you need to understand CI/CD issues. Use when this capability is needed.
metadata:
  author: kamijin-fanta
---

# GitHub Actions Debug Skill

This skill helps investigate and debug GitHub Actions workflow failures using the GitHub CLI (`gh`).

## When to Use

- GitHub Actions workflows are failing
- Need to understand why CI/CD is not working
- Investigating test failures in CI
- Analyzing build or deployment issues
- Checking workflow run history

## Task

Systematically investigate GitHub Actions issues using the following workflow:

### 1. List Recent Workflow Runs

Start by getting an overview of recent runs:

```bash
# List last 10 workflow runs
gh run list --limit 10

# List runs for a specific workflow
gh run list --workflow=test.yml --limit 10

# List only failed runs
gh run list --status=failure --limit 10

# List runs on a specific branch
gh run list --branch=main --limit 10
```

### 2. View Run Summary

Get detailed information about a specific run:

```bash
# View run summary by ID
gh run view <RUN_ID>

# View the most recent run for a workflow
gh run view --workflow=test.yml
```

The summary includes:
- Overall status (success, failure, cancelled)
- Jobs and their status
- Annotations and errors
- Trigger event and branch

### 3. View Failed Job Logs

Get detailed logs from failed jobs:

```bash
# View logs of failed steps only
gh run view <RUN_ID> --log-failed

# View all logs for a run
gh run view <RUN_ID> --log

# View logs for a specific job
gh run view <RUN_ID> --job=<JOB_ID> --log
```

### 4. Common Failure Patterns to Look For

When analyzing logs, look for:

#### Configuration Errors
- Invalid YAML syntax
- Unknown workflow triggers
- Invalid action versions
- Missing or invalid parameters

**Example:**
```
jsonschema: "output" does not validate
additional properties 'format' not allowed
```
→ Check for deprecated or invalid configuration options

#### Dependency Issues
- Package installation failures
- Version mismatches
- Cache restoration failures
- Missing dependencies

**Example:**
```
Failed to restore: "/usr/bin/tar" failed with error
```
→ Cache corruption; may need to clear cache or update cache keys

#### Test Failures
- Unit test failures
- Integration test timeouts
- Race conditions
- Environment-specific issues

#### Build Errors
- Compilation errors
- Linter failures
- Type errors
- Import errors

#### Permission Issues
- Authentication failures
- Missing secrets
- Insufficient permissions
- Token expiration

### 5. Investigation Checklist

- [ ] Identify the failing workflow and job
- [ ] Check the trigger event (push, PR, schedule)
- [ ] Review the error messages and annotations
- [ ] Compare with successful runs to identify changes
- [ ] Check for configuration file changes
- [ ] Verify dependency versions
- [ ] Look for environment-specific issues
- [ ] Check secrets and permissions

### 6. Common Fixes

#### Update Workflow Triggers

Add or modify trigger events:

```yaml
on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
```

#### Fix Configuration Errors

Remove deprecated options or update to new syntax:

```yaml
# Old (may not work with new versions)
output:
  format: colored-line-number
  print-issued-lines: true

# Fixed (remove unsupported options)
# (many tools use their own config or defaults)
```

#### Clear or Update Cache

Add cache versioning:

```yaml
- uses: actions/cache@v4
  with:
    path: ~/go/pkg/mod
    key: ${{ runner.os }}-go-${{ hashFiles('**/go.sum') }}-v2  # Increment version
```

#### Update Action Versions

Use specific versions instead of `@master`:

```yaml
# Before
- uses: some-action@master

# After
- uses: some-action@v3
```

#### Fix Permissions

Add required permissions:

```yaml
permissions:
  contents: read
  pull-requests: write
  checks: write  # Add if needed
```

## Output Format

Provide a structured analysis:

1. **Issue Summary**: Brief description of the failure
2. **Root Cause**: What caused the failure
3. **Affected Workflows**: Which workflows/jobs are impacted
4. **Fix Applied**: What changes were made
5. **Verification**: How to verify the fix works

For each issue found, include:
- **Location**: Workflow file and line number
- **Error Message**: Exact error from logs
- **Explanation**: Why this error occurred
- **Solution**: How to fix it
- **Prevention**: How to avoid similar issues

## Useful Commands Reference

```bash
# List all workflows
gh workflow list

# View workflow definition
gh workflow view <WORKFLOW_NAME>

# Manually trigger a workflow
gh workflow run <WORKFLOW_NAME>

# Watch a running workflow
gh run watch <RUN_ID>

# Download artifacts from a run
gh run download <RUN_ID>

# Re-run a failed workflow
gh run rerun <RUN_ID>

# Re-run only failed jobs
gh run rerun <RUN_ID> --failed

# Cancel a running workflow
gh run cancel <RUN_ID>

# Delete a workflow run
gh run delete <RUN_ID>
```

## Tips

1. **Start broad, then narrow**: List runs → View summary → Check failed logs
2. **Compare with successful runs**: Look for what changed
3. **Check recent commits**: Workflow file changes often cause issues
4. **Verify locally first**: Run tests locally before pushing fixes
5. **Use workflow dispatch**: Test workflow changes with manual triggers
6. **Check GitHub Status**: Sometimes issues are platform-wide
7. **Review action versions**: Breaking changes in actions can cause failures

## Example Investigation Workflow

```bash
# 1. Check recent failures
gh run list --status=failure --limit 5

# 2. Get details on the latest failure
gh run view <RUN_ID>

# 3. View only failed job logs
gh run view <RUN_ID> --log-failed

# 4. Identify the issue (e.g., config error in .golangci.yml)

# 5. Fix the issue locally

# 6. Verify the fix
go run github.com/golangci/golangci-lint/cmd/golangci-lint@latest config verify

# 7. Commit and push

# 8. Monitor the new run
gh run watch
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kamijin-fanta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
