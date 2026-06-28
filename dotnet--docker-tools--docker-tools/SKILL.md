---
name: investigating-pull-request
description: >- Use when this capability is needed.
metadata:
  author: dotnet
---

# Investigating GitHub pull requests

This document contains useful patterns for inspecting a single GitHub pull request.

## How to view pull request context

Use `Show-PullRequestComments.ps1` to view all pull request comments, review
decisions, issue comments, review submissions, and inline review comments.

```shell
pwsh ./eng/docker-tools/skill-helpers/Show-PullRequestComments.ps1 $prNumber [-Repo "$owner/$repo"]
```

## How to view pull request checks and logs

Use `Show-PullRequestBuilds.ps1` to view the GitHub status check summary,
including a detailed view of Azure Pipelines build checks.

```shell
pwsh ./eng/docker-tools/skill-helpers/Show-PullRequestBuilds.ps1 -PullRequest $prNumber [-Repo "$owner/$repo"]
```

## How to read Azure Pipelines logs

First, get the log ID from the build timeline.
Then, use `Get-BuildLog.ps1` to print the full log:

```shell
pwsh ./eng/docker-tools/skill-helpers/Get-BuildLog.ps1 -Organization dnceng-public -Project public -BuildId $buildId -LogId $logId
```

---
> Source: [dotnet/docker-tools](https://github.com/dotnet/docker-tools) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-28 -->
