---
name: github-actions-failure-debugging
description: Guide for debugging failing GitHub Actions workflows. Use this when asked to debug failing GitHub Actions workflows. Use when this capability is needed.
metadata:
  author: rabithua
---

To debug failing GitHub Actions workflows in a pull request, follow this process, using tools provided from the GitHub MCP Server:

1. Use the `list_workflow_runs` tool to look up recent workflow runs for the pull request and their status
2. Use the `summarize_job_log_failures` tool to get an AI summary of the logs for failed jobs, to understand what went wrong without filling your context windows with thousands of lines of logs
3. If you still need more information, use the `get_job_logs` or `get_workflow_run_logs` tool to get the full, detailed failure logs
4. Try to reproduce the failure yourself in your own environment.
5. Fix the failing build. If you were able to reproduce the failure yourself, make sure it is fixed before committing your changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rabithua) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
