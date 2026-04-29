---
name: analyze-ci
description: Analyze failed GitHub Action jobs for a pull request. Use when this capability is needed.
metadata:
  author: benchflow-ai
---

# Analyze CI Failures

This skill analyzes logs from failed GitHub Action jobs using Claude.

## Prerequisites

- **GitHub Token**: Auto-detected via `gh auth token`, or set `GITHUB_TOKEN` env var

## Usage

```bash
# Analyze all failed jobs in a PR
uv run skills analyze-ci <pr_url>

# Analyze specific job URLs directly
uv run skills analyze-ci <job_url> [job_url ...]

# Show debug info (tokens and costs)
uv run skills analyze-ci <pr_url> --debug
```

Output: A concise failure summary with root cause, error messages, test names, and relevant log snippets.

## Examples

```bash
# Analyze CI failures for a PR
uv run skills analyze-ci https://github.com/mlflow/mlflow/pull/19601

# Analyze specific job URLs directly
uv run skills analyze-ci https://github.com/mlflow/mlflow/actions/runs/12345/job/67890
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/benchflow-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
